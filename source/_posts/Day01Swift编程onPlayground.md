---
title: Day01Swift编程onPlayground
date: 2025-04-26 22:20:17
tags: [swift编程,iOS编程]
---

**今天 Day 1 推荐：**


### 1. Swift 语言 - 协议+扩展练习


```swift
protocol Shape {
    var area: Double { get }
}

struct Circle: Shape {
    var radius: Double
    var area: Double {
        return Double.pi * radius * radius
    }
}

struct Rectangle: Shape {
    var width: Double
    var height: Double
    var area: Double {
        return width * height
    }
}

extension Array where Element: Shape {
    func totalArea() -> Double {
        self.reduce(0) { $0 + $1.area }
    }
}

// 测试
let shapes: [Shape] = [
    Circle(radius: 3),
    Rectangle(width: 4, height: 5)
]

print("Total area: \(shapes.totalArea())")
```


**练习点：** 协议+扩展+泛型约束

<!--more-->

### 2. SwiftUI - 简单的自定义动画按钮


```swift
import SwiftUI
import PlaygroundSupport

struct BouncyButton: View {
    @State private var isPressed = false

    var body: some View {
        Button(action: {
            withAnimation(.spring(response: 0.3, dampingFraction: 0.5)) {
                isPressed.toggle()
            }
        }) {
            Text("Press Me")
                .font(.title)
                .padding()
                .background(isPressed ? Color.green : Color.blue)
                .foregroundColor(.white)
                .clipShape(Capsule())
                .scaleEffect(isPressed ? 1.2 : 1.0)
        }
    }
}

// 预览
PlaygroundPage.current.setLiveView(BouncyButton())
```


**练习点：**


- SwiftUI 动画（`withAnimation`、`spring`）
- `State`变量控制
- 组件封装




代码解释：



### 1. 定义一个协议 Shape


```swift
protocol Shape {
    var area: Double { get }
}
```


- `protocol` 是 Swift 里面定义规范的一种方式。
- `Shape` 这个协议要求**所有遵循它的类型**必须有一个只读属性 `area`，类型是 `Double`。
- 这就像是给所有“形状”下了规定：“你必须能告诉我你的面积”。


### 2. 定义两个遵循 Shape 协议的结构体


#### 圆形 Circle：


```swift
struct Circle: Shape {
    var radius: Double
    var area: Double {
        return Double.pi * radius * radius
    }
}
```


- `Circle` 有个属性 `radius` 表示半径。
- 它实现了协议要求的 `area`，用公式 **π × 半径²** 算面积。

#### 矩形 Rectangle：


```swift
struct Rectangle: Shape {
    var width: Double
    var height: Double
    var area: Double {
        return width * height
    }
}
```


- `Rectangle` 有 `width`（宽度）和 `height`（高度）。
- 它的 `area` 就是 **宽 × 高**。


### 3. 给数组加功能：扩展 Array


```swift
extension Array where Element: Shape {
    func totalArea() -> Double {
        self.reduce(0) { $0 + $1.area }
    }
}
```


这里很重要，稍微细一点解释：


- `extension Array where Element: Shape`
➔ 只给**数组元素是 Shape 类型**的数组加新功能。
- `func totalArea() -> Double`
➔ 新加一个方法叫 `totalArea()`，计算**所有形状面积之和**。
- `self.reduce(0) { $0 + $1.area }`
➔ `reduce` 是一个高阶函数，意思是：


从 `0` 开始


把数组里面每个元素的 `area` 加起来


最终得出一个总面积

**简化理解**：`totalArea()` = 把数组里每个 `Shape` 的面积加在一起！



### 4. 测试一下


```swift
let shapes: [Shape] = [
    Circle(radius: 3),
    Rectangle(width: 4, height: 5)
]

print("Total area: \(shapes.totalArea())")
```


- 定义了一个 `shapes` 数组，里面有一个半径是 3 的圆，和一个宽4高5的矩形。
- 调用 `totalArea()`，求这两个形状的总面积并打印出来。


### 总结一下


| 元素 | 含义 |
| ---- | ---- |
| protocol Shape | 定了一个“形状”协议，所有形状都要能算面积。 |
| struct Circle、struct Rectangle | 实际的形状，实现了Shape协议。 |
| extension Array | 扩展数组，让**装 Shape 的数组**能直接求总面积。 |
| reduce | 用来把所有面积加起来，得出总面积。 |







# 详细解释这段代码 self.reduce(0) { $0 + $1.area }

### 先看整体：


```swift
self.reduce(0) { $0 + $1.area }
```


它是在数组上调用 `reduce` 方法。


- `self` 指的是当前的数组（比如 `[Circle, Rectangle, ...]`）。
- `reduce(初始值) { 闭包 }` 是 Swift 提供的一个**高阶函数**，用来**把一个数组归并成一个值**。
- 它从一个“初始值”开始，不断把数组里的每个元素叠加/处理进去。


### 逐步解释：


#### 1. reduce(0)


意思是：


- 从数值 `0` 开始累加。
- 这个 `0` 是初始的“总面积”。

#### 2. { $0 + $1.area }


这里是闭包，里面有两个参数：


| 符号 | 代表什么意思 |
| ---- | ---- |
| $0 | 当前累加的“总和” |
| $1 | 当前正在遍历的数组元素（这里是一个 Shape 类型） |


所以这个 `{ $0 + $1.area }` 其实就是：



“把当前总和 `($0)` 加上当前元素的面积 `($1.area)`，然后作为新的总和继续往下传”。




### 一个模拟小例子


假设数组是：


```swift
[ Circle(radius: 3), Rectangle(width: 4, height: 5) ]
```


