---
title: swift中@MainActor的作用
date: 2025-03-23 21:32:35
tags:
---


# @MainActor 什么作用

`@MainActor` 是一个 **Swift Concurrency**（并发）中的属性包装器，主要用于确保某个函数或某个对象的代码在 **主线程** 上执行。


在 iOS 和 macOS 等应用程序中，更新 UI 必须在 **主线程** 上进行。否则，可能会导致 **UI 错乱、崩溃** 或 **不可预期的行为**。Swift 引入了并发和异步编程（`async/await`），`@MainActor` 就是为了确保在并发环境下，对 UI 的更新和其他与主线程相关的操作能安全地进行。


### **作用**：


- 确保特定的函数、属性或类在 **主线程** 上执行。
- 防止并发代码在后台线程访问 UI 或与 UI 相关的状态，避免线程安全问题。

### **常见使用场景**：


- **UI 更新**：在异步任务完成后更新 UI。
- **主线程操作**：处理与主线程相关的任务，如网络请求完成后更新 UI。


### **代码示例：**


```swift
import SwiftUI

@MainActor
class ViewModel: ObservableObject {
    @Published var data: String = "Loading..."
    
    func fetchData() async {
        // 假设我们在后台线程异步获取数据
        await Task.sleep(2 * 1_000_000_000) // 模拟网络延迟
        let newData = "数据加载完成"
        
        // 由于这个方法被标记为 @MainActor，以下更新 UI 的代码会在主线程上执行
        self.data = newData
    }
}

struct ContentView: View {
    @StateObject private var viewModel = ViewModel()

    var body: some View {
        VStack {
            Text(viewModel.data)
                .font(.largeTitle)
                .padding()
            
            Button("加载数据") {
                Task {
                    await viewModel.fetchData()
                }
            }
            .padding()
        }
    }
}

#Preview {
    ContentView()
}
```


### **解析**：


- **@MainActor**：确保 `ViewModel` 的 `fetchData` 方法在主线程上执行 UI 更新操作，即使它本身是异步的。
- 即使在 `fetchData` 方法中调用了 `await`，返回到主线程更新 `data` 属性时也不会发生线程安全问题，因为 `@MainActor` 会自动确保所有与 UI 相关的操作都在主线程中进行。


### **注意点**：


1. **@MainActor** 只能应用于需要 **主线程操作** 的方法或类，尤其是那些更新 UI 的地方。
2. 在 Swift Concurrency 中，异步操作（`async/await`）默认是异步执行的，但一旦涉及到 UI 更新，必须确保这些操作回到主线程。`@MainActor` 做的就是这一件事。

### **总结**：


`@MainActor` 使得代码能够在 **主线程** 上执行，从而避免在并发操作中直接更新 UI 的问题，保证了线程安全和 UI 的一致性。


如果你在进行异步任务处理时遇到需要更新 UI 的情况，记得使用 `@MainActor` 来确保线程安全！

# @MainActor 只是修饰 class ViewModel吗

`@MainActor` **不仅可以修饰 class ViewModel，还可以用于函数、属性、全局变量等**，它的作用是确保被修饰的代码 **始终在主线程上执行**。


### **@MainActor 可以修饰的内容**


1. **修饰类/结构体**
2. **修饰方法（函数）**
3. **修饰属性**
4. **修饰全局变量**


## **1. 修饰类 / 结构体**


如果在 `ViewModel` 这个类前添加 `@MainActor`，**该类的所有属性和方法** 默认都会在 **主线程** 上执行。


```swift
@MainActor
class ViewModel: ObservableObject {
    @Published var data: String = "Loading..."
    
    func fetchData() async {
        await Task.sleep(2 * 1_000_000_000) // 模拟网络延迟
        data = "数据加载完成"
    }
}
```


**作用**：
✅ `data` 这个 `@Published` 属性默认在主线程上访问。
✅ `fetchData()` 也默认在主线程上执行，不用手动 `DispatchQueue.main.async` 切换线程。



## **2. 只修饰某个方法**


如果你不想让整个类都在主线程执行，可以**单独修饰某个方法**，比如 `fetchData()` 方法，而不修饰整个 `ViewModel` 类：


```swift
class ViewModel: ObservableObject {
    @Published var data: String = "Loading..."
    
    @MainActor
    func fetchData() async {
        await Task.sleep(2 * 1_000_000_000) // 模拟网络延迟
        data = "数据加载完成"
    }
}
```


**作用**：
✅ `fetchData()` 会在主线程上执行，而 `ViewModel` 其他方法仍然可以在子线程上运行。



## **3. 只修饰某个属性**


如果你只想保证某个属性在主线程上访问，可以这样做：


```swift
class ViewModel: ObservableObject {
    @MainActor @Published var data: String = "Loading..."
    
    func fetchData() async {
        await Task.sleep(2 * 1_000_000_000) // 模拟网络延迟
        await MainActor.run { // 在主线程上更新数据
            data = "数据加载完成"
        }
    }
}
```


**作用**：
✅ `data` 这个属性**只能**在主线程上被访问。
✅ `fetchData()` 仍然可以在后台执行，但更新 `data` 时会**切换到主线程**。



## **4. 作用于全局变量**


如果你有一个**全局变量**，你可以使用 `@MainActor` 让它始终在 **主线程上访问**：


```swift
@MainActor var globalText: String = "默认文本"
```



## **5. 结合 Task 在主线程运行代码**


如果你的 `ViewModel` 没有 `@MainActor` 修饰，但你想在 `fetchData()` 里保证某些代码在主线程执行，可以这样做：


