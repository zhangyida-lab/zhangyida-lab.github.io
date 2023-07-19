---
title: SwiftQA
date: 2023-05-05 15:53:10
tags: softwareDevelopment
---
> Brevity is the soul of wit.
![SwiftQA](PLQA.jpg)

## How to use swift to development an ios APP

## The idea of this application

## What is first-class type ?

Functions are a first-class type. This means that a function can return another function as its value.

## What is type-safe language?
<!--more-->
Swift is a type-safe language, which means the language helps you to be clear about the types of values your code can work with. If part of your code requires a String, type safety prevents you from passing it an Int by mistake. Likewise, type safety prevents you from accidentally passing an optional String to a piece of code that requires a non-optional String. Type safety helps you catch and fix errors as early as possible in the development process

“An algorithm is a set of instructions for accomplishing a task”

“There are three essential building blocks in all algorithms: sequencing, selection, and iteration”

Organizing Data
“Instances, Methods, and Properties”

“Much of the code you write for an app is event-based, executing in response to a constantly changing environment.
”

“Apps receive all kinds of input: touches on their screens, files, sensor input (such as a camera), data from the internet. Inputs can even come from other programs. For example, you can use the Share sheet in your Photos app to send a photo to another editing app, or to include it in a message to your friend. When it comes to output, apps can return images, sounds, music, text, tactile sensations, and animations.
”

## what is clourse?

```swift
{(parameters) -> return type in
 // Code
 }
```

Closures can take advantage of Swift’s type inference system, so you can clean up your closure even more by trimming
out the type information.

Closures that appear at the end of the argument list can be written outside of and after the function’s
parentheses; this is called trailing closure syntax.
If doing so would leave an empty pair of parentheses
behind, you may remove them entirely.

Notice that when the closure moves outside the parentheses, its argument label is removed from the
call. If there are multiple trailing closures, this only applies to the first; subsequent trailing closures
retain their argument labels. For example, a function whose signature looks like this:

```swift
func doAwesomeWork(on input: String,
 using transformer: () -> Void,
then completion: () -> Void)
```

```swift

Would be called using trailing closure syntax like this:

```swift
doAwesomeWork(on: "My Project") {
print("Doing work on \(input) in `transformer`")
} then: {
print("Finishing up in `completion`")
}
```

## wuat is Initialization?

Initialization is the operation of setting up an instance of a type. It entails giving each stored property
an initial value and may involve other preparatory work. After this process, the instance is prepared and
available to use.

**But unlike other methods, initializers do not return values. Instead, initializers are tasked with giving values to a type’s stored properties.**

### Default initializers for structs

Structures can have both **default** and **custom initializers**

- an empty initializer (an initializer without parameters) provided to you by the Swift compiler automatically

```swift
init() {

 }
```

- A *memberwise initializer* has a parameter for each stored property on the instance, and it will use default parameter syntax to provide any default values you have declared on the type. This allows you to decide which parameters you wish to provide at the call site.

```swift
init(population: Int = 5422, numberOfStoplights: Int = 4) {
 self.population = population
 self.numberOfStoplights = numberOfStoplights
 }
```

The types that you have been creating up to this point have all been created in more or less the same
way: Properties were either given default stored values or their values were computed on demand.
Initialization was not customized, and we did not give it much consideration.
It is very common to want control over how an instance of a type is created. For example, you have
been giving default values to an instance’s stored properties and then changing the properties’ values
after you create the instance. This strategy is inelegant. It would be ideal for the instance to have all the
correct values in its properties immediately. Initializers help you create an instance with the appropriate
values.

one of the principal goals of initialization is to give all the type’s stored properties values
so that the new instance is ready to use. The compiler will enforce the requirement that your new
instance have values in its stored properties. If you do not provide an initializer for your custom struct,
you must provide the necessary values through default values or memberwise initialization

To tell the compiler “I intend to equate two instances of this type,” you start by declaring that your type
wants to conform to the Equatable protocol

Free default memberwise initializers are a benefit of structs; they are not available on classes

## Hashable

Hashability has a straightforward purpose: the ability of a type to generate an integer based on its content

If you want to know whether two strings are equal, you can first check their hashes, which takes nearly
no time at all. If their hashes are different, then you know the strings are different.
But, because strings can be larger and more complex than integers, it is possible for two different
strings to have the same hash. So, if two strings’ hashes are the same, you have not learned anything,
and you must proceed with a complete equality check to be sure.
(As an aside: Because an equality check is a necessary backup plan when comparing the hashes of
instances, Hashable inherits from Equatable.)

An ideal hashing algorithm is one that is fast to compute and unlikely to collide with another instance’s
hash – but does not need to guarantee that two hashes will never be the same.

## Get set in swift

there are three code  example:

```swift
struct Car {
 private var fuelLevelStorage: Double = 1.0
 var fuelLevel: Double {
 set {
 fuelLevelStorage = max(min(newValue, 1), 0)
 }
 get {
 return fuelLevelStorage
 }
 }
 }
