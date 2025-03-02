---
title: swift中类的初始化
date: 2025-03-02 13:04:05
tags:
---



 # swift 中类的初始化 

 在 Swift 中，类的初始化是通过 **构造器（initializer）** 来完成的。Swift 提供了多种初始化方式，主要包括：

## 1. **基本构造器**

Swift 类中的 **init 方法** 负责初始化实例的所有属性。

```swift
class Person {
    var name: String
    var age: Int
    
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
}

let p = Person(name: "Tom", age: 25)
print(p.name)  // Tom
print(p.age)   // 25
```

⚠️ **必须初始化所有非可选属性**，否则会报错。

## 2. **默认构造器（Default Initializer）**

如果所有属性都有默认值，Swift 会提供一个默认的无参数构造器。

```swift
class Car {
    var brand = "Toyota"
    var year = 2020
}

let c = Car()  // 自动使用默认构造器
print(c.brand)  // Toyota
```

## 3. **可选属性的初始化**

可选属性（`?`）默认是 `nil`，可以不用在 `init` 里赋值：

```swift
class Book {
    var title: String
    var author: String?
    
    init(title: String) {
        self.title = title
    }
}

let b = Book(title: "Swift Programming")
print(b.author)  // nil
```

## 4. **自定义构造器（Custom Initializer）**

可以创建多个构造器来适应不同的初始化需求：

```swift
class User {
    var name: String
    var age: Int

    init() {  // 无参构造器
        self.name = "Default"
        self.age = 18
    }

    init(name: String, age: Int) {  // 带参数构造器
        self.name = name
        self.age = age
    }
}

let u1 = User()  // 使用无参构造器
let u2 = User(name: "Alice", age: 22)  // 使用带参数构造器
```

## 5. **convenience 便利构造器**

用于在类中提供更简洁的初始化方式，但最终必须调用 **指定构造器（designated initializer）**。

```swift
class Animal {
    var name: String
    var type: String

    init(name: String, type: String) {  // 指定构造器
        self.name = name
        self.type = type
    }

    convenience init(name: String) {  // 便利构造器
        self.init(name: name, type: "Unknown")
    }
}

let a = Animal(name: "Dog")
print(a.type)  // Unknown
```

`convenience` 不能直接初始化属性，必须调用另一个 `init` 方法。

## 6. **必需构造器 (required)**

如果希望所有子类都必须实现某个构造器，可以使用 `required` 关键字：

```swift
class Vehicle {
    var speed: Int
    
    required init(speed: Int) {
        self.speed = speed
    }
}

class Bike: Vehicle {
    required init(speed: Int) {
        super.init(speed: speed)
    }
}
```

**子类必须实现 required 构造器，否则会编译错误。**

## 7. **Failable Initializer（可能失败的构造器）**

使用 `init?` 或 `init!` 允许构造器返回 `nil`（初始化失败）。

```swift
class Product {
    var name: String
    
    init?(name: String) {  
        if name.isEmpty {
            return nil  // 初始化失败
        }
        self.name = name
    }
}

let p1 = Product(name: "")  // nil
let p2 = Product(name: "iPhone")  // 正常初始化
```

## 8. **deinit 析构器**

当对象被销毁时，`deinit` 会被调用：

```swift
class FileManager {
    init() {
        print("FileManager initialized")
    }
    
    deinit {
        print("FileManager deallocated")
    }
}

var file: FileManager? = FileManager()  
file = nil  // 释放对象，触发 `deinit`
```

### 结论：

Swift 的类初始化方式丰富，主要方式包括：

1. **普通构造器 init**
2. **默认构造器（适用于有默认值的属性）**
3. **可选属性初始化**
4. **多个自定义构造器**
5. **convenience 便利构造器**
6. **required 必需构造器**
7. **init? 可能失败的构造器**
8. **deinit 析构器**
这些特性使得 Swift 的类初始化既灵活又安全！🎯
 # 用户问题 
 类的初始化的底层原理 
 # chatGPT 
 ### **Swift 类的初始化底层原理**

在 Swift 中，类的初始化是一个复杂的过程，涉及 **内存分配、初始化流程、继承体系、动态派发** 等底层机制。我们可以从以下几个方面来理解 Swift 类初始化的底层原理。

## **1. 类的内存分配**

在 Swift 中，类是 **引用类型（Reference Type）**，对象的存储方式和结构体不同：

- **类的实例存储在堆（Heap）中**，由 ARC（自动引用计数）管理。
- **实例变量（属性）存储在对象的内存块中**。
- **对象的 vtable（方法表）存储在类的元数据中**，用于动态派发方法调用。
**初始化时的内存分配过程：**

1. **swift_allocObject 分配内存**（来自 Swift Runtime）。
2. **零初始化内存**，所有存储属性设置为默认值（如 nil、0 等）。
3. **调用构造器 init()**，设置实际属性值。
```swift
class Person {
    var name: String
    var age: Int
    
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
}

let p = Person(name: "Tom", age: 25) // 堆上分配内存，调用 init()
```

在汇编层面，Swift 使用 **swift_allocObject** 进行对象分配：

```assembly
swift_allocObject(typeMetaData, size, align)
```

