---
title: 3DPDocker实施部署原理
date: 2025-09-01 17:08:57
tags:
---


# 3DP Docker实施部署原理

## 一、docker安装脚本解析

```bash
tar xzvf docker-20.10.15.tgz yes | cp -f ./docker/* /bin/ cp daemon.json /etc/docker/ cp docker.service /usr/lib/systemd/system/ cp docker-compose-linux-aarch64 /bin/docker-compose chmod +x /bin/docker-compose systemctl enable docker systemctl start docker.service docker network create -d bridge 3dp_net
```

### 📌 脚本解析
<!--more-->
```bash
tar xzvf docker-20.10.15.tgz
```

- 解压 `docker-20.10.15.tgz` 压缩包，里面通常包含 `docker` 的二进制文件。

```bash
yes | cp -f ./docker/* /bin/
```

- 将解压出来的 `docker/*` 文件强制复制到 `/bin/` 目录下。

- `yes |` 是自动回答 `y`，避免覆盖提示。

- 这样 `docker`、`dockerd` 等可执行文件会放到系统 PATH 中，方便直接使用。

```bash
cp daemon.json /etc/docker/
```

- 复制 `daemon.json` 配置文件到 `/etc/docker/`。

- `daemon.json` 是 Docker 守护进程的配置文件（比如镜像加速、日志驱动等）。

```bash
cp docker.service /usr/lib/systemd/system/
```

- 将 `docker.service` 复制到 `systemd` 的服务目录。

- 这样 `systemctl` 就能管理 Docker 进程（开机自启、停止、重启等）。

```bash
cp docker-compose-linux-aarch64 /bin/docker-compose
```

- 把 `docker-compose` 二进制文件拷贝到 `/bin/`，命名为 `docker-compose`。

- 文件名带 `aarch64`，说明这是 **ARM64 架构**（常见于 ARM 服务器、部分国产 CPU）的版本。

```bash
chmod +x /bin/docker-compose
```

- 给 `docker-compose` 添加执行权限。

```bash
systemctl enable docker
```

- 设置 Docker 服务 **开机自启**。

```bash
systemctl start docker.service
```

- 启动 Docker 服务。

```bash
docker network create -d bridge 3dp_net
```

- 创建一个名为 `3dp_net` 的 Docker 自定义网络，驱动为 `bridge`。

- 方便后续 `docker-compose.yml` 中的容器通过该网络互相通信。

### ✅ 总结

这段命令的完整流程是：

1. **解压 Docker 二进制包**

