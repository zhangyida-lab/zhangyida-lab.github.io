---
title: swiftUI中的@state属性详解
date: 2025-03-24 21:39:49
tags: [swift编程,swiftUI]
---



# swiftUI中的@state属性详解
在 SwiftUI 中，`@State` 是一个非常重要的属性包装器，它用来声明 **视图的本地状态**，并且让 SwiftUI 知道何时 **重新渲染视图**。


### **作用**：


- **保持视图的状态**：`@State` 用来声明视图的内部状态，这些状态仅在视图内部有效。如果视图的状态发生变化，SwiftUI 会自动重新渲染该视图。
- **触发视图更新**：当被 `@State` 修饰的变量发生变化时，SwiftUI 会自动更新视图，确保界面和状态保持同步。

<!--more-->
### **基本用法**：


```swift
import SwiftUI

struct ContentView: View {
    @State private var isToggled = false // 声明状态变量

    var body: some View {
        VStack {
            Text(isToggled ? "开" : "关") // 根据状态显示不同的文本
                .font(.largeTitle)
                .padding()
            
            Toggle("开关", isOn: $isToggled) // 使用绑定的状态变量
                .padding()
        }
    }
}
```


### **解析**：


- `@State private var isToggled = false`：声明了一个状态变量 `isToggled`，它会保存视图中的状态。
- `Toggle` 视图使用 `$isToggled` 来绑定到 `isToggled` 变量，这意味着当切换开关时，`isToggled` 的值会更新。
- 当 `isToggled` 发生变化时，`Text` 视图的内容会自动更新，SwiftUI 会重新渲染界面。


### **为什么需要 @State？**


1. **保持本地状态**：

- `@State` 是一种本地状态，只会影响当前视图。你不需要担心它会影响其他视图（如全局状态或者在多个视图之间传递的状态）。
2. **自动更新界面**：

- 当你改变一个由 `@State` 修饰的变量时，SwiftUI 会知道这个状态已经变化，它会自动触发视图的更新，不需要你手动去处理界面的刷新。


### **常见应用场景**：


1. **切换按钮状态**：像上面例子中的 `Toggle`，通过切换开关来改变视图的显示内容。
2. **输入框内容**：例如处理文本框输入时，可以用 `@State` 保存用户输入的内容。

```swift
struct ContentView: View {
    @State private var text: String = "" // 保存输入框的内容

    var body: some View {
        VStack {
            TextField("输入一些文字", text: $text)
                .padding()
                .textFieldStyle(RoundedBorderTextFieldStyle())
            
            Text("你输入的是: \(text)")
                .padding()
        }
        .padding()
    }
}
```
3. **动画效果**：通过 `@State` 控制动画状态，触发视图的动态变化。

```swift
struct AnimatedView: View {
    @State private var rotation: Double = 0 // 旋转角度

    var body: some View {
        Image(systemName: "arrow.right.circle.fill")
            .rotationEffect(.degrees(rotation)) // 根据 rotation 状态旋转图标
            .onTapGesture {
                withAnimation {
                    rotation += 45 // 每次点击增加 45 度
                }
            }
            .padding()
    }
}
```


### **注意事项**：


- **视图局部状态**：`@State` 用于保存视图的 **局部状态**。如果需要多个视图之间共享状态，应该使用 `@Binding`、`@ObservedObject` 或 `@EnvironmentObject` 等。
- **数据持久性**：`@State` 的值只在当前视图的生命周期内有效。如果你重新创建视图，`@State` 中的数据会丢失。如果需要在视图之间共享状态或者持久化数据，考虑使用 `@AppStorage`、`@EnvironmentObject` 等。


### **总结**：


- `@State` 用来声明视图的 **本地状态**，并且能在状态变化时自动触发视图更新。
- 当你修改 `@State` 变量时，SwiftUI 会自动重新渲染视图，使得 UI 始终保持最新的状态。
