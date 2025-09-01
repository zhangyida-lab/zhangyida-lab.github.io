---
title: JAR和WAR的区别
date: 2025-09-01 16:43:02
tags:
---


# jar包和war包的区别


## 1. 定义


- **JAR（Java ARchive）包**


是一种 **Java 类库打包格式**。


主要用来 **打包 Java 类文件、配置文件、资源文件（图片、音频等）**。


常见场景：发布 Java 库、工具包，或者 Spring Boot 打包成可运行的 JAR。
- **WAR（Web Application ARchive）包**


是一种 **Web 应用打包格式**。


用于把一个 **Web 应用（Servlet、JSP、HTML、JS、CSS、图片、WEB-INF 配置等）** 打包成一个整体。


常见场景：部署到 Web 服务器（如 Tomcat、Jetty、JBoss）。


## 2. 文件结构差异


### JAR 包结构（普通 Java 应用）


```bash
myapp.jar
 ├── META-INF/
 │    └── MANIFEST.MF   （清单文件，可指定主类 Main-Class）
 ├── com/
 │    └── example/...
 └── 资源文件

```

### WAR 包结构（Web 应用）


```pgsql
myapp.war
 ├── META-INF/
 ├── WEB-INF/
 │    ├── web.xml           （Web 应用配置文件，Servlet/Filter/Listener）
 │    ├── classes/          （编译好的字节码 .class 文件）
 │    └── lib/              （依赖的 JAR 包）
 ├── index.jsp              （JSP 页面）
 ├── static/                （静态资源，HTML/JS/CSS/图片）
 └── 其他资源

```


## 3. 部署方式


- **JAR 包**


可以直接运行：

```bash
java -jar myapp.jar

```


常见于 **Spring Boot** 应用（内嵌 Tomcat/Jetty，不需要单独部署）。
- **WAR 包**


需要放到应用服务器（Tomcat、JBoss、WebLogic）里运行。


例如：把 `myapp.war` 放到 `Tomcat/webapps/` 下，启动 Tomcat 后访问应用。


## 4. 典型应用场景


- **JAR 包**


工具类库（比如 MyBatis.jar、mysql-connector.jar）


独立运行的应用（Spring Boot 项目常用）
- **WAR 包**


传统 Java Web 项目（Servlet、JSP、Struts、Spring MVC 等）


部署到企业级 Web 容器中


✅ **总结一句话**：


- **JAR** = Java 程序或库，通常可执行或供别人调用。
- **WAR** = Web 应用程序，必须运行在 Web 服务器中（除非是 Spring Boot 这种“可执行 WAR”）。