- typeMetaData：类型元数据（metadata），用于存储 vtable 和类型信息。
- size：对象的大小（根据属性计算）。
- align：对齐方式。
## **2. Swift 类的初始化流程**

Swift 采用 **两阶段初始化（Two-Phase Initialization）** 来保证对象的安全性。

### **阶段 1：存储属性初始化**

1. 调用 **swift_allocObject** 分配对象的内存，并设置默认值。
2. 依次调用类层级结构中的 **init** 方法，初始化属性。
```swift
class A {
    var x: Int
    init(x: Int) {
        self.x = x
        print("A init")
    }
}

class B: A {
    var y: Int
    init(x: Int, y: Int) {
        self.y = y  // ✅ 子类属性必须先初始化
        super.init(x: x)  // ✅ 然后调用父类的 `init`
        print("B init")
    }
}

let obj = B(x: 10, y: 20)
/* 输出：
 A init
 B init
*/
```

规则：

- **子类的存储属性必须先初始化**，再调用 super.init() 。
- **父类的 init 只能在子类的属性初始化完成后调用**。
### **阶段 2：对象完全初始化**

1. init 方法执行完成，实例可用。
2. 如果 init 方法是 required，子类必须重写。
3. convenience init 只是辅助构造器，最终要调用 designated init。
## **3. Swift 的初始化方法在底层的调用过程**

Swift 的 `init()` 方法在底层是 **C++ vtable（虚函数表）+ Swift 元数据** 结合的实现。

例如，Swift 类在底层被转换为类似 C++ 结构：

```cpp
struct Person {
    void* isa; // 指向类的元数据
    char* name;
    int age;
};
```

在 `init()` 过程中，Swift 进行如下操作：

1. **swift_allocObject 申请内存**
2. **默认初始化 self**
3. **调用 init() 构造器**
4. **填充 self 的属性**
5. **返回对象**
在汇编层面：

```assembly
mov r0, #size    // 分配 size 大小的内存
bl swift_allocObject // 调用 swift_allocObject 分配对象
str r0, [sp, #self] // 存储 self 指针
bl _T05PersonC5inityACSS_SitcfC // 调用 init()
```

## **4. convenience init 的底层调用**

Swift 的 **convenience init 只是语法糖**，它最终会调用 `init()`。

示例：

```swift
class Car {
    var brand: String
    var year: Int

    init(brand: String, year: Int) {
        self.brand = brand
        self.year = year
    }

    convenience init(brand: String) {
        self.init(brand: brand, year: 2020) // 必须调用另一个 init()
    }
}
```

底层逻辑：

1. convenience init **不会直接初始化存储属性**，必须调用 init()
2. init() 先执行，再执行 convenience init
在底层的调用链：

```assembly
bl _T05CarC5inityACSS_SitcfC   // 调用 init(brand:year:)
bl _T05CarC5inityACSS_tcfc    // convenience init 调用 init()
```

## **5. required init 在底层的实现**

`required` 关键字确保 **所有子类都必须实现** 该构造器。

```swift
class Animal {
    var name: String
    required init(name: String) {
        self.name = name
    }
}

class Dog: Animal {
    required init(name: String) {
        super.init(name: name)
    }
}
```

在底层：

- required init 通过 **vtable 进行动态分发**，以确保子类必须实现。
在 Swift Runtime 中：

```cpp
struct AnimalVTable {
    void (*required_init)(Animal*, String);
};
```

在汇编层面：

```assembly
bl _T06AnimalC5inityACSScfc   // 调用 Animal 的 init()
bl _T03DogC5inityACSScfc      // 调用 Dog 的 init()
```

由于 Swift 运行时支持 **动态派发**，`required init` 确保子类必须实现它。

## **6. deinit 析构器的底层实现**

Swift 使用 ARC 管理对象，当引用计数为 `0` 时，调用 `deinit` 释放对象。

```swift
class File {
    var name: String
    init(name: String) { self.name = name }
    
    deinit {
        print("File \(name) is being deinitialized")
    }
}

var file: File? = File(name: "log.txt")
file = nil  // 触发 deinit()
```

**底层调用**

1. swift_release(self) 递减引用计数。
2. deinit() 触发清理逻辑。
3. swift_deallocObject(self) 释放内存。
在汇编层面：

```assembly
bl swift_release  // 递减引用计数
cmp r0, #0        // 判断是否为 0
beq _deinit       // 如果为 0，调用 deinit
bl swift_deallocObject // 释放对象
```

## **总结**

1. **Swift 类的初始化是两阶段的**：
- 第一阶段：分配内存 + 初始化存储属性。
- 第二阶段：完成对象初始化，允许方法调用。
2. **底层依赖 swift_allocObject 进行内存分配**，并通过 **vtable + Swift 元数据** 进行方法分发。
3. **convenience init 只是语法糖，最终必须调用 init()**。
4. **required init 强制子类实现，底层通过 vtable 进行动态派发**。
5. **deinit 依赖 ARC 进行对象释放，调用 swift_release 和 swift_deallocObject**。
Swift 的初始化机制**兼顾了安全性和性能**，避免了未初始化对象的访问错误，并且优化了类的内存管理。🚀