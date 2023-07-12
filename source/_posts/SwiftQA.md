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

see more information on apple offical **[docment](https://developer.apple.com/documentation/Swift)** about swift.
