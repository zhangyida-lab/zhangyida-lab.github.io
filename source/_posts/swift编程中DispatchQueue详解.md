---
title: swift编程中DispatchQueue详解
date: 2025-03-22 13:38:33
tags: swift编程
---



# swift编程中 DispatchQueue.main.async

在 Swift 编程中，`DispatchQueue.main.async` 主要用于将任务提交到**主线程**的**主队列**（`DispatchQueue.main`）上，并异步执行。


## **基本概念**
<!--more-->

### **1. 什么是 DispatchQueue.main?**


`DispatchQueue.main` 代表**主队列**（Main Queue），它是一个**串行队列（Serial Queue）**，所有任务都会按顺序执行。


- **主队列运行在主线程上**，而主线程主要用于更新 UI 以及响应用户交互。
- 任何 UI 更新都必须在主线程上进行，否则可能会导致界面异常或崩溃。

### **2. async（异步）意味着什么？**


`async` 表示任务会被提交到队列，但不会**阻塞**当前线程，任务会在稍后执行。


- 这意味着你可以在后台线程执行一些耗时任务（如网络请求），然后使用 DispatchQueue.main.async 切换回主线程更新 UI。


## **使用示例**


### **1. 在主线程更新 UI**


```swift
DispatchQueue.main.async {
    self.label.text = "数据加载完成"
}
```


- 这里 self.label.text = "数据加载完成" 需要在主线程执行，否则 UI 可能不会正确更新。


### **2. 在后台执行任务后回到主线程**


通常，我们在后台线程执行一些耗时操作，例如网络请求，然后在主线程更新 UI：


```swift
DispatchQueue.global(qos: .background).async {
    // 在后台线程执行耗时任务
    let data = self.loadData()
    
    DispatchQueue.main.async {
        // 回到主线程更新 UI
        self.label.text = data
    }
}
```


- DispatchQueue.global(qos: .background).async 让 loadData() 在后台线程运行。
- DispatchQueue.main.async 让 UI 更新回到主线程执行。


### **3. 避免 EXC_BAD_ACCESS 问题**


如果你在子线程直接修改 UI 组件（如 `UILabel`、`UIButton`），可能会导致应用崩溃：


```swift
DispatchQueue.global().async {
    self.label.text = "错误的操作" // ❌ 在后台线程修改 UI，会导致崩溃
}
```


解决方案：


```swift
DispatchQueue.global().async {
    let data = self.loadData()
    
    DispatchQueue.main.async {
        self.label.text = data // ✅ 确保 UI 更新在主线程执行
    }
}
```



## **小结**


- DispatchQueue.main 代表主线程的**主队列**，用于执行 UI 相关任务。
- async 使任务在主队列上**异步**执行，不会阻塞当前线程。
- **所有 UI 更新必须在主线程执行**，否则可能导致 UI 崩溃。
- 常见用法：在后台加载数据后，使用 DispatchQueue.main.async 切换回主线程更新 UI。


# DispatchQueue 中的dispatch 是什么意思

`DispatchQueue` 中的 **"dispatch"** 直译为 **"分派"、"调度"**，在编程上下文中可以理解为 **"任务调度"**。


在 Swift 的 `DispatchQueue`（调度队列）中，`dispatch` 负责管理任务的执行方式，包括 **在哪个线程执行**、**是同步还是异步执行** 等。



## **dispatch 的核心概念**


### **1. 任务调度（Dispatching Tasks）**


`dispatch` 允许你将任务（代码块）调度到不同的**队列**上，以不同的方式（同步 `sync` 或异步 `async`）执行。例如：


```swift
DispatchQueue.global().async {
    print("在后台线程执行任务")
}
DispatchQueue.main.async {
    print("回到主线程执行 UI 更新")
}
```


这里，`dispatch` 负责**把任务派发到不同的队列**，并安排它们的执行顺序。



### **2. dispatch 作为 GCD（Grand Central Dispatch） 的一部分**


在 iOS 和 macOS 中，`DispatchQueue` 是 **GCD（Grand Central Dispatch）** 的核心组件之一。
GCD 是 Apple 提供的一个**多线程管理工具**，用于高效管理 CPU 资源，提高应用性能。



### **3. dispatch 的不同用法**


#### **（1）同步 vs 异步**


- **同步（sync）**：任务执行完毕后，才会继续执行后续代码（阻塞当前线程）。
- **异步（async）**：任务会被提交到队列中，当前代码继续执行，不会等待任务完成（非阻塞）。

示例：


```swift
DispatchQueue.global().sync {
    print("同步任务，当前线程会等待")
}
DispatchQueue.global().async {
    print("异步任务，当前线程不会等待")
}
```


#### **（2）主队列 vs 全局队列**


- **主队列（DispatchQueue.main）**：所有 UI 相关任务必须在主线程执行。
- **全局队列（DispatchQueue.global()）**：用于执行后台任务（如网络请求、数据处理）。

示例：


```swift
DispatchQueue.main.async {
    print("主线程更新 UI")
}
DispatchQueue.global().async {
    print("后台线程处理数据")
}
```


#### **（3）自定义队列**


你可以创建自己的 `DispatchQueue` 来管理任务：


```swift
let myQueue = DispatchQueue(label: "com.myapp.myqueue", qos: .userInitiated)
myQueue.async {
    print("在自定义队列中执行任务")
}
```



## **总结**


- dispatch 代表 **任务调度**，用于管理代码在不同线程/队列上的执行方式。
- DispatchQueue 负责在主线程或后台线程上执行任务，以提高应用的**并发性**和**性能**。
- sync 和 async 控制任务的执行方式，**async 是非阻塞的，sync 是阻塞的**。
- DispatchQueue.main 适用于 UI 更新，DispatchQueue.global() 适用于后台任务。
