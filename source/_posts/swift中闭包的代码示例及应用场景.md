---
title: swift中闭包的代码示例及应用场景
date: 2025-03-25 12:26:04
tags: [swift编程,闭包]
---




# 闭包示例，展示不同的用法，包括捕获值、闭包类型作为参数、尾随闭包等。



```swift
import Foundation

// 定义一个闭包，返回两个整数的和
let addClosure: (Int, Int) -&gt; Int = { a, b in
    return a + b
}

// 调用闭包并传递参数
let result = addClosure(5, 7)

print("结果是: \(result)")  // 输出：结果是: 12
```
<!--more-->

### 解析：


- `addClosure` 是一个闭包，接受两个 `Int` 参数，返回它们的和。
- 闭包被赋值给一个常量，并在后续通过传递参数来调用。


### 示例 1：捕获值的闭包


闭包可以捕获并存储其周围环境中的变量和常量。这是一个“捕获值”的例子。


```swift
import Foundation

func makeIncrementer(incrementAmount: Int) -&gt; () -&gt; Int {
    var total = 0
    let incrementer: () -&gt; Int = {
        total += incrementAmount
        return total
    }
    return incrementer
}

let incrementByTwo = makeIncrementer(incrementAmount: 2)

print(incrementByTwo())  // 输出: 2
print(incrementByTwo())  // 输出: 4
print(incrementByTwo())  // 输出: 6
```


### 解析：


- `makeIncrementer` 函数返回一个闭包，该闭包每次被调用时都会增加 `total` 变量的值。
- `incrementByTwo` 捕获了 `incrementAmount` 和 `total`，因此即使在外部调用时，闭包依然能访问并修改这些值。


### 示例 2：闭包作为函数参数


你可以将闭包作为函数的参数传递，下面是一个使用闭包进行排序的例子。


```swift
import Foundation

let numbers = [5, 3, 8, 1, 4]

let sortedNumbers = numbers.sorted { (a, b) -&gt; Bool in
    return a &lt; b
}

print(sortedNumbers)  // 输出: [1, 3, 4, 5, 8]
```


### 解析：


- `sorted` 方法接受一个闭包参数，这个闭包定义了如何比较两个数字（`a` 和 `b`）。
- 在闭包中，我们返回 `a &lt; b` 来实现升序排序。


### 示例 3：尾随闭包


当闭包是函数的最后一个参数时，你可以将闭包写在函数调用的外面，这叫做“尾随闭包”：


```swift
import Foundation

func performOperation(a: Int, b: Int, operation: (Int, Int) -&gt; Int) -&gt; Int {
    return operation(a, b)
}

let result = performOperation(a: 4, b: 5) { (x, y) in
    return x * y  // 闭包体
}

print(result)  // 输出: 20
```


### 解析：


- `performOperation` 函数接受两个整数参数以及一个闭包。尾随闭包传递了一个乘法操作，计算了两个数的乘积。


### 示例 4：捕获列表（捕获外部引用）


闭包有时可能会捕获并保留对外部变量的引用，这可能导致引用循环。通过使用“捕获列表”，你可以指定如何捕获外部变量，避免内存泄漏。


```swift
import Foundation

class MyClass {
    var value = 0
    
    func incrementValue() {
        let incrementer: () -&gt; Void = { [weak self] in
            self?.value += 1
            print(self?.value ?? 0)
        }
        incrementer()
    }
}

var myObject: MyClass? = MyClass()
myObject?.incrementValue()  // 输出: 1
myObject?.incrementValue()  // 输出: 2

myObject = nil  // 释放对象
```


### 解析：


- `[weak self]` 捕获了 `self` 的弱引用，这样就不会造成强引用循环。
- 当 `myObject` 被置为 `nil` 后，`self` 被自动释放，不会造成内存泄漏。


### 示例 5：异步闭包


闭包也可以在异步任务中使用，下面是一个使用闭包来处理网络请求的例子：


```swift
import Foundation

func fetchData(completion: @escaping (String) -&gt; Void) {
    DispatchQueue.global().async {
        // 模拟网络请求延迟
        sleep(2)
        DispatchQueue.main.async {
            completion("数据加载完毕")
        }
    }
}

fetchData { result in
    print(result)  // 输出: 数据加载完毕
}

RunLoop.main.run()  // 保持主线程运行，等待异步操作完成
```


### 解析：


- `fetchData` 模拟了一个异步任务，完成后会调用传递给它的 `completion` 闭包。
- `@escaping` 修饰符表示闭包会在函数返回后仍然被调用，通常用于异步任务。


这些示例展示了闭包在实际开发中的多种复杂用法，包括捕获外部变量、尾随闭包、异步任务等。你可以根据具体需求灵活运用。如果有其他问题，或者希望深入某一部分，随时告诉我！
# 用户问题
这些示例代码都有哪些应用场景
# ChatGPT
这些闭包的示例代码在实际开发中都有广泛的应用场景。每种用法都解决了不同类型的需求，以下是一些常见的应用场景：


### 1. **捕获值的闭包（闭包捕获外部变量）**


**应用场景：**


- **计数器或累加器**：在多次调用时保持状态，且每次调用时进行增量或状态更新。
- **持久化数据**：当你需要在多个方法间共享某些数据时，闭包可以捕获并保持这些值。