```

```swift
class family {
    var _members: Int = 2
    var members: Int {
      get {
        return _members
      }
      set (newVal) {
        if newVal >= 2 {
          _members = newVal
        } else {
          println('error: cannot have family with less than 2 members')
      }
    }
  }
}
```

thanks to the setter function, we can also set it's value  by typing, for example: instanceOfFamily.members = 3. What has changed, however, is the fact that we cannot set this variable to anything smaller than 2 anymore.

```swift
var A:Int = 0
var B:Int = 0

var C:Int {
get {return 1}
set {print("Recived new value", newValue, " and stored into 'B' ")
     B = newValue
     }
}

// When we are getting a value of C it fires get{} part of C property
A = C
A            // Now A = 1

// When we are setting a value to C it fires set{} part of C property
C = 2
B            // Now B = 2
```

## What does playground source folder do ?

when you are working in a playground and want to separate some of your code into other files like you do in a project, you can do it by adding those files to the Sources group like this. Note that when you do this, any declarations in your Sources are compiled into a separate module from your main
playground content, so they must be declared public to be usable by the playground.

## Property wrapper

- Defining a property wrapper

```swift
@propertyWrapper public struct Percentage {

 private var storage: Double

 public init(wrappedValue: Double) {
 storage = max(min(wrappedValue, 1), 0)
 }

 public var wrappedValue: Double {
 set {
 storage = max(min(newValue, 1), 0)
 }
 get {
 return storage
 }
 }
}
```

- Using a property wrapper

```swift
struct Car {
 @Percentage var fuelLevel: Double = 1.0
}
var myCar = Car()
myCar.fuelLevel = 1.1
print("Fuel:", myCar.fuelLevel)
```

- The principle of property wrapper
when you declare a property using a property wrapper attribute such as @Percentage, the compiler rewrites your property declaration to use an instance of the wrapper type (Percentage) to handle the storage and transformation of its value. In this case, the compiler takes your fuelLevel property declaration:

```swift
 @Percentage var fuelLevel: Double = 1.0
 ```

And rewrites it at compile time to something more like this:

 ```swift
 private var _fuelLevel = Percentage(wrappedValue: 1.0)
 var fuelLevel: Double {
 get { return _fuelLevel.wrappedValue }
 set { _fuelLevel.wrappedValue = newValue }
 }
 ```

## Generic Data Structures

- stack
  
```swift
struct Stack {
 var items = [Int]()
 mutating func push(_ newItem: Int) {
 items.append(newItem)
 }
 mutating func pop() -> Int? {
 guard !items.isEmpty else { return nil }
 return items.removeLast()
 }
}
```

- Generic stack

``` swift
struct Stack<Element> {
 var items = [Element]()
 mutating func push(_ newItem: Element) {
 items.append(newItem)
 }
 mutating func pop() -> Element? {
 guard !items.isEmpty else { return nil }
 return items.removeLast()
 }
}
```

Array uses Element as its placeholder type name. Optional uses Wrapped. It is common to use T (short for “Type”), U, and so on.

-----

see more information on apple offical **[docment](https://developer.apple.com/documentation/Swift)** about swift.
