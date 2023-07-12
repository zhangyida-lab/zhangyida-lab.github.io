---
title: AppleBookAboutIOSDevelopment
date: 2023-06-16 08:57:16
tags: bookReview
---
![AppleBookAboutIOSDevelopment](iosDevelopment.jpg)
> Don't call us,I will call you back.
Apple book can give you a very standard way to CS develop and a good habit.if you are a beginner,even if you will not use swift language a few years later.
when you want to learn ios development,there are three main book "Develop in Swift Explorations","Develop in Swift Fundamentals","Develop in Swift Data Collections".
I will list some content in these books,I believe you will go to apple book to find them.
<!--more-->
## 1.Develop in Swift Explorations

the "Develop in Swift Explorations" in apple book is friendly for the reader who is beginner for CS developer.And this book is worth to reading many times.
some content in this book is  very useful ,you can clear your mind.
such as : the explaination about "app","event:Function Callbacks、Delegates、Data Sources、Outlets and Actions"
,"debug","test"...

### the structure of "Develop in Swift Explorations"

> - value
> - algorithm
> - Organizing Data
> - building app

### some content i think it's useful in "Develop in Swift Explorations"

- value
Programming is all about inputs and outputs.
the input and output is all data.
everything in our world can be captured and described by some form of data—and then analyzed, stored, and shared by computers and the programs that run on them
- algorithm
An algorithm is a set of instructions for accomplishing a task.
There are three essential building blocks in all algorithms: sequencing, selection, and iteration.
- Organizing Data
  - Instances, Methods, and Properties
Much of the code you write for an app is event-based, executing in response to a constantly changing environment.

- building app
Apps receive all kinds of input: touches on their screens, files, sensor input (such as a camera), data from the internet. Inputs can even come from other programs. For example, you can use the Share sheet in your Photos app to send a photo to another editing app, or to include it in a message to your friend. When it comes to output, apps can return images, sounds, music, text, tactile sensations, and animations.

## 2.Develop in Swift Fundamentals

### the structure of "Develop in Swift Fundamentals"

> - getting started with app development
> - introduction to UIkit
> - navigation and workflow 
> in every unit you can know basic swift and ios develop practice.

### some content i think it's useful in "Develop in Swift Fundamentals"

- All properties must be set to initial values before completing initialization.

``` swift
struct Temperature {
  var celsius: Double
 
  init(celsius: Double) {
    self.celsius = celsius
  }
  init(fahrenheit: Double) {
    celsius = (fahrenheit - 32) / 1.8
  }
}
 
let currentTemperature = Temperature(celsius: 18.5)
let boiling = Temperature(fahrenheit: 212.0)
 
print(currentTemperature.celsius)
print(boiling.celsius)
```

- Occasionally you’ll want to update the property values of a structure within an instance method. To do so you’ll need to add the mutating keyword before the function

``` swift
struct Odometer {
  var count: Int = 0 // Assigns a default value to the `count` 
  property. 
 
  mutating func increment() {
    count += 1
  }
 
  mutating func increment(by amount: Int) {
    count += amount
  }
 
  mutating func reset() {
    count = 0
  }
}
 
var odometer = Odometer() // odometer.count defaults to 0
odometer.increment() // odometer.count is incremented to 1
odometer.increment(by: 15) // odometer.count is incremented to 16 
odometer.reset() // odometer.count is reset to 0”
```

- Property Observers
  
``` swift
struct StepCounter {
    var totalSteps: Int = 0 {
        willSet {
            print(”About to set totalSteps to \(newValue)”)
        }
        didSet {
            if totalSteps > oldValue  {
                print(”Added \(totalSteps - oldValue) steps”)
            }
        }
    }
}

```

- Type Properties and Methods
  
which can be accessed or called on the type itself. Use the static keyword to add a property or method to a type

```swift
struct Temperature {
  static var boilingPoint = 100
}
```

- in Swift, self refers to the current instance of the type.

```swift
struct Car {
  var color: Color
 
  var description: String {
    return “This is a \(self.color) car.”
  }
}

```

```swift
struct Temperature {
  var celsius: Double
 
  init(celsius: Double) {
    self.celsius = celsius
  }
}

```

- Override Initializer

