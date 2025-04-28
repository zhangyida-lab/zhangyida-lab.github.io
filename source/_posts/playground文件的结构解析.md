---
title: playground文件的结构解析
date: 2025-04-28 16:32:08
tags: [swift编程,playground]
---



# 📚 Playground 中的 Sources 和 Resources 作用


## 1. Sources 文件夹（源码文件夹）


- **作用**：放**Swift代码文件**，比如 `.swift` 文件。
- **特点**：


Sources 中的代码**会自动被 Playground 主体引用**，不需要 `import`。


Sources 中的代码**默认是隐藏的**（在 Playground 打开主界面时不会显示出来，需要自己点进去看）。


适合放：


工具类（比如扩展、数据结构、算法）


辅助函数


协议、模型定义（比如 `protocol Shape {}`）
- **好处**：


可以保持 Playground 主文件干净，逻辑清晰。


编译速度快（Sources 的内容在 Playground 打开时是**一次性编译**的）。

✅ **简单理解**：



Sources = 小型 "模块/库" 专门放支持代码。




### 举个例子


假设你在 Sources 里放了一个文件 `MathHelper.swift`：


```swift
// Sources/MathHelper.swift
public func square(_ x: Int) -&gt; Int {
    x * x
}
```


在 Playground 主体中直接就可以使用：


```swift
let result = square(5)
print(result) // 输出 25
```


**注意**：
Sources 里的函数或类型，要加 `public` 修饰，否则主 Playground 文件访问不到！



## 2. Resources 文件夹（资源文件夹）


- **作用**：放**资源文件**，比如：


图片（.png, .jpg）


JSON 文件


音频（.mp3）


视频（.mp4）


甚至小的数据库文件（.sqlite）
- **特点**：


Resources 中的文件可以通过代码访问，比如 `Bundle.main.url(forResource:)`


加载这些文件时，就像访问 App 的资源包一样。
- **适合放**：


图片素材


测试数据（比如假数据 json）


小型静态资源

✅ **简单理解**：



Resources = 给你的 Playground 项目提供素材支持的地方。




### 举个例子


假设你放了一张图 `logo.png` 在 Resources 里。


你可以这样加载：


```swift
import UIKit
import PlaygroundSupport

if let url = Bundle.main.url(forResource: "logo", withExtension: "png"),
   let data = try? Data(contentsOf: url),
   let image = UIImage(data: data) {
    
    // 展示图片
    let imageView = UIImageView(image: image)
    PlaygroundPage.current.liveView = imageView
}
```


这样就可以在 Playground 里直接显示图片了！



# 🔥 总结一句话


| 文件夹 | 主要放什么 | 作用 |
| ---- | ---- | ---- |
| Sources | Swift 代码（模块、工具类、模型） | 组织代码结构，保持主界面简洁，优化编译速度 |
| Resources | 图片、JSON、音频等静态资源 | 提供程序运行时需要用到的外部素材 |


# 📚 Playground 中 "每个 Page 的 Sources/Resources" vs "总项目的 Sources/Resources" 区别



# 1. Playground 总体结构


先看最顶层（也叫 Playground 的 **根目录**）通常是这样：


```
MyPlayground.playground
├── Contents.swift （入口页）
├── Sources/    （全局的 Swift 源码）
├── Resources/  （全局的资源文件）
├── Pages/
│   ├── Page1.playgroundpage/
│   │   ├── Contents.swift
│   │   ├── Sources/    （Page1局部源码）
│   │   ├── Resources/  （Page1局部资源）
│   ├── Page2.playgroundpage/
│   │   ├── Contents.swift
│   │   ├── Sources/    （Page2局部源码）
│   │   ├── Resources/  （Page2局部资源）
```


看出来了吧？
每个 **Page** 自己也可以有一套 `Sources/` 和 `Resources/`！



# 2. 根 Sources / 根 Resources（顶层）


| 项目 | 用途 |
| ---- | ---- |
| 根 Sources/ | 供 **整个 Playground 所有 Pages** 公用的 Swift 代码。 |
| 根 Resources/ | 供 **整个 Playground 所有 Pages** 公用的资源文件（图片、json、视频等）。 |


✅ 特点：


- 任何 Page 都可以直接访问根 Sources/Resources。
- 根 Sources 里的 `.swift` 文件要加 `public` 才能在 Page 里被访问到。


# 3. Page 内自己的 Sources / Resources（局部）


| 项目 | 用途 |
| ---- | ---- |
| Page 自己的 Sources/ | **只在这一页（Page）内部**使用的小模块、小类。其他 Pages 看不到。 |
| Page 自己的 Resources/ | **只在这一页（Page）内部**使用的资源（比如这个Page专属的一张图）。 |


✅ 特点：


- 只在自己那一页能访问，**其他 Pages 无法访问**。
- 如果两个 Page 都要用，就应该放到 **根目录**的 Sources/Resources。
- 好处是可以避免污染，比如 Page1 有专用测试数据，Page2不会被干扰。


# 🔥 举例对比一下


## （1）如果你在 Playground 根 Sources 里有个文件：


```swift
// Sources/MathHelper.swift
public func add(_ a: Int, _ b: Int) -&gt; Int {
    a + b
}
```


那么在 **任何 Page** 都能写：


```swift
let result = add(3, 4)
```


都能用，没问题。



## （2）如果你在 Pages/Page1.playgroundpage/Sources/LocalHelper.swift 里写：


```swift
// Pages/Page1.playgroundpage/Sources/LocalHelper.swift
public func localAdd(_ a: Int, _ b: Int) -&gt; Int {
    a + b
}
```


那么这个 `localAdd` 只在 **Page1** 里能用，在 **Page2** 用不了。


如果你在 Page2 里写：


```swift
let r = localAdd(2, 5) // ❌ 报错：找不到 localAdd
```


就会出错！



# 4. 最常见的合理用法是这样：


| 内容 | 放到哪里 | 原因 |
| ---- | ---- | ---- |
| 通用工具类（比如通用数学、通用模型） | 根 Sources/ | 所有 Pages 都可以共用 |
| 只在某一页使用的小代码、小资源 | Page 自己的 Sources/Resources/ | 避免污染别的 Page，隔离干净 |
| 所有页面都需要访问的图片、json数据 | 根 Resources/ | 公共资源方便管理 |
| 某个页面特有的测试图片、专属小数据 | Page 局部 Resources/ | 避免不同Page相互影响 |



# 🎯 总结一张表（直接记住）


| 比较项 | 顶层 Sources/Resources | Page 局部 Sources/Resources |
| ---- | ---- | ---- |
| 作用范围 | 全 Playground 共享 | 只限当前 Page |
| 访问权限 | 任何 Page 都能用 | 只有本 Page 可以用 |
| 适合放 | 通用代码/通用资源 | 特定页用的小模块/小资源 |
| 示例 | 通用数学库 / 公共图片 | 特定 Page 的小组件 / 测试数据 |



# ✅ 最后用一句话帮你记忆



**根Sources/Resources是"全局共享"的，Page下Sources/Resources是"本地专用"的。**

