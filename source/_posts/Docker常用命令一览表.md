---
title: Docker常用命令一览表
date: 2025-09-05 16:03:28
tags: [微服务]
---

## 🔹 容器相关


```bash
# 查看正在运行的容器
docker ps

# 查看所有容器（包括已停止）
docker ps -a

# 启动容器
docker start &lt;容器ID或名称&gt;

# 停止容器
docker stop &lt;容器ID或名称&gt;

# 重启容器
docker restart &lt;容器ID或名称&gt;

# 删除容器
docker rm &lt;容器ID或名称&gt;

# 强制删除运行中的容器
docker rm -f &lt;容器ID或名称&gt;

# 查看容器日志
docker logs &lt;容器ID或名称&gt;

# 实时查看日志
docker logs -f &lt;容器ID或名称&gt;

# 进入容器（交互式终端）
docker exec -it &lt;容器ID或名称&gt; /bin/bash

```


## 🔹 镜像相关


```bash
# 查看本地镜像
docker images

# 拉取镜像
docker pull &lt;镜像名称:标签&gt;
# 示例：docker pull nginx:latest

# 删除镜像
docker rmi &lt;镜像ID或名称&gt;

# 构建镜像（需有Dockerfile）
docker build -t &lt;镜像名称:标签&gt; .

# 给镜像打标签
docker tag &lt;镜像ID&gt; &lt;新名称:新标签&gt;

```


## 🔹 容器运行


```bash
# 启动并运行一个容器
docker run -it &lt;镜像名称&gt; /bin/bash

# 后台运行容器
docker run -d &lt;镜像名称&gt;

# 运行容器并映射端口
docker run -d -p 8080:80 &lt;镜像名称&gt;

# 挂载本地目录到容器
docker run -d -v /宿主机路径:/容器路径 &lt;镜像名称&gt;

```


## 🔹 网络与数据卷


```bash
# 查看网络
docker network ls

# 创建网络
docker network create &lt;网络名称&gt;

# 删除网络
docker network rm &lt;网络名称&gt;

# 查看卷
docker volume ls

# 创建卷
docker volume create &lt;卷名称&gt;

# 删除卷
docker volume rm &lt;卷名称&gt;

```


## 🔹 系统管理


```bash
# 查看 Docker 版本
docker version

# 查看 Docker 系统信息
docker info

# 清理未使用的镜像、容器、网络等（慎用）
docker system prune -a

```








# 🐳 Docker 常用命令速查表


| 命令 | 英文含义 | 中文解释 |
| ---- | ---- | ---- |
| docker ps | process status | 查看正在运行的容器 |
| docker ps -a | all | 查看所有容器（包括已停止） |
| docker start | start | 启动已存在但停止的容器 |
| docker stop | stop | 停止运行中的容器 |
| docker restart | restart | 重启容器 |
| docker rm | remove | 删除容器（仅限已停止） |
| docker rm -f | force remove | 强制删除运行中的容器 |
| docker logs | logs | 查看容器日志 |
| docker logs -f | follow logs | 实时跟踪容器日志 |
| docker exec -it | execute (interactive + tty) | 进入容器执行命令（交互式终端） |



## 镜像相关


| 命令 | 英文含义 | 中文解释 |
| ---- | ---- | ---- |
| docker images | images | 查看本地镜像 |
| docker pull | pull | 从远程仓库下载镜像 |
| docker rmi | remove image | 删除本地镜像 |
| docker build -t | build (tag) | 根据 Dockerfile 构建镜像并打标签 |
| docker tag | tag | 给镜像打新标签 |



## 容器运行


| 命令 | 英文含义 | 中文解释 |
| ---- | ---- | ---- |
| docker run -it | run (interactive + tty) | 运行容器并进入命令行 |
| docker run -d | run (detached) | 后台运行容器 |
| docker run -p | run (publish) | 运行容器并映射端口 |
| docker run -v | run (volume) | 运行容器并挂载宿主机目录 |



## 网络与存储


| 命令 | 英文含义 | 中文解释 |
| ---- | ---- | ---- |
| docker network ls | list | 查看网络 |
| docker network create | create | 创建网络 |
| docker network rm | remove | 删除网络 |
| docker volume ls | list volumes | 查看数据卷 |
| docker volume create | create volume | 创建数据卷 |
| docker volume rm | remove volume | 删除数据卷 |



## 系统管理


| 命令 | 英文含义 | 中文解释 |
| ---- | ---- | ---- |
| docker version | version | 显示 Docker 客户端和服务端版本 |
| docker info | info | 显示 Docker 系统信息 |
| docker system prune -a | prune (all) | 清理未使用的容器、镜像、网络、卷 |