```swift
class Person {
  let name: String
 
  init(name: String) {
    self.name = name
  }
}


class Student: Person {
  var favoriteSubject: String
 
  init(name: String, favoriteSubject: String) {
    self.favoriteSubject = favoriteSubject
    super.init(name: name)
  }
}

```

- Class or Structure
Because classes and structures are so similar, it can be hard to know when to use classes and when to use structures for the building blocks of your program.
As a basic rule, you should start new types as structures until you need one of the features that classes provide.
Start with a class when you’re working with a framework that uses classes or when you want to refer to the same instance of a type in multiple places”
such as uikit,uitableview .

- break and continous
  
```swift
“for counter in -3...3 {
  print(counter)
  if counter == 0 {
    break
  }
}

“for person in people {
  if person.age < 18 {
    continue
  }
 
  sendEmail(to: person)
}
```

- Control

“Think of a control as a communication tool between the user and the app. When the user interacts with a control, the control triggers a control event. Different controls trigger different control events.
After setting up a control in Interface Builder, you set up an @IBAction that responds to a specific control event and allows you to execute a block of code. ”

- View Controllers
Each UIViewController class has a view property that represents the parent view of the scene.

When you added a button to the screen, the button became a child view of the scene’s main view. When you wired up actions and outlets, you linked them to the ViewController file, which defined the view controller for that scene.

```swift
“if let unwrappedPublicationYear = book.publicationYear {
  print(”The book was published in \(unwrappedPublicationYear)”)
}
else {
  print(”The book does not have an official publication date.”)
}
”
```

- Implicitly Unwrapped Optionals
  
```swift
class ViewController: UIViewController {
  @IBOutlet var label: UILabel!
}
 
If you were the developer of this class, you’d know that anytime a ViewController is created and presented to the user, there will always be a label on the screen, because you added it in the storyboard. But in iOS development, the storyboard elements aren’t connected to their corresponding outlets until after initialization takes place. Therefore, label must be allowed to be nil for a brief period”

```

## 3.Develop in Swift Data Collections

### the structure of "Develop in Swift Data Collections"

> - table and persistence
> - working with Web
> - advanced data display

### some content i think it's useful in "Develop in Swift Data Collections"

- Protocol and Delegation
A protocol is a set of rules or procedures that define how things are done
When you adopt a protocol in Swift, you're promising to implement all the methods required by that protocol.

Delegation is a design pattern, or common practice, that enables a class or structure to hand off, or delegate, some of its responsibilities to an instance of another type

One approach is to add a table view instance directly to a view controller's view. In this scenario, the view controller may manage other views in addition to the table view. This means you're responsible for managing the position and size of the table view. You're also responsible for setting the data source and delegate object(s). (You'll learn about the data source object and the delegate object later in this lesson.)
The second approach is to use a table view controller. UITableViewController is a view controller subclass that manages a single table view instance. In this approach, the table view takes up the entire view, and you can't adjust the table view's size. The table view controller also acts as the data source and delegate of the table view.

closures, which are blocks of code that can be used like variables, which allow you to write code that will be executed at a later time. You'll use closures to write code that will execute after network requests are finished. You'll also learn how to use closures to work with collections and create animations in your app

- Clourse
“As you are writing out the method name, press the Return key to autocomplete it, and the cursor will highlight the first parameter. Press Return again, and Xcode will simplify and autofill the closure syntax. ”

What makes closures so powerful is that you can pass any logic to the sorted(by:) function. So now you can pass a closure that sorts by track name or by star rating:

```swift
let sortedTracks = tracks.sorted { (firstTrack, secondTrack) ->
   Bool in
    return firstTrack.starRating < secondTrack.starRating
}
```

- Trailing Closure Syntax

```swift
func performRequest(url: String, response: (code: Int) -> Void) { }

performRequest(url: "https://www.apple.com") { (data) in
    print(data)
}

```

```swift
let names = ["Johnny", "Nellie", "Aaron", "Rachel"]
 
// Creates a new array of full names by adding "Smith" to each
   first name
let fullNames = names.map { (name) -> String in
    return name + " Smith"
}
```

there  is another book you can read ,that is "iOS Program 6th".i will review this book next time.