---
title: 什么是springBoot
date: 2025-09-01 17:03:45
tags:
---


# 什么是sprigboot

 **Spring Boot**，这是 Spring 生态里最常用的框架之一。



# 🔹 1. 什么是 Spring Boot


**Spring Boot** 是 **Spring 框架的快速开发脚手架**。
它的目标是：
👉 **让你几分钟就能构建一个可运行的 Spring 应用，而不是几天**。


换句话说：
<!--more-->

- **Spring Framework**：功能强大，但配置复杂（XML + JavaConfig）。
- **Spring Boot**：在 Spring 之上做了大量自动化封装，简化开发。


# 🔹 2. Spring Boot 的核心特性


1. **自动配置（Auto Configuration）**

- 不用手写大量的 XML 或 Java 配置，Spring Boot 会根据依赖自动配置常见组件。
- 比如你引入 `spring-boot-starter-web`，就自动配置好 Tomcat、Spring MVC、Jackson。
2. **起步依赖（Starter）**

- 提供一组“开箱即用”的依赖管理包。
- 比如：


`spring-boot-starter-web` → Web 应用（MVC + Tomcat + JSON）


`spring-boot-starter-data-jpa` → JPA + Hibernate


`spring-boot-starter-security` → 安全认证


`spring-boot-starter-test` → 测试框架
3. **内嵌 Web 服务器**

- 内置 Tomcat、Jetty、Undertow，直接 `java -jar` 就能启动应用，不需要额外安装。
4. **生产级特性（Actuator）**

- 监控、健康检查、日志、度量指标（Metrics）开箱即用。
5. **零配置运行**

- 默认约定大于配置（Convention over Configuration）。
- 配置文件通常只需改 `application.yml` 或 `application.properties`。


# 🔹 3. Spring Boot 的项目结构


一个典型的 Spring Boot 项目：


```bash
src/main/java
 └── com.example.demo
     ├── DemoApplication.java   # 主程序入口
     ├── controller/            # 控制层
     ├── service/               # 业务层
     └── repository/            # 数据访问层
src/main/resources
 ├── application.yml            # 配置文件
 └── static/                    # 静态资源

```

主类：


```java
@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}

```


# 🔹 4. 常用注解


- **核心注解**


`@SpringBootApplication` → 启动类注解（包含 `@Configuration + @EnableAutoConfiguration + @ComponentScan`）。
- **Web 层**


`@RestController` → 标记控制器类（返回 JSON）。


`@RequestMapping`, `@GetMapping`, `@PostMapping` → 请求映射。
- **Bean 管理**


`@Component`, `@Service`, `@Repository`, `@Controller` → 标记 Spring 管理的组件。


`@Autowired` → 依赖注入。


`@Configuration`, `@Bean` → JavaConfig 方式定义 Bean。
- **配置**


`@ConfigurationProperties` → 绑定配置文件到对象。


`@Value` → 读取配置项。


# 🔹 5. Spring Boot 配置文件


支持两种：


- `application.properties`
- `application.yml`（推荐，层次更清晰）

示例：


```yaml
server:
  port: 8081

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/demo
    username: root
    password: root
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true

```


# 🔹 6. Spring Boot 的优势


✅ 快速上手，几行代码就能跑一个 Web 应用。
✅ 内嵌服务器，打包成 `jar` 就能部署。
✅ 自动配置，大量默认设置，减少样板代码。
✅ 统一的 Starter 依赖管理。
✅ 生产级监控与健康检查。



# 🔹 7. 适用场景


- **微服务架构**（通常结合 Spring Cloud 使用）。
- **企业级 Web 应用**（REST API、管理后台）。
- **中小型快速开发项目**（CRUD 系统）。
- **云原生应用**（Docker + Kubernetes 部署）。


✅ **总结**
Spring Boot 是 Spring 的“加速器”：


- 让开发者只关注业务逻辑，减少配置负担；
- 结合 Spring Cloud 可以快速构建 **微服务系统**。