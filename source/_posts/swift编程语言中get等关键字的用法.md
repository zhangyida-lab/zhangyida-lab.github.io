---
title: swift编程语言中get等关键字的用法
date: 2025-04-26 21:32:10
tags: [swift编程,iOS编程]
---



# 关于get和set的代码示例：

```swift
struct Temperature {
 var celsius: Float = 0.0
 var fahrenheit: Float {
 get {
 return ((celsius * 1.8) + 32.0)
 }
 set {
 self.celsius = ((newValue - 32) / 1.8)
 }
 }
}
<!--more-->


var temp = Temperature(celsius: 40)
print(temp.fahrenheit)
temp.fahrenheit = 55
print(temp.celsius)
```

 ## 这段 Swift 代码定义了一个叫 `Temperature` 的**结构体**，它实现了摄氏度（`celsius`）和华氏度（`fahrenheit`）之间的相互转换。我们来一步步解释：



### 结构体部分


```swift
struct Temperature {
    var celsius: Float = 0.0
```


- 这里定义了一个属性 `celsius`，表示**摄氏温度**，默认是 `0.0`。

```swift
    var fahrenheit: Float {
        get {
            return ((celsius * 1.8) + 32.0)
        }
        set {
            self.celsius = ((newValue - 32) / 1.8)
        }
    }
}
```


- `fahrenheit` 是一个**计算属性**（注意：它没有直接存储值，而是通过 `celsius` 动态计算出来的）。
- `get`：读取 `fahrenheit` 时，根据摄氏温度计算出华氏温度：
`华氏温度 = 摄氏温度 × 1.8 + 32`
- `set`：设置 `fahrenheit` 时，自动根据输入的华氏温度**反推摄氏温度**：
`摄氏温度 = (华氏温度 - 32) ÷ 1.8`
- `newValue` 是 Swift 自动提供的，代表**新设置的值**。


### 使用部分


```swift
var temp = Temperature(celsius: 40)
print(temp.fahrenheit)
```


- 创建了一个 `Temperature` 实例 `temp`，初始摄氏温度是 40°C。
- 打印 `temp.fahrenheit`，会触发 `get`，计算出对应的华氏温度：
`40 * 1.8 + 32 = 104`

所以这里打印出来的是：


```text
104.0
```



```swift
temp.fahrenheit = 55
print(temp.celsius)
```


- 给 `temp.fahrenheit` 赋值为 55，这会触发 `set`，反过来计算新的摄氏温度：
`celsius = (55 - 32) / 1.8 = 12.777...`
- 再打印 `temp.celsius`，所以输出：

```text
12.777778
```


（浮点数打印可能会带很多小数位）



### 总结


这段代码实现了：


- 根据摄氏度动态计算华氏度。
- 根据华氏度反推摄氏度。
- **celsius** 是实际存储的值，**fahrenheit** 是根据 `celsius` 实时计算出来的。


要不要我顺便帮你画一张小图，展示 `celsius` 和 `fahrenheit` 之间的流转关系？🎯
（要的话告诉我哈～）

# get 和set的其他用法吗？或者willset didset等。

`get` 和 `set` 在 Swift 里除了你刚看到的这种基本用法，还有一些**更灵活**、**更高级**的用法。简单说一下几个常见的：



### 1. **只读计算属性**（只有 get，没有 set）


如果你只需要读取，不需要修改，可以只写 `get`，而且 **可以省略 get 关键字**！


```swift
struct Rectangle {
    var width: Double
    var height: Double
    
    var area: Double {
        // 省略了 get
        width * height
    }
}

let rect = Rectangle(width: 10, height: 5)
print(rect.area)  // 输出 50
```



这里 `area` 是只读的，不能直接改，只能通过改 `width` 或 `height` 间接改变。




### 2. **带 private 的 set**（读是公开的，写是私有的）


有时候你希望外部代码能读这个属性，但**不能随便改**，可以给 `set` 加上 `private`。


```swift
struct BankAccount {
    private(set) var balance: Double = 0
    
    mutating func deposit(_ amount: Double) {
        balance += amount
    }
}

var account = BankAccount()
account.deposit(100)
print(account.balance)  // 可以读
// account.balance = 500  ❌ 直接改会报错
```



### 3. **属性观察器 willSet 和 didSet**（不同于 get/set，但相关）


除了 `get/set`，Swift 还有**属性观察器**，让你在**属性即将改变前/改变后**执行代码：


```swift
struct StepCounter {
    var steps: Int = 0 {
        willSet(newSteps) {
            print("准备将步数改为 \(newSteps)")
        }
        didSet {
            print("步数增加了 \(steps - oldValue)")
        }
    }
}

var counter = StepCounter()
counter.steps = 100
```


输出：


```
准备将步数改为 100
步数增加了 100
```


- `willSet` 在新值赋值之前触发
- `didSet` 在新值赋值之后触发
- `oldValue` 是修改前的旧值


### 小结


| 用法 | 说明 |
| ---- | ---- |
| get / set | 计算属性，读写控制 |
| 只写 get | 只读属性，不能直接设置 |
| private set | 外部只能读，内部可以写 |
| willSet / didSet | 属性变化前后做一些操作 |
