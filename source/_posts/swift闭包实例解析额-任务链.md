---
title: swift闭包实例解析额-任务链
date: 2025-05-07 11:30:06
tags: [swift编程,闭包]
---

# 💡 用闭包实现一个“任务链”


### 💻 原始代码：


```swift
func makeTaskQueue() -> () -> Void {
    var tasks: [() -> Void] = []
    
    func addTask(_ task: @escaping () -> Void) {
        tasks.append(task)
    }
    
    func run() {
        tasks.forEach { $0() }
    }
    
    return {
        addTask { print("Task 1") }
        addTask { print("Task 2") }
        run()
    }
}

let runAll = makeTaskQueue()
runAll()
```
<!--more-->


## 🧠 本质目标：


构造一个**任务调度器**：
你把多个任务（用闭包表示）一个个“收集”起来，最后统一执行。



## 🧱 分步解释：


### ✅ 1. 函数签名


```swift
func makeTaskQueue() -> () -> Void
```


这是一个**高阶函数**，返回一个闭包 `() -> Void`。
也就是说，调用 `makeTaskQueue()` 会返回一个不带参数、不返回值的函数。



### ✅ 2. 内部的 tasks 数组


```swift
var tasks: [() -> Void] = []
```


我们创建了一个数组 `tasks`，用于存储闭包（任务）。每个任务都是 `() -> Void`，表示：无参数、无返回值的函数。



### ✅ 3. 内部的 addTask 函数


```swift
func addTask(_ task: @escaping () -> Void) {
    tasks.append(task)
}
```


这个函数将闭包 `task` 添加进 `tasks` 数组。


- `@escaping` 关键字是必须的：因为闭包被“逃逸”到外部 `tasks` 数组中，生命周期比当前函数长。


### ✅ 4. run 函数：用于统一执行所有任务


```swift
func run() {
    tasks.forEach { $0() }
}
```


用 `forEach` 遍历 `tasks`，并执行每个闭包（任务）。



### ✅ 5. 返回的闭包：


```swift
return {
    addTask { print("Task 1") }
    addTask { print("Task 2") }
    run()
}
```


这个闭包定义了 **你“最终调用返回值时”做的事情**，它：


1. 添加两个任务；
2. 然后执行所有任务。


### ✅ 6. 使用方式


```swift
let runAll = makeTaskQueue()
runAll()
```


解释：


- `makeTaskQueue()` 返回了一个闭包。
- `runAll()` 就是运行这个闭包：它会执行刚才我们在闭包中定义的逻辑，即添加任务 + 执行任务。


## 🧪 输出结果：


```arduino
Task 1
Task 2
```


说明任务确实被依次执行了。



## 📌 关键知识点总结：


| 知识点 | 解释 |
| ---- | ---- |
| 闭包数组 [() -> Void] | 用于保存多个可执行任务 |
| @escaping | 闭包逃逸到当前函数作用域外（例如存入数组）时需要 |
| 高阶函数返回值 | makeTaskQueue 返回的是一个闭包 |
| 闭包捕获上下文 | tasks、addTask、run 都被“捕获”到返回闭包中 |
| 函数式编程思想 | 构建函数组合结构，延迟执行逻辑 |



## 🔧 拓展练习建议


你可以尝试对这个任务系统做一些升级：


1. **改成类，支持外部动态添加任务：**

```swift
class TaskQueue {
    private var tasks: [() -> Void] = []

    func add(_ task: @escaping () -> Void) {
        tasks.append(task)
    }

    func run() {
        tasks.forEach { $0() }
    }
}
```
2. **加任务命名 + 日志记录功能**
3. **加延迟执行功能（DispatchQueue）**
4. **模拟并发任务执行**



# 问题
() -> Void 函数返回类型和真正的return {
    addTask { print("Task 1") }
    addTask { print("Task 2") }
    run()
}匹配吗？感觉返回的不是() -> Void 类型




## 🤔 疑问重述：


你看到代码：


```swift
func makeTaskQueue() -> () -> Void {
    ...
    return {
        addTask { print("Task 1") }
        addTask { print("Task 2") }
        run()
    }
}
```


你疑惑：


