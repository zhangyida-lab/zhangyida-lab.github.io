---
title: swiftui中的lazyVGrid用法
date: 2025-03-29 12:12:48
tags: [swiftUI,swift编程]
---


# swiftui中LazyVGrid的原理用法及示例

`LazyVGrid` 是 SwiftUI 中用于创建垂直方向上具有惰性加载的网格视图的容器。与 `VGrid` 不同，`LazyVGrid` 在需要时才加载其内容，这有助于提升性能，尤其是当网格中包含大量内容时。这个视图在滚动过程中会按需加载和销毁视图，避免了内存的浪费。


### 原理


`LazyVGrid` 会延迟视图的创建，直到它们需要在屏幕上显示。换句话说，它会根据用户滚动位置只渲染当前屏幕内的内容，而不是一次性加载所有的元素。这个惰性加载的特性使得它特别适合展示大量数据。


### 基本用法


`LazyVGrid` 需要一个 `GridItem` 数组来定义每列的布局。每个 `GridItem` 可以指定其大小，排列方式，间距等。


### 示例


```swift
import SwiftUI

struct ContentView: View {
    let data = Array(1...100)  // 100个数据，用来展示
    
    // 定义网格列
    let columns = [
        GridItem(.flexible()),  // 每列宽度灵活
        GridItem(.flexible())
    ]
    
    var body: some View {
        ScrollView {
            LazyVGrid(columns: columns, spacing: 20) {  // 使用LazyVGrid
                ForEach(data, id: \.self) { item in
                    Text("Item \(item)")
                        .frame(height: 100)
                        .background(Color.blue)
                        .cornerRadius(10)
                        .foregroundColor(.white)
                        .padding()
                }
            }
            .padding()
        }
    }
}

@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```


### 解析


1. **数据源**：我们创建了一个简单的整数数组 `data` 来展示数据。
2. **网格列**：`columns` 数组定义了两列，每一列的宽度是灵活的，即 `GridItem(.flexible())`。你还可以使用 `.fixed()` 来指定固定的列宽，或者使用 `.adaptive(minimum:)` 来定义最小列宽，并且自动适应屏幕。
3. **ScrollView**：`LazyVGrid` 需要被包裹在 `ScrollView` 内，以便支持滚动功能。
4. **视图内容**：通过 `ForEach` 遍历 `data` 数组，为每个元素创建一个 `Text` 视图。

### 常用属性


- `columns`：定义列的布局。`GridItem` 允许你指定 `.flexible()`（灵活的宽度）、`.fixed()`（固定宽度）和 `.adaptive()`（自动适应）等。
- `spacing`：指定网格单元格之间的间距。
- `alignment`：设置网格中单元格内容的对齐方式（水平和垂直）。
- `padding`：设置网格容器的内边距。

### 高级用法


你可以通过动态调整网格列数或者配置复杂的布局来实现不同的 UI 需求。例如，使用 `adaptive` 来创建自适应列数，或者混合使用 `LazyHGrid` 和 `LazyVGrid` 创建复杂的网格布局。


```swift
let columns = [
    GridItem(.adaptive(minimum: 100))  // 最小宽度为100，自动调整列数
]
```


### 性能优化


使用 `LazyVGrid` 是进行大数据量展示时的最佳实践，因为它只会渲染当前屏幕内可见的内容，节省了内存和计算资源。

