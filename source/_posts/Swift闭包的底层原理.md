---
title: Swift闭包的底层原理
date: 2025-03-21 17:53:40
tags: swift编程
---

## **Swift 闭包的底层原理**

Swift 中的 **闭包（Closure）** 是一种功能强大的特性，它本质上是一种可以捕获并存储其上下文环境中的变量和常量的 **匿名函数**。Swift 的闭包在底层是通过 **堆（Heap）分配、自动内存管理（ARC）以及栈（Stack）优化** 实现的。



## **Swift 闭包的底层原理**


### **1. 闭包的存储位置**


在 Swift 中，函数和闭包的存储方式取决于它们是否捕获了外部变量：

<!--more-->
- **没有捕获外部变量的闭包**（即“普通函数”）存储在 **代码段（Text Segment）**，不会涉及堆分配。
- **捕获了外部变量的闭包** 需要额外存储捕获的变量，因此 Swift 会将它们分配到 **堆（Heap）**，并使用 **ARC（自动引用计数）** 进行内存管理。

### **2. 捕获列表的作用**


闭包的 **捕获列表（Capture List）** 允许开发者控制变量的捕获方式：


- **强引用（默认）**：直接捕获变量，会导致引用计数增加，可能造成循环引用。
- **弱引用（weak）**：不会增加引用计数，变量可能变为 nil。
- **无主引用（unowned）**：不会增加引用计数，适用于变量不会变成 nil 的情况。

```swift
class Person {
    var name: String
    init(name: String) { self.name = name }
    deinit { print("\(name) 被释放") }
}

var closure: (() -&gt; Void)?

do {
    let p = Person(name: "Tom")
    closure = { print(p.name) } // p 被闭包强引用
}
closure?() // "Tom"
```


**问题**：`p` 的生命周期被闭包延长，无法立即释放。


使用 `[weak p]` 解决循环引用：


```swift
do {
    let p = Person(name: "Tom")
    closure = { [weak p] in print(p?.name ?? "No Name") }
}
closure?() // "No Name"，p 已被释放
```



### **3. Swift 闭包的底层实现**


#### **(1) 逃逸与非逃逸闭包**


- **非逃逸闭包（@noescape，默认）**：

不会在函数执行完毕后继续存活。
其生命周期受限于函数作用域，编译器可以优化它，通常存储在**栈（Stack）**中，减少堆分配。
- **逃逸闭包（@escaping）**：

会在函数返回后仍然存活，例如用于异步回调、存储到全局变量或对象属性中。
需要在堆（Heap）上分配，并使用 ARC 进行管理。

```swift
func testClosure(_ closure: () -&gt; Void) {
    closure() // 非逃逸
}

func escapingClosure(_ closure: @escaping () -&gt; Void) {
    DispatchQueue.global().async {
        closure() // 逃逸闭包，需要存储在堆上
    }
}
```


#### **(2) 闭包的底层结构**


Swift 闭包本质上是一个**结构体**，它包含：


1. **函数指针**（指向实际执行的代码）。
2. **捕获的变量**（如果有，存储在堆上）。
3. **引用计数信息**（用于 ARC 管理）。

可以用 `@convention(c)` 让 Swift 闭包转换成 C 语言函数指针，说明闭包底层其实就是函数指针的封装：


```swift
typealias CFunction = @convention(c) (Int, Int) -&gt; Int
let sum: CFunction = { $0 + $1 }
```



### **4. 栈 vs. 堆 分配**


| **类型** | **存储位置** | **性能** |
| ---- | ---- | ---- |
| **普通函数** | 代码段 | 快 |
| **无变量捕获闭包** | 栈 | 快 |
| **有变量捕获闭包** | 堆 | 慢（ARC 影响性能） |
| **逃逸闭包（@escaping）** | 堆 | 慢 |


Swift 编译器会进行 **栈优化（Stack Promotion）**，对于不捕获变量的闭包，避免堆分配，提高性能。



## **总结**


1. **Swift 闭包是结构体**，内部包含函数指针和捕获的变量。
2. **无变量捕获时**，闭包可以存储在 **栈** 中，避免 ARC 管理，提高性能。
3. **有变量捕获时**，Swift 会在 **堆** 中分配闭包，并使用 **ARC** 进行管理。
4. **逃逸闭包（@escaping）** 会导致闭包必须存储在 **堆** 中，增加性能开销。
5. **捕获列表** 控制闭包如何捕获变量，避免循环引用问题。

这些底层实现使 Swift 闭包既强大又高效，同时需要合理管理引用，避免内存泄漏。