2. **复制可执行文件到 /bin/**

3. **安装配置文件（daemon.json, systemd service）**

4. **安装 Docker Compose**

5. **启用并启动 Docker 服务**

6. **创建容器间通信网络 3dp_net**

换句话说，这是一份 **离线部署 Docker + Compose 环境，并初始化网络的脚本**。

### docker network create -d bridge 3dp_net 创建自定义网络的意义以及后续容器间沟通的原理



这其实涉及到 **Docker 网络模型**。我帮你分两部分讲清楚：

#### 一、创建自定义网络的意义

命令：

```bash
docker network create -d bridge 3dp_net
```

- `-d bridge` → 使用 **bridge 桥接网络驱动**（默认类型）。

- `3dp_net` → 网络名字。

**为什么不直接用默认的 bridge 网络？**

1. **容器互通性更好**
- 在 Docker 默认的 `bridge` 网络中，容器只能通过 **IP 地址**互相访问。

- 在自定义 `bridge` 网络（比如 `3dp_net`）里，容器可以通过 **容器名/服务名** 来互相访问（Docker 内置 DNS 解析）。

- 举例：

有两个容器：`mysql` 和 `app`，都在 `3dp_net` 网络里

`app` 容器里可以直接用 `mysql:3306` 访问数据库，不需要写 IP。

2. **网络隔离**
- 不同的自定义网络是相互隔离的。

- 这样可以把一组服务（例如你 3DPlaza 系统的 nacos、workflow、mysql、minio）放到同一个网络，和其他无关容器区分开。
3. **便于维护**
- 多个 `docker-compose.yml` 文件可以都接入同一个 `3dp_net` 网络，实现不同 Compose 项目之间的容器通信。

#### 二、容器间通信的原理

1. **Bridge 网络机制**
- Docker 在宿主机上创建一张虚拟网桥（类似 Linux 的 `br0`）。

- 每个容器启动时会被分配一个虚拟网卡（veth pair），另一端连接到这张网桥。

- 所有加入 `3dp_net` 的容器都在同一网桥里，就像在同一个交换机下。
2. **IP 分配**
- Docker 内置一个小型的 DHCP 服务，容器启动时会分配一个私有 IP（默认 172.18.x.x 之类）。
3. **DNS 解析**
- 自定义网络中，Docker 自带一个 DNS 服务。

- 容器可以通过 **容器名** 或 **服务名** 互相解析到对方的 IP。

- 例如：

`3DP-mysql` 的 IP 可能是 `172.18.0.3`

`3DP-app` 里访问数据库时，只需要写 `mysql:3306`，Docker 会自动解析成 `172.18.0.3:3306`。

4. **跨容器通信**
- 在同一个自定义网络的容器之间，网络通信是直接透传的（类似同一个二层交换机）。

- 如果容器在不同网络，可以用 `docker network connect` 手动把一个容器加入多个网络。

#### ✅ 总结

- 创建 `3dp_net` 的意义：

容器间能通过名字互相访问（无需写死 IP）。

保持服务之间的隔离和可维护性。

方便不同 `docker-compose` 项目共享网络，形成一个逻辑内网。

- 通信原理：

Docker 在宿主机上建一张虚拟网桥。

容器通过虚拟网卡接入网桥，分配到一个私有 IP。

Docker 内置 DNS，允许容器通过 **容器名/服务名** 来互相访问。

### 二、docker commpose 配置文件解析-以minio为例

```yaml
version: '3'

services:

  3DP-minio:

    image: minio/minio:RELEASE.2023-09-07T02-05-02Z

    container_name: 3DP-minio

    restart: always

    volumes:

      - "/data/3DPlaza/minio/data:/data"

      - "/data/3DPlaza/minio/.minio:/root/.minio"

    environment:

      TZ: Asia/Shanghai

      MINIO_ACCESS_KEY: admin

      MINIO_SECRET_KEY: 3DplazaPlatform2024

    ports:

      - "9000:9000"

      - "9090:9090"

    command: server /data --console-address ":9090" -address ":9000"

    networks:

      - 3dp_net

    logging:

      options:

        max-size: "20m"

        max-file: "4"

networks:

  3dp_net:

    external: true
```



这个 `docker-compose.yml` 文件定义了一个 **MinIO** 服务容器配置。MinIO 是一个开源的对象存储服务，兼容 Amazon S3 协议，常用于私有云存储。下面我帮你逐行解释一下：

### 📌 文件结构说明

```yaml
version: '3'
```

- 使用 Docker Compose v3 文件语法。

### 📦 services 部分

#### 🔹 3DP-minio

- **image: minio/minio:RELEASE.2023-09-07T02-05-02Z**

使用的镜像是 `minio/minio`，版本号固定为 `RELEASE.2023-09-07T02-05-02Z`。

（这保证了部署的可重复性，不会随镜像更新而变化）

- **container_name: 3DP-minio**

容器名称固定为 `3DP-minio`。

- **restart: always**

容器异常退出或系统重启时会自动拉起。

- **volumes**

`/data/3DPlaza/minio/data:/data`

→ 容器的 `/data` 目录映射到宿主机的 `/data/3DPlaza/minio/data`，用于存放实际的对象数据。

`/data/3DPlaza/minio/.minio:/root/.minio`

→ 存放 MinIO 的用户配置文件、凭证等。

该配置保证以后迁移服务器时只需要将文件辅助就可以，降低迁移成本

- **environment**

`TZ=Asia/Shanghai` → 设置时区为上海时间。

`MINIO_ACCESS_KEY=admin` → MinIO 的管理账号。

`MINIO_SECRET_KEY=3DplazaPlatform2024` → MinIO 的管理密码。

⚠️ 注意：`MINIO_ACCESS_KEY` 和 `MINIO_SECRET_KEY` 这两个参数在新版本 MinIO 中已被替换为 `MINIO_ROOT_USER` 和 `MINIO_ROOT_PASSWORD`，但在你用的这个版本中仍然有效。

- **ports**

`"9000:9000"` → 映射对象存储服务 API 端口

`"9090:9090"` → 映射管理控制台端口

- **command**

```bash
server /data --console-address ":9090" -address ":9000"
```

`server /data` → 指定存储目录为 `/data`（已挂载到宿主机）。

`--console-address ":9090"` → 管理控制台监听 `9090` 端口。

`-address ":9000"` → API 服务监听 `9000` 端口。

- **networks**

加入外部网络 `3dp_net`，以便和其他容器（例如 Nacos、业务服务）通信。

- **logging**

配置日志滚动：

`max-size: "20m"` → 每个日志文件最大 20MB

`max-file: "4"` → 最多保留 4 个日志文件

### 🌐 networks 部分

```yaml
networks:

  3dp_net:

    external: true
```

- 使用外部已存在的 `3dp_net` 网络，而不是由当前 Compose 文件创建。

### ✅ 总结

这个 Compose 文件会启动一个 **MinIO 对象存储服务**，特点是：

- 对象存储数据存放在宿主机 `/data/3DPlaza/minio/data`

- 管理控制台在 `http://&lt;host&gt;:9090`

- API 接口在 `http://&lt;host&gt;:9000`

- 管理账号：`admin`

- 管理密码：`3DplazaPlatform2024`

- 容器加入 `3dp_net` 网络，方便和你的其他 3DP 服务交互





### nacos的配置

```yaml
version: '3'
services:
  3DP-nacos:
    image: nacos/nacos-server:v2.2.0
    container_name: 3DP-nacos
    restart: always
    environment:
      - TZ=Asia/Shanghai
      - MODE=standalone
      - SPRING_DATASOURCE_PLATFORM=mysql
      - MYSQL_SERVICE_HOST=200.157.100.42
      - MYSQL_SERVICE_PORT=3306
      - MYSQL_SERVICE_USER=root
      - MYSQL_SERVICE_PASSWORD=3DplazaPlatform2024
      - MYSQL_SERVICE_DB_NAME=nacos_config
      - NACOS.CORE.AUTH.ENABLED=true
    ports:
      - "8848:8848"
      - "9848:9848"
      - "9849:9849"
    networks:
      - 3dp_net
    logging:
      options:
        max-size: "20m"
        max-file: "4"
networks:
  3dp_net:
    external: true
```



### nacos服务注册与发现的原理

Nacos 的 **服务注册与发现** 原理可以拆分成 **服务注册（Provider 注册到 Nacos）** 和 **服务发现（Consumer 从 Nacos 获取 Provider 列表）** 两部分。它本质上是一个 **注册中心 + 配置中心**。下面我从原理角度详细说明：

## 1. 服务注册原理

当一个服务实例（Provider，比如用户服务 user-service）启动时，它会把自己的信息注册到 Nacos：

- **注册内容**

服务名（如 `user-service`）

IP 地址（如 `10.0.0.5`）

端口号（如 `8080`）

权重、集群名、健康检查信息

- **注册方式**

服务启动后，Nacos 客户端会调用 **Nacos Server 提供的 HTTP/GRPC API** 发送注册请求

Nacos Server 把服务信息写入内存中的注册表（Registry），并持久化到数据库（默认 Derby/MySQL）

- **心跳机制**

Provider 会定期（默认 5s）向 Nacos 发送心跳包

如果 Nacos 在一定时间（默认 30s）没收到心跳，会认为该实例下线，将其剔除

## 2. 服务发现原理

当一个消费者服务（Consumer，比如订单服务 order-service）需要调用 `user-service` 时，它的服务发现流程如下：

1. **拉取注册表**
- Consumer 启动时，会向 Nacos 请求自己依赖的服务列表（如 `user-service` 的所有可用实例）

- 客户端本地缓存一份服务列表
2. **本地负载均衡**
- Consumer 在调用时，并不会每次都去问 Nacos，而是直接从本地缓存的服务列表里选择一个实例（一般采用 **Ribbon、Spring LoadBalancer 或 Dubbo 内置策略**）
3. **服务列表更新（推拉结合）**
- Consumer 本地缓存会随着 Nacos 的变更而更新：

**推模式**：Nacos Server 通过长轮询或 gRPC 推送最新的服务列表到 Consumer

**拉模式**：Consumer 也会定期（默认 30s）去 Nacos 拉取一次更新，做兜底

## 3. 健康检查

- **临时实例（ephemeral = true）**：靠心跳维持，心跳超时就被剔除

- **持久实例（ephemeral = false）**：不依赖心跳，Nacos 不会自动剔除，需要人工下线

## 4. 高可用与一致性

- **多节点集群模式**：Nacos Server 自身也可以多节点部署

- **一致性协议**：

临时实例的注册表数据用 **AP 模式**（可用性优先，最终一致）

持久实例的数据用 **CP 模式**（一致性优先，依赖 Raft 协议）

## 总结流程图

```nginx
Provider 启动 → 注册到 Nacos → 定期发送心跳  

Consumer 启动 → 从 Nacos 拉取服务列表 → 本地缓存 → 负载均衡调用  

服务上下线 → Nacos 更新注册表 → 推送/拉取同步到 Consumer
```

### nacos配置微服务的原理

**Nacos 配置微服务的原理**，其实就是它作为 **配置中心（Configuration Center）** 的工作机制，和它的 **服务注册与发现** 部分相辅相成



### 1. 为什么要有配置中心

在微服务架构里，每个服务都需要一堆配置：

- 数据库连接（地址、用户名、密码）

- Redis、消息队列地址

- 限流、开关参数

- 不同环境（dev/test/prod）的配置差异

如果这些配置都写死在每个服务的本地文件里：

- 修改配置要 **重新打包、重启** 服务

- 多个服务要改相同配置（比如数据库地址）非常麻烦

- 不利于动态调优（比如流量控制参数）

👉 于是需要一个 **集中式配置中心**，Nacos 就扮演了这个角色。

## 2. Nacos 配置管理的核心原理

### （1）配置存储

- 配置以 **键值对（DataId, Group, Namespace）** 的形式存储在 Nacos Server 里

- 可以存放 **properties、yaml、json** 等文本格式

- 底层存储：Nacos 内存 + 数据库（默认 Derby，可切换 MySQL）

### （2）客户端获取配置

- 微服务启动时，通过 **Nacos SDK** 或 **Spring Cloud Alibaba Nacos Starter** 连接到 Nacos Server

- 客户端根据 **namespace + group + dataId** 拉取对应的配置文件

- 拉取到的配置会被注入到应用的 **Environment** 或者 Spring 的 `@Value` / `@ConfigurationProperties` 里

### （3）配置动态刷新

- Nacos 提供 **长轮询 / gRPC 推送** 机制

- 当配置在控制台被修改时，Nacos Server 会通知所有订阅该配置的客户端

- 客户端自动更新本地配置，并触发 Spring 的 `@RefreshScope` 或监听器机制，实时生效

举个例子：

```yaml
# Nacos 里配置了

spring:

  datasource:

    url: jdbc:mysql://10.0.0.5:3306/test

    username: root

    password: 123456
```

如果你在 Nacos 控制台把 `password` 改成 `654321`，客户端不用重启，下一次获取数据连接时就会用新密码。

### （4）配置隔离

- **Namespace**：环境隔离（如 dev/test/prod）

- **Group**：项目或业务隔离（如 order-service-group / user-service-group）

- **DataId**：具体配置文件（如 `application.yaml`、`db-config.yaml`）

## 3. 高可用与一致性

- 配置中心数据通过 **MySQL 存储 + Raft 协议** 保证一致性

- 多节点 Nacos 集群可以保证高可用

- 客户端会有本地缓存配置（`nacos/config` 目录），即使 Nacos 挂了，也能用上一次的配置启动

## 4. 总结工作流程

```markdown
微服务启动 → 从 Nacos 拉取配置（DataId + Group + Namespace）

         → 配置注入到应用环境（Environment）

         → 应用运行时使用这些配置



当配置在 Nacos 修改 → Nacos Server 通知客户端

                  → 客户端刷新配置（动态生效，无需重启）


```









### 以ibase server配置文件为例

```yaml
server:

  port: 30108

spring:

  application:

    name: i-base

  cloud:

    nacos:

      discovery:

        service: '${spring.application.name}'

        ip: '${spring.cloud.client.ip-address}'

        port: '${server.port}'

sprite:

  global-config:

    #项目编号

    project-code: PROJECT_1635185673461960704_1678843508667

    #版本号

    version: 1

    #实体类生成路径

    asm-main-class: com.hoteamsoft.touchy.goblin.cordial.doppler.domain.MainEntity

    #项目创建人账号，若mongodb内的userId为空时，可删除此配置

    # login-name: liuyaowei@hoteamsoft.com

  metadata-center:

    #mongodb地址

    host: 200.157.100.42

    #mongodb端口

    port: 27017

    #mongodb库名

    db-name: jitunahuadev

    #mongodb集合名(元数据初始化后会在mongodb中创建一个以项目编号命名的集合)

    collection-name: PROJECT_1635185673461960704_1678843508667

    #mongodb用户名

    credential-user-name: admin

    #mongodb用户名

    credential-db-location: admin

    #mongodb密码

    credential-password: 3DplazaPlatform2024

    #暂不了解

    cluster-deployment: false

  datasource:

    druid:

      connect-properties: oracle.jdbc.J2EE13Compliant=true

    enableStudioDataBase: true

    enableLog: true

    strict: false

    # 是否懒加载元数据中心数据源元数据

    enableLazyLoadMetadataCenter: true

    # 是否开启数据源分组功能

    enableDsGroup: false

    studioAopPackage: com.hoteamsoft.loki.server.controller

    cache:

      enableCacheExpire: true

    dataSources:

      MYSQL:

        url: jdbc:dm://200.157.100.42:5236/PLM_RUN_1?user=PLM_RUN_1&password=3DplazaPlatform2024

        username: PLM_RUN_1

        password: 3DplazaPlatform2024

    primary: mysql

nebula:

  enabled: false

#缓存配置

cache:

  #  系统基本缓存key前缀

  sysPrefixKey: i_base

logging:

  level:

    org:

      springframework:

        data:

          mongodb: info

keen:

  enable: true

  remote:

    redis:

      #  valueSerializer: java

      single:

        default:

          host: 200.157.100.42

          password: 123456

          port: 6379

          db: 26

feign:

  platform-base-service: platform-base-service

  platform-base-log: platform-base-service

loki:

  minio:

    enabled: true

    bucket-name: studio-cache

    endpoint: http://200.157.100.42:9000

    accessKey: admin

    secretKey: 3DplazaPlatform2024  

projectCode: ${project:PROJECT_1635185673461960704_1678843508667}

rocketmq:

  consumer:

    group: i-base-test

    namesrvAddr: 200.157.100.42:9876

    topic: "workFlowPluginTopicTest"

    tag: "*"

CommandService:

  #转换节点从系统中获取图幅、反签水印等配置的Url

  ConfigServerUrl: http://200.157.100.42:80
```



是一个 **Spring Boot 微服务 + Nacos 配置中心** 的配置文件（看起来像 `application.yml` 或从 Nacos 拉取的配置），里面包含 **服务注册、数据库、MongoDB、Redis、MinIO、RocketMQ** 等多个子系统的配置。

我帮你逐块拆解一下：

## 1. 服务基础配置

```yaml
server:

  port: 30108

spring:

  application:

    name: i-base

  cloud:

    nacos:

      discovery:

        service: '${spring.application.name}'

        ip: '${spring.cloud.client.ip-address}'

        port: '${server.port}'
```

- `server.port: 30108` → 微服务运行端口

- `spring.application.name: i-base` → 微服务的名字，服务注册到 Nacos 时用这个名字

- `spring.cloud.nacos.discovery` → Nacos 注册中心配置：

`service` → 服务名（这里引用 `i-base`）

`ip` → 服务注册时使用的 IP

`port` → 服务注册时使用的端口

👉 **作用**：把本服务（i-base）注册到 Nacos，供其他微服务发现和调用。

## 2. sprite 配置（自定义模块，可能是你们内部框架）

```yaml
sprite:

  global-config:

    project-code: PROJECT_1635185673461960704_1678843508667

    version: 1

    asm-main-class: com.hoteamsoft.touchy.goblin.cordial.doppler.domain.MainEntity
```

- `project-code` → 项目唯一编号

- `version` → 项目版本号

- `asm-main-class` → 主实体类路径，可能用于代码生成或业务逻辑入口

### metadata-center

```yaml
  metadata-center:

    host: 200.157.100.42

    port: 27017

    db-name: jitunahuadev

    collection-name: PROJECT_1635185673461960704_1678843508667

    credential-user-name: admin

    credential-db-location: admin

    credential-password: 3DplazaPlatform2024

    cluster-deployment: false
```

👉 MongoDB 作为 **元数据存储中心**：

- `db-name` → 库名

- `collection-name` → 集合名（通常和项目编号绑定）

- `credential-*` → MongoDB 用户名、密码、认证数据库

## 3. 数据源配置

```yaml
  datasource:

    druid:

      connect-properties: oracle.jdbc.J2EE13Compliant=true

    enableStudioDataBase: true

    enableLog: true

    strict: false

    enableLazyLoadMetadataCenter: true

    enableDsGroup: false

    studioAopPackage: com.hoteamsoft.loki.server.controller

    cache:

      enableCacheExpire: true

    dataSources:

      MYSQL:

        url: jdbc:dm://200.157.100.42:5236/PLM_RUN_1?user=PLM_RUN_1&password=3DplazaPlatform2024

        username: PLM_RUN_1

        password: 3DplazaPlatform2024

    primary: mysql
```

- 使用 **Druid 连接池**

- `enableStudioDataBase` → 是否启用工作室数据库

- `enableLog` → 是否启用 SQL 日志

- `dataSources.MYSQL` → 定义了一个数据源（但注意这里用的是 `jdbc:dm://`，其实是 **达梦数据库 DM** 协议，不是 MySQL）

- `primary: mysql` → 默认数据源

👉 说明：虽然写的是 MYSQL，但底层连的是 **达梦数据库 DM8**。

## 4. nebula 配置

```yaml
nebula:

  enabled: false
```

- 可能是图数据库 Nebula Graph，当前关闭。

## 5. 缓存配置

```yaml
cache:

  sysPrefixKey: i_base
```

- 定义缓存的 Key 前缀，避免不同服务的缓存冲突。

## 6. 日志级别

```yaml
logging:

  level:

    org:

      springframework:

        data:

          mongodb: info
```

- 设置 `spring-data-mongodb` 的日志级别为 `info`，避免日志太多。

## 7. Redis 配置

```yaml
keen:

  enable: true

  remote:

    redis:

      single:

        default:

          host: 200.157.100.42

          password: 123456

          port: 6379

          db: 26
```

- 启用了 `keen`（你们内部组件）

- `redis.single.default` → 连接单机 Redis

地址：200.157.100.42:6379

密码：123456

使用 DB 26

## 8. Feign 配置（服务调用）

```yaml
feign:

  platform-base-service: platform-base-service

  platform-base-log: platform-base-service
```

- Feign 调用时指定服务名（从 Nacos 里发现 `platform-base-service`）。

## 9. MinIO 配置（对象存储）

```yaml
loki:

  minio:

    enabled: true

    bucket-name: studio-cache

    endpoint: http://200.157.100.42:9000

    accessKey: admin

    secretKey: 3DplazaPlatform2024
```

- MinIO 文件存储开启，桶名 `studio-cache`

- 连接地址 `200.157.100.42:9000`

- 访问密钥 `admin / 3DplazaPlatform2024`

👉 用来存储缓存文件、资源文件。

## 10. 项目编号引用

```yaml
projectCode: ${project:PROJECT_1635185673461960704_1678843508667}
```

- 从外部 `project` 变量读取，若无则用默认值 `PROJECT_1635185673461960704_1678843508667`。

## 11. RocketMQ 消息队列

```yaml
rocketmq:

  consumer:

    group: i-base-test

    namesrvAddr: 200.157.100.42:9876

    topic: "workFlowPluginTopicTest"

    tag: "*"
```

- 配置 **RocketMQ 消费者**：

消费组：`i-base-test`

NameServer 地址：`200.157.100.42:9876`

订阅 Topic：`workFlowPluginTopicTest`

Tag：`*`（所有消息）

## 12. CommandService 配置

```yaml
CommandService:

  ConfigServerUrl: http://200.157.100.42:80
```

- 远程配置服务地址（可能用于文件转换、图幅、反签水印等）。

✅ **总结一下：**

- **Nacos 部分**：主要是 `spring.cloud.nacos.discovery`，用于注册服务到 Nacos。

- **其余部分**：数据库（达梦）、MongoDB、Redis、MinIO、RocketMQ、Feign、缓存、日志等，是本微服务 `i-base` 的业务配置。

- **整体作用**：

`i-base` 微服务启动 → 注册到 Nacos → 可被别的微服务发现

运行时使用 Nacos 配置中心下发的这些配置，完成数据库、缓存、消息队列、存储等初始化

### 其他命令解析

```bash
docker-compose -f /data/3DPlaza/docker-config/b1-mysql/docker-compose.yaml up -d
```

### 📝 命令分解

1. **docker-compose**

使用 Docker Compose 工具来管理和运行多容器应用。

2. **-f /data/3DPlaza/docker-config/b1-mysql/docker-compose.yaml**
- `-f` 选项指定要使用的 Compose 配置文件路径。

- 这里用的文件是 `/data/3DPlaza/docker-config/b1-mysql/docker-compose.yaml`，而不是默认的 `docker-compose.yml`。

- 说明项目的 Compose 配置文件存放在 `/data/3DPlaza/docker-config/b1-mysql/` 目录下。
3. **up**
- 根据指定的 `docker-compose.yaml` 文件创建并启动容器。

- 如果镜像没有，会自动拉取。

- 如果容器不存在，会自动创建。

- 如果容器已经存在但停止了，会自动启动。
4. **-d (detached mode)**
- 让容器在后台运行（不会占用当前终端）。

- 不加 `-d` 会在前台运行，日志直接打印在终端。

### 🚀 整体意思

**使用 /data/3DPlaza/docker-config/b1-mysql/docker-compose.yaml 这个 Compose 文件，在后台模式启动（或创建）其中定义的所有服务容器。**