**典型使用场景：**


- **网络请求的成功回调**：每次网络请求时都需要对某个累加值进行更新。例如，每次从服务器获取数据时，更新下载进度。
- **动画或计时器**：需要在每次动画更新或定时器触发时执行某些增量操作。

例如，做一个下载进度的累加器：


```swift
func makeProgressTracker() -&gt; () -&gt; Int {
    var progress = 0
    let tracker: () -&gt; Int = {
        progress += 10
        return progress
    }
    return tracker
}

let progressTracker = makeProgressTracker()

print(progressTracker())  // 输出: 10
print(progressTracker())  // 输出: 20
```


### 2. **闭包作为函数参数**


**应用场景：**


- **排序、过滤和映射**：许多集合类（如数组、字典等）都提供了可以接受闭包的 API，用于进行排序、过滤、映射等操作。
- **自定义业务逻辑**：你可以将通用的函数封装成闭包参数传递，允许在不同的场景中使用不同的业务逻辑。

**典型使用场景：**


- **排序数据**：通过自定义闭包实现特定的排序逻辑。
- **UI更新或操作触发**：比如点击按钮后根据闭包的实现来决定如何响应点击事件。

例如，数组排序：


```swift
let numbers = [5, 3, 8, 1, 4]

let sortedNumbers = numbers.sorted { (a, b) -&gt; Bool in
    return a &lt; b  // 自定义升序排序
}

print(sortedNumbers)  // 输出: [1, 3, 4, 5, 8]
```


### 3. **尾随闭包**


**应用场景：**


- **回调函数**：在需要传递一个闭包作为参数时，尤其是闭包很长或需要传递多个参数时，尾随闭包可以使代码更简洁、更易读。
- **UI构建**：在 SwiftUI 中，很多视图组件的布局也使用尾随闭包进行简化。

**典型使用场景：**


- **事件响应或动画执行**：例如动画的 completion 闭包，用户交互后的处理闭包等。
- **自定义控件**：许多 UI 框架或库，如 SwiftUI，都会大量使用尾随闭包来提高可读性和简洁性。

例如，使用尾随闭包简化代码：


```swift
func performOperation(a: Int, b: Int, operation: (Int, Int) -&gt; Int) -&gt; Int {
    return operation(a, b)
}

let result = performOperation(a: 4, b: 5) { (x, y) in
    return x * y  // 闭包体
}

print(result)  // 输出: 20
```


### 4. **捕获列表（避免强引用循环）**


**应用场景：**


- **避免内存泄漏**：当闭包在对象内部被持有时，特别是闭包引用了对象本身，可能会导致强引用循环，捕获列表提供了一种方式来防止这种情况。
- **长时间运行的闭包任务**：例如，定时器、网络请求、异步任务等都可能引发强引用循环。

**典型使用场景：**


- **异步任务或定时器**：当闭包中引用了对象（如视图控制器或其他类的实例）时，捕获列表帮助我们避免内存泄漏。
- **UI 相关的任务**：比如在某些场景中，视图控制器内可能需要进行异步任务，这时需要确保在闭包执行完毕后，视图控制器能够被正确释放。

例如，避免闭包中的强引用循环：


```swift
class MyClass {
    var value = 0
    
    func incrementValue() {
        let incrementer: () -&gt; Void = { [weak self] in
            self?.value += 1
            print(self?.value ?? 0)
        }
        incrementer()
    }
}

var myObject: MyClass? = MyClass()
myObject?.incrementValue()  // 输出: 1
myObject?.incrementValue()  // 输出: 2

myObject = nil  // 释放对象
```


### 5. **异步闭包**


**应用场景：**


- **网络请求回调**：闭包非常适合处理异步回调，尤其是处理 HTTP 请求或其他异步操作。
- **多线程操作**：当你需要执行并发或后台任务时，使用闭包能够在任务完成后返回结果。

**典型使用场景：**


- **异步数据加载**：例如，获取远程服务器的数据时，你可以使用闭包来处理请求的结果。
- **UI 更新**：很多异步任务（如网络请求、数据库操作）完成后，需要更新 UI，这时可以使用闭包回调来更新界面。

例如，网络请求模拟：


```swift
func fetchData(completion: @escaping (String) -&gt; Void) {
    DispatchQueue.global().async {
        // 模拟网络请求延迟
        sleep(2)
        DispatchQueue.main.async {
            completion("数据加载完毕")
        }
    }
}

fetchData { result in
    print(result)  // 输出: 数据加载完毕
}

RunLoop.main.run()  // 保持主线程运行，等待异步操作完成
```



### 总结


这些示例中的闭包应用场景大体可以分为以下几类：


1. **状态管理**：通过闭包捕获和管理状态（如计数器、累加器等）。
2. **自定义逻辑**：通过将闭包作为参数传递，灵活地自定义排序、过滤、映射等业务逻辑。
3. **UI 响应和事件处理**：闭包作为事件的回调，简化了代码，增强了可读性（如尾随闭包）。
4. **内存管理**：使用捕获列表防止强引用循环，避免内存泄漏。
5. **异步处理和回调**：通过闭包处理异步任务和回调（如网络请求、定时器等）。

闭包在 Swift 中的强大灵活性使得它非常适合处理异步任务、回调、事件响应、内存管理等一系列复杂场景。