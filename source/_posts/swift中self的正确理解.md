---
title: swift中self的正确理解
date: 2025-03-02 14:55:13
tags:
---



 # swift中self的正确理解


 在 Swift 中，`self` 主要有以下几种用法，具体取决于上下文：

### 1. **区分属性和局部变量**

在实例方法或构造方法中，如果参数或局部变量的名称与实例属性相同，`self` 用于区分它们。例如：

```swift
class Person {
    var name: String

    init(name: String) {
        self.name = name  // self.name 指的是实例属性，而 name 是方法参数
    }
}
```

如果不加 `self`，Swift 默认会优先使用作用域内的局部变量或方法参数，而不是实例属性。

### 2. **在闭包中显式捕获 self**

在闭包内部访问实例属性或方法时，通常需要显式使用 `self`，尤其是在 **逃逸闭包（escaping closure）** 中，以避免强引用循环（retain cycle）：

```swift
class ViewController {
    var titleText = "Hello"

    func fetchData(completion: @escaping () -> Void) {
        DispatchQueue.global().async {
            print(self.titleText)  // 必须显式使用 self
            completion()
        }
    }
}
```

如果闭包会长期持有 `self`，可以使用 `[weak self]` 或 `[unowned self]` 来防止循环引用：

```swift
class ViewController {
    var titleText = "Hello"

    func fetchData(completion: @escaping () -> Void) {
        DispatchQueue.global().async { [weak self] in
            guard let self = self else { return }
            print(self.titleText)
            completion()
        }
    }
}
```

### 3. **在扩展（extension）中调用自身方法**

在扩展中调用自身的方法或属性时，可以省略 `self`，但如果在闭包内部访问，则通常需要显式使用 `self`。

```swift
extension Person {
    func printName() {
        print(self.name)  // 这里 self 可加可不加
    }
}
```

### 4. **在类型方法或静态方法中使用 self 访问类型本身**

在 **静态方法（static method）** 或 **类方法（class method）** 中，`self` 代表的是类型本身，而不是实例。

```swift
class MathUtil {
    static var pi = 3.14159

    static func printPi() {
        print(self.pi)  // 这里 self 指的是 MathUtil
    }
}
```

如果是 `class` 修饰的方法，`self` 允许在子类中重写访问：

```swift
class Parent {
    class func describe() {
        print("I am \(self)")  // self 代表调用此方法的类型
    }
}

class Child: Parent { }

Child.describe()  // 输出：I am Child
```

### 5. **在构造方法中调用其他构造方法**

在同一个类的构造方法中，可以使用 `self.init(...)` 来调用另一个构造方法（**构造方法委托**）：

```swift
class Rectangle {
    var width: Double
    var height: Double

    init(width: Double, height: Double) {
        self.width = width
        self.height = height
    }

    convenience init(size: Double) {
        self.init(width: size, height: size)  // 调用指定构造方法
    }
}
```

### 6. **作为当前实例的引用传递**

在某些情况下，我们可以将 `self` 作为参数传递给其他方法或闭包：

```swift
class Car {
    func start() {
        print("Car started")
    }

    func setup(action: (Car) -> Void) {
        action(self)  // 传递当前实例
    }
}

let myCar = Car()
myCar.setup { car in
    car.start()  // 访问实例方法
}
```

### 7. **在结构体（struct）或枚举（enum）中修改自身**

由于结构体（`struct`）和枚举（`enum`）是值类型，想要在方法中修改 `self`，必须用 `mutating` 关键字：

```swift
struct Counter {
    var count = 0

    mutating func increment() {
        self.count += 1  // 需要 `mutating` 允许修改自身
    }
}
```

### 8. **在协议默认实现中访问 self**

在协议扩展中，`self` 可以指代符合协议的当前实例：

```swift
protocol Describable {
    func describe() -> String
}

extension Describable {
    func printDescription() {
        print(self.describe())  // self 代表当前实例
    }
}

struct Person: Describable {
    var name: String

    func describe() -> String {
        return "My name is \(name)"
    }
}

let p = Person(name: "Alice")
p.printDescription()  // 输出：My name is Alice
```

## 总结

- **实例属性和方法**：使用 self 以区分局部变量和实例属性。
- **闭包中使用 self**：在逃逸闭包中访问 self 时，需显式使用，并考虑 [weak self] 避免循环引用。
- **类型方法中使用 self**：指代类型本身（如 static 和 class 方法）。
- **构造方法中调用其他构造方法**：用 self.init(...)。
- **结构体/枚举中修改 self**：方法必须加 mutating。
- **作为参数传递**：可以用 self 传递当前实例。
在大多数情况下，`self` 可以省略，除非有歧义或必须显式使用。