```swift
class ViewModel: ObservableObject {
    @Published var data: String = "Loading..."
    
    func fetchData() async {
        await Task.sleep(2 * 1_000_000_000) // 模拟网络延迟
        await MainActor.run {
            data = "数据加载完成"
        }
    }
}
```


**作用**：
✅ `await MainActor.run {}` 让 `data` 更新时切换到主线程执行。
✅ `fetchData()` 本身可以在后台执行，提高性能。



## **总结**


| 语法 | 作用 |
| ---- | ---- |
| @MainActor class MyClass {} | **整个类** 的方法和属性都会在 **主线程** 上执行 |
| @MainActor struct MyStruct {} | **整个结构体** 的方法和属性都在 **主线程** 上执行 |
| @MainActor func myFunction() {} | **单独一个方法** 在 **主线程** 上执行 |
| @MainActor var myProperty = "" | **单个属性** 只能在 **主线程** 访问 |
| await MainActor.run {} | **在某个代码块** 内切换到 **主线程** |


如果你的 `ViewModel` 里有多个 `@Published` 属性，或者大量 UI 相关的代码，建议直接给整个类加 `@MainActor`，这样代码更干净，不需要在每个地方都 `await MainActor.run {}`。


💡 **最佳实践**：


- 处理 UI 相关数据时，加 `@MainActor`，确保所有 UI 代码在主线程执行。
- 网络请求、数据处理等可以在后台线程进行，等到需要更新 UI 时再 `await MainActor.run {}` 切换到主线程。


你可以根据具体需求来决定 **修饰整个类 / 结构体，还是单独修饰方法、属性**。希望这些例子能帮助你理解 `@MainActor` 的用法！🚀

# “@MainActor 使得代码能够在 主线程 上执行，从而避免在并发操作中直接更新 UI 的问题，保证了线程安全和 UI 的一致性。”的解释
理解 `@MainActor` 需要先明白两个重要概念：


1. **主线程（Main Thread）**

- 在 iOS/macOS 应用中，**主线程主要用于 UI 更新**。
- 如果你在后台线程更新 UI，可能会导致 **界面不响应、崩溃** 或 **数据不一致**。
2. **Swift Concurrency（并发编程）**

- `async/await` 允许你在 **多个线程** 之间切换，提高应用性能。
- 但如果在后台线程直接修改 UI，可能会导致 **线程安全问题**。


### **为什么不能在后台线程更新 UI？**


假设你从服务器获取数据，并在异步任务中更新 `UILabel`，如果这个操作在**后台线程**执行，可能会出现问题。


**错误示例（没有回到主线程更新 UI）：**


```swift
func fetchData() async {
    let url = URL(string: "https://example.com/data")!
    
    do {
        let (data, _) = try await URLSession.shared.data(from: url)
        let text = String(data: data, encoding: .utf8) ?? "加载失败"

        // ❌ 直接在后台线程更新 UI，可能导致崩溃
        label.text = text  
    } catch {
        print("数据获取失败: \(error)")
    }
}
```


在 Swift 中，`URLSession.shared.data(from:)` **默认在后台线程执行**。如果 `label.text = text` 也是在后台线程执行，可能会导致 **UI 崩溃**，因为 UIKit 只能在主线程上操作。



### **如何用 @MainActor 解决这个问题？**


`@MainActor` 让代码**始终在主线程上执行**，确保 UI 更新的安全性。


#### **方法 1：给整个类加 @MainActor**


```swift
@MainActor
class ViewModel: ObservableObject {
    @Published var text: String = "加载中..."
    
    func fetchData() async {
        let url = URL(string: "https://example.com/data")!
        
        do {
            let (data, _) = try await URLSession.shared.data(from: url)
            let newText = String(data: data, encoding: .utf8) ?? "加载失败"
            
            text = newText  // ✅ @MainActor 让这行代码在主线程执行
        } catch {
            text = "获取失败"
        }
    }
}
```


✅ 这里 `text = newText` **一定会在主线程上执行**，不会导致 UI 崩溃。
✅ **整个 ViewModel 都在主线程执行**，不用担心线程安全问题。



#### **方法 2：只修饰 fetchData()**


如果你不想让整个 `ViewModel` 在主线程运行，只想确保 UI 更新在主线程执行，可以这样做：


```swift
class ViewModel: ObservableObject {
    @Published var text: String = "加载中..."
    
    func fetchData() async {
        let url = URL(string: "https://example.com/data")!
        
        do {
            let (data, _) = try await URLSession.shared.data(from: url)
            let newText = String(data: data, encoding: .utf8) ?? "加载失败"
            
            await updateText(newText)  // ✅ 确保在主线程执行 UI 更新
        } catch {
            await updateText("获取失败")
        }
    }
    
    @MainActor
    func updateText(_ newText: String) {
        text = newText  // ✅ 这行代码一定会在主线程执行
    }
}
```


✅ 这里 `fetchData()` 可以在后台线程执行，提高性能。
✅ 只有 `updateText(_:)` **必须在主线程**，保证 UI 更新的安全性。



### **总结**


1. **为什么 UI 更新必须在主线程？**

- UIKit 和 SwiftUI **不支持在后台线程更新 UI**，否则可能会崩溃或界面异常。
2. **@MainActor 如何解决 UI 线程问题？**

- **保证修饰的代码（类、方法、属性）始终在主线程执行**，防止 UI 崩溃。
3. **如何使用 @MainActor？**

- **方法 1**：如果整个 `ViewModel` 都会更新 UI，直接 `@MainActor` 修饰类。
- **方法 2**：如果只有部分方法需要在主线程执行，给这些方法加 `@MainActor`。

这样，你就能在 Swift 的并发环境下，**安全地更新 UI，而不会发生线程安全问题**！🚀