- `makeTaskQueue` 声明返回 `() -> Void` 类型；
- 但 `return { ... }` 里看起来执行了很多事情，还调用了 `run()`；
- 为什么这仍然算是 `() -> Void` 类型？


## ✅ 解答：**返回值是一个“闭包”，而非闭包执行的结果**


关键点是：


```swift
return {
    addTask { print("Task 1") }
    addTask { print("Task 2") }
    run()
}
```


这个语法是：**返回一个闭包本身**（而不是立即执行闭包后返回结果）。



### ✅ 闭包的语法糖：


Swift 中 `return { ... }` 的 `{}` 部分就是一个**闭包表达式**，本质上等价于：


```swift
let closure: () -> Void = {
    addTask { print("Task 1") }
    addTask { print("Task 2") }
    run()
}
return closure
```


这里闭包类型确实是 `() -> Void`，因为：


- 它不接受任何参数；
- 最后执行的是 `run()`，而 `run()` 本身返回 `Void`；
- 所以这个闭包“整体”的返回值是 `Void`，即符合 `() -> Void` 类型。


## 🔍 关键点总结


| 项 | 说明 |
| ---- | ---- |
| () -> Void | 是**函数/闭包类型**，表示“接受0个参数、返回Void的函数” |
| return { ... } | 返回一个闭包，而非执行结果 |
| { addTask...; run() } | 是闭包的内容，只有调用闭包时才会执行这些语句 |
| Void 和 () | 在 Swift 中是一样的，表示“没有返回值” |



## ✅ 示例验证


你可以在 Playground 中试试这个简单例子来加深理解：


```swift
func makeThing() -> () -> Void {
    return {
        print("This is a closure.")
    }
}

let closure = makeThing() // 返回的是闭包本身
closure()                 // 现在才执行闭包，打印信息
```



## 🚫 易混淆点：不是“执行后返回结果”


很多人会以为：


```swift
return {
    doSomething()
}
```


好像是 “返回 `doSomething()` 的执行结果”，但实际上是返回 “一个包着 `doSomething()` 的闭包”。



## ✅ 总结一句话：



`return { ... }` 返回的是闭包本身，不是闭包执行后的结果。只要闭包签名是 `() -> Void`，无论里面执行多少语句、调用多少函数，它的**整体类型**就是 `() -> Void` —— 跟你看到的匹配完全一致。




是否还想进一步探讨闭包中的“捕获环境变量”、“逃逸闭包”和“尾随闭包”这些高级用法？我可以继续扩展。
# 问题
详细解释下这个函数，  func addTask(_ task: @escaping () ->Void) {
        tasks.append(task)
    }以及func makeTaskQueue() ->() ->Void函数执行的底层细节
# ChatGPT
非常好，这是深入理解 Swift 闭包和函数行为的关键一步！我们来从**语义、底层实现、捕获行为**多个角度详细解析下面这两段代码：



## 🔹函数 1：addTask(_:)


```swift
func addTask(_ task: @escaping () -> Void) {
    tasks.append(task)
}
```


### ✅ 这个函数做了什么？


- 接收一个闭包参数 `task`，类型是 `() -> Void`（无参数无返回值）
- 把这个闭包追加进 `tasks` 这个数组


### ❗️为什么要用 @escaping？


Swift 默认函数参数中的闭包是“非逃逸的”（non-escaping），意味着闭包会在函数调用期间就执行完，不会在函数体之外保存起来。


**但是这里我们将 task 存进了 tasks 数组，打算稍后执行** —— 这就是“闭包逃逸”。


🧠 **逃逸闭包的定义：**



被传入函数内部，但函数结束后仍会被使用或持有的闭包，称为“逃逸闭包”，必须用 `@escaping` 标记。




### 🔬 编译器如何处理 @escaping？


当你加了 `@escaping`，Swift 编译器会将闭包提升到堆（heap）上进行持久化，保证函数执行完之后闭包还能继续存在。否则，它可能会因为被放在栈上而失效（dangling pointer）。



## 🔹函数 2：makeTaskQueue() -> () -> Void


### 👀 首先看返回类型：


```swift
() -> Void
```