- 第一次循环：


`$0 = 0`


`$1 = Circle(radius: 3)`


`$1.area = π × 3² ≈ 28.27`


新的总和 = `0 + 28.27 = 28.27`
- 第二次循环：


`$0 = 28.27`


`$1 = Rectangle(width: 4, height: 5)`


`$1.area = 4 × 5 = 20`


新的总和 = `28.27 + 20 = 48.27`

最后返回的就是：**48.27**



### 更长一点写出来（等价形式）


如果不写得这么简洁，等价于下面这种形式：


```swift
self.reduce(0) { (currentTotal, currentShape) in
    return currentTotal + currentShape.area
}
```


这里把 `$0` 写成了 `currentTotal`，把 `$1` 写成了 `currentShape`，更好懂一些。



### 小结一句话版：


`self.reduce(0) { $0 + $1.area }`
👉 是在遍历数组，把所有 `Shape` 的 `area` 加在一起，得到总面积。



### 延伸（进阶理解）


- `reduce` 不只是用来加法，它可以用来任何「聚合」的操作，比如乘法、拼接字符串、找最大值等等。
- 这里用 `reduce` 是因为它比 `for` 循环更**简洁**，而且是**函数式编程风格**，Swift 非常推崇这种写法。


# shapes.totalArea() 该函数无法使用，提示this method is defined on array ,may not be available in context

你遇到的这个提示：



this method is defined on array and may not be available in context



就是因为 **你定义 shapes 的类型是 [Shape]**，而我们扩展的方法要求的是**元素是遵循 Shape 协议的具体类型**，而不是**协议类型本身**。


Swift 中，


- `[Shape]` 是一个元素是 **协议对象**（existential type）的数组。
- `Array where Element: Shape` 是要求元素是**具体的遵循协议的类型**（比如 `Circle`、`Rectangle`）。

这两种概念不一样！导致你的 `shapes.totalArea()` 调用失败。



### 进阶解决（标准做法）——正确扩展 [any Shape] 的 Array


如果你想保留 `extension Array` 的写法，要这样做：


```swift
extension Array where Element == any Shape {
    func totalArea() -> Double {
        self.reduce(0) { $0 + $1.area }
    }
}
```


这里用到了 Swift 的新特性，`Element == any Shape`，明确告诉编译器元素是**"符合 Shape 协议的任意对象"**。


然后你的定义：


```swift
let shapes: [any Shape] = [
    Circle(radius: 3),
    Rectangle(width: 4, height: 5)
]

print(shapes.totalArea())
```




### 最后，小总结一句话版：



Swift 的 `Array where Element: 协议` 是要求具体类型，不是协议本身。如果你用 `[Shape]`，那就是“协议对象数组”，需要用 `[any Shape]` 或换种写法。



# protocol Shape 不能当类型使用比如struct ？


**protocol Shape 本身不是一个具体类型**，不能直接当作 `struct`、`class`、或者别的值来用。
Swift 里，`protocol` 是**一组规则的集合**，而不是能直接实例化的类型。




### 那为什么有时候又看到 [Shape] 这样的写法？


因为 Swift 支持一种叫 "**协议类型（existential type）**" 的概念。


比如你可以定义：


```swift
let shapes: [Shape] = [...]
```


这其实是说：



我有一堆遵守 `Shape` 协议的对象，但我不关心它们具体是谁（可能是 `Circle`，也可能是 `Rectangle`），只关心它们都能提供 `area` 这个属性。



但这背后 Swift 是有**额外开销**的，比如动态查找 `area`，因为 Swift 运行时需要跳到具体实现上。



### 细分来看：


| 用途 | 是否可以用 protocol |
| ---- | ---- |
| 作为一组要求来让别人遵守（比如给 struct/class 写协议） | ✅可以 |
| 直接当作实例来用（像 struct 一样 new 一个） | ❌不可以 |
| 作为参数、返回值、变量（比如 [Shape]） | ✅可以，但要小心性能、特性差异 |
| 作为泛型约束（比如 T: Shape） | ✅可以，推荐 |



### Swift 5.6 以后，Swift 更严格区分了两种情况：


- `protocol Shape {}` 是**一组能力要求**，不能直接实例化。
- `[any Shape]` 才是真正明确说：「这是**一堆遵循 Shape 的对象**。」

如果你写：


```swift
let shape: Shape = Circle(radius: 3) 
```


Swift 5.7 以后，会强烈建议你写成：


```swift
let shape: any Shape = Circle(radius: 3)
```


明确加上 `any`，表示这是一个 **existential**（存在类型）。



### 那，protocol 和 struct、class的区别到底是啥？


简单对比一下：


| 特性 | protocol | struct/class |
|:---|:---|
| 是不是实体？ | ❌不是实体（只是规范） | ✅是实体（可以实例化） |
| 用来干嘛？ | 描述行为、特征 | 存储数据和具体实现 |
| 能不能实例化？ | ❌不能 | ✅能 |
| 示例 | `protocol Shape { var area: Double { get } }` | `struct Circle: Shape { ... }` |



### 总结一句话版：



`protocol` 是用来规定“必须有什么功能”，但不能直接造出来，真正能被造出来的是遵循这个协议的 `struct` 或 `class`。




### 最后给你一组完整例子，感受一下


```swift
protocol Shape {
    var area: Double { get }
}

struct Circle: Shape {
    var radius: Double
    var area: Double {
        return Double.pi * radius * radius
    }
}

let c = Circle(radius: 5) // OK，Circle可以实例化
let s: any Shape = c      // OK，Circle实例赋值给 any Shape 类型
// let s2 = Shape()       // ❌ 错误，protocol本身不能new出来！
```

