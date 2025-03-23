---
title: swift中的关键字及应用实例
date: 2025-03-22 18:00:25
tags: 
      - swift
      - 关键字
---



# swift中的关键字及应用实例

Swift 编程语言包含许多关键字（keywords），它们用于定义变量、控制流程、声明类型、管理内存等。以下是 Swift 的关键字分类及其作用：



### **1. 变量和常量**


- var —— 变量
- let —— 常量

### **2. 数据类型声明**


- class —— 定义类
- struct —— 定义结构体
- enum —— 定义枚举
- protocol —— 定义协议
- typealias —— 类型别名
- associatedtype —— 关联类型（用于协议）
- extension —— 扩展类型
- func —— 定义函数
- init —— 构造方法
- deinit —— 析构方法
- subscript —— 下标操作

### **3. 访问控制**


- private —— 仅在当前作用域可访问
- fileprivate —— 仅在当前文件可访问
- internal —— 仅在当前模块可访问（默认）
- public —— 允许外部模块访问
- open —— 允许外部模块继承和重写（比 public 更开放）

### **4. 流程控制**


- if —— 条件语句
- else —— 条件语句
- switch —— 多分支选择
- case —— switch 语句中的分支
- default —— switch 的默认分支
- guard —— 预先条件检查
- for —— 循环
- while —— 循环
- repeat —— do-while 循环
- break —— 跳出循环
- continue —— 继续下一次循环
- return —— 返回值
- fallthrough —— 继续执行下一个 case
- throw —— 抛出错误
- catch —— 捕获错误
- do —— 捕获错误的代码块
- try —— 可能抛出错误的代码
- throws —— 声明函数可能抛出错误
- rethrows —— 声明函数在其参数抛出错误时，才会抛出错误

### **5. 并发**


- async —— 异步方法
- await —— 等待异步方法返回
- actor —— 线程安全的类
- yield —— 生成器（用于 async 序列）

### **6. 运算符**


- as —— 类型转换
- as? —— 可选类型转换
- as! —— 强制类型转换
- is —— 类型检查
- try? —— 可选错误处理
- try! —— 强制错误处理

### **7. 属性和方法**


- self —— 代表当前实例
- super —— 访问父类
- static —— 静态属性或方法
- class —— 类级属性或方法（允许子类重写）
- mutating —— 允许 struct 或 enum 的方法修改 self
- nonmutating —— 结构体或枚举方法不修改 self
- required —— 强制子类实现构造器
- convenience —— 便利构造器
- final —— 禁止重写
- override —— 重写父类方法或属性
- lazy —— 延迟加载属性
- dynamic —— 允许动态派发（Objective-C 兼容）
- optional —— 可选协议方法（Objective-C 兼容）

### **8. 泛型**


- where —— 泛型约束
- some —— 返回不透明类型
- inout —— 传递参数时修改其值

### **9. 其他**


- import —— 导入模块
- nil —— 空值
- true / false —— 布尔值
- _ —— 占位符（可用于忽略值）
- #file、#line、#column、#function —— 调试信息
- #available —— 检测 API 可用性
- #selector —— 获取 Objective-C 方法选择器
- #keyPath —— 获取属性键路径


Swift 的关键字用于各种场景，掌握它们能更好地编写 Swift 代码。你具体想深入了解哪部分关键字的用法？

以下是 Swift 关键字的详细解释和代码示例，涵盖最重要的关键字及其实际应用：



## **1. 变量和常量**


### var（变量，可变）


```swift
var age = 25
age = 30  // 变量值可以更改
print(age)  // 输出 30
```


### let（常量，不可变）


```swift
let name = "Swift"
// name = "New Swift" // ❌ 错误：常量不能更改
print(name)
```



## **2. 数据类型声明**


### class（类）


```swift
class Person {
    var name: String
    init(name: String) {
        self.name = name
    }
    func greet() {
        print("Hello, \(name)!")
    }
}
let p = Person(name: "Alice")
p.greet()  // 输出：Hello, Alice!
```


### struct（结构体）


```swift
struct Point {
    var x: Int
    var y: Int
}
let point = Point(x: 10, y: 20)
print(point)
```


### enum（枚举）


```swift
enum Direction {
    case north, south, east, west
}
let dir = Direction.north
print(dir)  // 输出 north
```


### protocol（协议）


```swift
protocol Drawable {
    func draw()
}

class Circle: Drawable {
    func draw() {
        print("Drawing a circle")
    }
}
let shape = Circle()
shape.draw()  // 输出：Drawing a circle
```



## **3. 访问控制**


### private（私有，仅限本类）


```swift
class Example {
    private var secret = "Hidden"
}
```


### public（公开，可被外部访问）


```swift
public class PublicClass {
    public var name = "Open Access"
}
```



## **4. 流程控制**


### if、else


```swift
let num = 10
if num &gt; 5 {
    print("Greater than 5")
} else {
    print("Less or equal to 5")
}
```


### switch


```swift
let grade = "B"
switch grade {
case "A":
    print("Excellent!")
case "B":
    print("Good job!")
default:
    print("Try harder!")
}
```


### guard


```swift
func checkAge(_ age: Int) {
    guard age &gt;= 18 else {
        print("You are underage.")
        return
    }
    print("Welcome!")
}
checkAge(16)  // 输出：You are underage.
```



## **5. 并发**


### async、await


```swift
import Foundation

func fetchData() async -&gt; String {
    return "Data received"
}

Task {
    let result = await fetchData()
    print(result)  // 输出：Data received
}
```



## **6. 关键操作**


### as（类型转换）


```swift
let value: Any = "Hello"
if let str = value as? String {
    print(str)  // 输出：Hello
}
```


### is（类型检查）


```swift
let item: Any = 123
if item is Int {
    print("This is an integer")
}
```



## **7. 继承和方法重写**


### super


```swift
class Animal {
    func sound() {
        print("Some sound")
    }
}
class Dog: Animal {
    override func sound() {
        super.sound()
        print("Bark!")
    }
}
let dog = Dog()
dog.sound()  
// 输出：Some sound
// 输出：Bark!
```


### final（禁止继承或重写）


```swift
final class Vehicle {
    func run() {
        print("Running")
    }
}
// class Car: Vehicle {} // ❌ 错误：无法继承 `final` 类
```



## **8. 泛型**


### where（泛型约束）


```swift
func compare(a: T, b: T) -&gt; Bool where T: Equatable {
    return a == b
}
print(compare(a: 5, b: 5))  // 输出：true
```



## **9. 错误处理**


### do、try、catch


```swift
enum FileError: Error {
    case notFound
}

func readFile() throws {
    throw FileError.notFound
}

do {
    try readFile()
} catch {
    print("Error: File not found")
}
```



## **10. 其他**


### _（忽略变量）


```swift
let (_, y) = (10, 20)
print(y)  // 输出：20
```


### import（导入模块）


```swift
import Foundation
```


### #available（检测 API 版本）


```swift
if #available(iOS 15, *) {
    print("Running on iOS 15 or later")
} else {
    print("Older version")
}
```