意味着 `makeTaskQueue()` 返回一个**函数/闭包**，这个返回值没有参数、返回 `Void`（即没有返回值）。



### 📦 函数体：


```swift
func makeTaskQueue() -> () -> Void {
    var tasks: [() -> Void] = []

    func addTask(_ task: @escaping () -> Void) {
        tasks.append(task)
    }

    func run() {
        tasks.forEach { $0() }
    }

    return {
        addTask { print("Task 1") }
        addTask { print("Task 2") }
        run()
    }
}
```


### 🔍 执行细节（按顺序）：


1. **函数被调用**：

```swift
let f = makeTaskQueue()
```
2. **内部作用域构建：**

- 创建了一个局部数组 `tasks`（栈上的引用，但实际元素在堆上）；
- 定义了两个内部函数 `addTask()` 和 `run()`，它们都闭包捕获了 `tasks`；
3. **返回一个闭包 { ... }**：

- 这个闭包 **内部调用 addTask() 多次并执行 run()**；
- 它被返回给外部：`f` 此时就是这个闭包。


### 🧠 闭包捕获发生了什么？


返回的闭包中使用了外部的局部变量 `tasks`、函数 `addTask()` 和 `run()`，所以 Swift 会：


- 将这些**捕获变量复制并封装进闭包上下文对象**；
- 所以即使函数 `makeTaskQueue()` 已经返回，`tasks` 数组仍然存在于内存中（堆）；
- 这是 Swift 的闭包捕获模型：**通过隐式创建上下文结构体（Context Struct）保存所有引用。**


### 📦 内存模型（示意）：


```swift
let closure = makeTaskQueue()
// 相当于生成了一个结构体（由编译器生成）：
struct TaskQueueContext {
    var tasks: [() -> Void]
    func addTask(...) { ... }
    func run() { ... }
    func call() {
        addTask { print("Task 1") }
        addTask { print("Task 2") }
        run()
    }
}
```


返回的闭包其实是封装了这个 `call()` 方法的结构体实例，闭包运行时实际上在执行这个方法。



## ✅ 整体流程回顾：


| 步骤 | 描述 |
| ---- | ---- |
| 1 | 调用 makeTaskQueue() |
| 2 | 创建 tasks 数组，定义 addTask() 和 run() |
| 3 | 返回一个闭包 { addTask...; run() }，捕获了这些上下文 |
| 4 | 当你调用闭包时，它会顺序添加任务并运行任务 |



## 🧪 调试建议（在 Playground 实验）


```swift
let closure = makeTaskQueue()
print(type(of: closure)) // 看闭包类型
closure() // 实际运行任务
```


你甚至可以打印闭包的地址、任务数量等信息来进一步理解捕获作用。



## ✳️ Bonus：可视化“捕获”原理图


```go
+------------------------+
| makeTaskQueue()        |
|                        |
|  +------------------+  |
|  | var tasks: [...] |◄────────────┐
|  +------------------+  |          │
|  | func addTask()   |──┐          │
|  | func run()       |──┘          │
|  +------------------+             │
|        ↓ captured                 │
|  return { ... addTask(); run() } │
|        ↑ returned closure ───────┘
+------------------------+
```



## ✅ 总结


- `addTask()` 是一个内部函数，接受逃逸闭包，保存到 `tasks` 数组。
- `makeTaskQueue()` 返回一个捕获了内部状态的闭包，实现延迟执行任务。
- Swift 底层用结构体封装捕获环境，闭包持有这些状态，哪怕外部函数已经结束。


# 深入学习 Swift 中的**逃逸闭包、自动闭包（@autoclosure）或捕获列表**等进阶闭包技巧？




### ✅ 1. **逃逸闭包（@escaping）**


- 闭包在函数作用域结束后还可能被调用。
- 常用于异步操作（如网络请求、延时回调）。
- 示例讲解 + 任务队列模拟。


### ✅ 2. **自动闭包（@autoclosure）**


- 让函数参数支持“延迟执行”。
- 语法更简洁，常用于 `assert()`, `guard`, `fatalError()` 等。

```swift
func log(_ message: @autoclosure () -> String) {
    print("Log: \(message())")
}
log("Something went wrong!") // 自动变成闭包
```



