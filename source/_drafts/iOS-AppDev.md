---
title: iOS_AppDev
tags:
---

## swiftUI

- You’ve used the @State and @Binding property wrappers to define a **value type** as a source of truth for triggering updates in your view hierarchy. In this article, you’ll learn how to define a **reference type** as a source of truth for your app’s user interface.
  
## swift keyword

- associatedtype

**An associated type gives a placeholder name to a type that's used as part of the protocol.**

```swift
protocol Vehicle {

    var name: String { get }

    associatedtype FuelType
    func fillGasTank(with fuel: FuelType)
}

```

```swift
struct Car: Vehicle {

    let name = "car"

    func fillGasTank(with fuel: Gasoline) {
        print("Fill \(name) with \(fuel.name)")
    }
}

struct Bus: Vehicle {

    let name = "bus"

    func fillGasTank(with fuel: Diesel) {
        print("Fill \(name) with \(fuel.name)")
    }
}

struct Gasoline {
    let name = "gasoline"
}

struct Diesel {
    let name = "diesel"
}
```

the fillGasTank(with:) function’s parameter data type of Car and Bus are not the same, Car requires Gasoline whereas Bus requires Diesel. That is why we need to define an associated type called FuelType in our Vehicle protocol.

- **some**
**The some keyword was introduced in Swift 5.1. It is used together with a protocol to create an opaque type that represents something that is conformed to a specific protocol.**

```swift
var myCar: some Vehicle = Car()
myCar = Bus() // 🔴 Compile error: Cannot assign value of type 'Bus' to type 'some Vehicle'

```

用中文表述就是，mycar该变量是car这个结构体的实例化，Car结构体遵循了Vehicle协议，mycar这个实例也遵循了这个Vehicle.只不过这个协议，有占位符，通过some关键字将他明确化了，但是他的实际类型却被隐藏了。因为Bus（）这个实例和Car（）实际上并不是同一个具体vehicle的协议. 可以理解为协议的泛型.