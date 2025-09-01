---
title: 什么是IOC容器
date: 2025-09-01 17:01:32
tags:
---


# 什么是IoC 容器（Inversion of Control）
 **IoC 容器（Inversion of Control Container）**，它是 Spring 框架的**核心概念**之一。



# 🔹 1. 什么是 IoC（控制反转）


**IoC（Inversion of Control，控制反转）** 的核心思想是：
👉 **对象的创建与依赖关系的管理，不再由对象本身控制，而是交给一个统一的容器来管理**。

<!--more-->
对比一下：


- **传统方式**（自己控制）

```java
UserService userService = new UserService(new UserRepository());

```


`UserService` 自己决定依赖谁，并且要主动去 `new`。
- **IoC 方式**（控制反转）

```java
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
UserService userService = context.getBean(UserService.class);

```


对象由 Spring 容器创建和注入，程序员只需“声明需要什么”，不用“亲手创建”。

👉 这就是 “控制权反转”：从自己管理对象 → 交给容器管理。



# 🔹 2. IoC 容器是什么


在 Spring 中，IoC 容器就是用来 **创建对象（Bean）、管理它们的生命周期、处理依赖关系** 的核心模块。


主要接口：


- **BeanFactory**：最底层的 IoC 容器，只在需要时才创建对象（懒加载）。
- **ApplicationContext**：更高级的容器，基于 `BeanFactory`，支持国际化、事件机制、自动扫描 Bean、AOP 等（实际开发常用）。


# 🔹 3. IoC 容器的工作原理


容器启动时会做以下事情：


1. **加载配置**

- XML 配置文件
- Java 注解（`@Component`, `@Configuration`, `@Bean`）
- JavaConfig 类
2. **实例化 Bean**
根据配置创建对象实例。
3. **依赖注入**
把需要的依赖对象注入进去（DI）。
4. **初始化 Bean**
执行 `@PostConstruct`、`InitializingBean` 等。
5. **使用 Bean**
应用程序通过 `context.getBean()` 或 `@Autowired` 获取 Bean。
6. **销毁 Bean**
容器关闭时，执行 `@PreDestroy`、`DisposableBean` 等。


# 🔹 4. Bean 的作用域（Scope）


IoC 容器中 Bean 的作用域有：


- `singleton`（默认） → 整个容器中只有一个实例
- `prototype` → 每次获取都会创建新对象
- `request`（Web） → 每次 HTTP 请求创建一个实例
- `session`（Web） → 每个 HTTP 会话创建一个实例


# 🔹 5. IoC 的好处


- **解耦**：对象只关心“做什么”，不关心“依赖谁”。
- **灵活**：更换实现只需改配置，不改业务代码。
- **可测试**：方便注入 Mock 对象进行单元测试。
- **统一管理**：生命周期和依赖关系由容器统一管理。


# 🔹 6. 举个例子


```java
@Component
class UserRepository {
    public void save(String name) {
        System.out.println("保存用户：" + name);
    }
}

@Service
class UserService {
    private final UserRepository userRepository;

    @Autowired
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public void addUser(String name) {
        userRepository.save(name);
    }
}

```

启动 Spring 容器后：


```java
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
UserService userService = context.getBean(UserService.class);
userService.addUser("张三");

```

👉 这里的 `UserService` 和 `UserRepository` 都是由 **IoC 容器创建并组装好的**。



✅ **总结**


- IoC 是一种思想：把对象的创建与依赖管理交给容器。
- IoC 容器 = Bean 工厂 + 依赖管理 + 生命周期管理。
- 在 Spring 中，`ApplicationContext` 是最常用的 IoC 容器实现。