### ✅ 3. **捕获列表（[weak self], [unowned self]）**


- 解决循环引用问题，尤其是在类与闭包互相引用的情况下。
- 用于防止内存泄漏。

```swift
class MyClass {
    var name = "Swift"
    func run() {
        DispatchQueue.main.async { [weak self] in
            print(self?.name ?? "nil")
        }
    }
}
```



### ✅ 4. **闭包与内存管理**


- ARC 如何管理闭包捕获的引用？
- 如何理解“捕获的值” vs “捕获的引用”？
- 堆 vs 栈行为深入。





## 🧠 什么是捕获列表（Capture List）？


当你在一个闭包中使用了外部变量时，**Swift 默认会“强引用”它们**（尤其是类实例）。
这可能导致**循环引用（retain cycle）**，从而引发**内存泄漏**。


➡️ 捕获列表允许你控制这些外部引用是强引用、弱引用，还是无主引用。



## 🔍 基本语法


```swift
let closure = { [capture1, capture2] in
    // 代码
}
```


但最常见的写法是用于引用 `self`：


```swift
someAsyncTask { [weak self] in
    self?.doSomething()
}
```



## 📌 举例说明（默认强引用）


```swift
class MyClass {
    var name = "Swift"
    lazy var printName: () -> Void = {
        print("Name is \(self.name)")
    }
}

let obj = MyClass()
obj.printName()  // 输出 "Name is Swift"
```


### ⛔问题：循环引用


- `obj` 强引用 `printName` 闭包；
- 闭包又强引用 `self`（即 obj）；
- 导致 `obj` 永远不会释放 → 内存泄漏。


## ✅ 捕获列表解决方法


### 1. **[weak self]**（可选引用）


```swift
lazy var printName: () -> Void = { [weak self] in
    print("Name is \(self?.name ?? "nil")")
}
```


- `self` 变成可选类型；
- 不再造成强引用；
- 如果 `self` 被释放，闭包也不会阻止。


### 2. **[unowned self]**（非可选引用）


```swift
lazy var printName: () -> Void = { [unowned self] in
    print("Name is \(self.name)")
}
```


- 更像“弱引用但不检查是否为空”；
- ⚠️如果 `self` 已释放，访问将导致 **运行时崩溃**；
- 适合你确定闭包执行期间 `self` 不会释放的场景。


## ✅ 捕获常量 vs 变量


```swift
var x = 10
let closure = { [x] in
    print(x)
}
x = 20
closure()  // 输出 10，因为捕获了当时的 x 值副本
```


- 捕获的是**值**，不是变量本身；
- 所以之后外部变量改变，闭包中的值不会变。


## 🔧 实战练习：避免循环引用


```swift
class ViewModel {
    var name = "User"
    
    func load() {
        simulateAsync { [weak self] in
            guard let self = self else { return }
            print("Loaded \(self.name)")
        }
    }
    
    func simulateAsync(_ completion: @escaping () -> Void) {
        DispatchQueue.global().asyncAfter(deadline: .now() + 1) {
            completion()
        }
    }
}
```



## 📌 总结对比表


| 写法 | 是否释放 self | 是否可选 | 崩溃风险 | 适用场景 |
| ---- | ---- | ---- | ---- | ---- |
| [strong self] | ❌ 否（默认） | 否 | ❌ | 默认，可能循环引用 |
| [weak self] | ✅ 是 | ✅ 是 | ❌ | 不确定是否释放 |
| [unowned self] | ✅ 是 | ❌ 否 | ✅ 有崩溃风险 | self 保证生命周期存在 |



## ✅ 推荐练习（可复制进 Playground）


```swift
class TimerHandler {
    var count = 0

    func start() {
        Timer.scheduledTimer(withTimeInterval: 1.0, repeats: true) { [weak self] _ in
            self?.count += 1
            print("Count: \(self?.count ?? -1)")
        }
    }
}

var handler: TimerHandler? = TimerHandler()
handler?.start()
DispatchQueue.main.asyncAfter(deadline: .now() + 5) {
    handler = nil // 观察是否内存泄露
}
```
