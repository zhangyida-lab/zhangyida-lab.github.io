---
title: iosProgramming
tags: iosProgramming
---
> 老当益壮，宁移白首之心；穷且益坚，不坠青云之志。——《滕王阁序》
> UIKit construct and manage a graphical, event-driven user interface for your iOS, iPadOS, or tvOS app.——Apple

![iosProgramming01](iosProgramming01.jpg)

## The View Hierarchy

- The View Hierarchy
Every application has a single instance of UIWindow that serves as the container for all the views in the
application. UIWindow is a subclass of UIView, so the window is itself a view. The window is created
when the application launches. Once the window is created, other views can be added to it.
- add view program
<!--more-->
``` swift
class ViewController: UIViewController {
 override func viewDidLoad() {
super.viewDidLoad()
let firstFrame = CGRect(x: 160, y: 240, width: 100, height: 150)
let firstView = UIView(frame: firstFrame)
firstView.backgroundColor = UIColor.blue
view.addSubview(firstView)
 }
}
```

- There are two ways that a view controller can create its view hierarchy:

  - in Interface Builder, by using an interface file such as a storyboard

  - programmatically, by overriding the UIViewController method loadView()

- what is viewDidLoad(),viewWillAppear(_:),and when to use it ?
The first option is the viewDidLoad() method that you
overrode to spot lazy loading. This method is called after the view controller’s interface file is loaded,
at which point all of the view controller’s outlets will reference the appropriate objects. The second
option is another UIViewController method, viewWillAppear(_:). This method is called just before a
view controller’s view is added to the window

Override viewDidLoad() if the configuration only needs to be done once
during the run of the app. Override viewWillAppear(_:) if you need the configuration to be done each
time the view controller’s view appears onscreen.

## methods  the lifecycle of a view controller and its view

- init(coder:) is the initializer for UIViewController instances created from a storyboard.When a view controller instance is created from a storyboard, its init(coder:) gets called once.You will learn more about this method in Chapter 16.
- init(nibName:bundle:) is the designated initializer for UIViewController.When a view controller instance is created without the use of a storyboard, its init(nibName:bundle:) gets called once. Note that in some apps, you may end up creating several instances of the same view controller class. This method will get called once on each view controller as it is created.
- loadView() is overridden to create a view controller’s view programmatically.
- viewDidLoad() is overridden to configure views created by loading an interface file. This method gets called after the view of a view controller is created.
- viewWillAppear(_:) is overridden to configure views created by loading an interface file.This method and viewDidAppear(_:) get called every time your view controller is moved onscreen. viewWillDisappear(_:) and viewDidDisappear(_:) get called every time your view controller is moved offscreen.

## Creating a View Programmatically

```swift
import UIKit
import MapKit
class MapViewController: UIViewController {
 var mapView: MKMapView!
 override func loadView() {
// Create a map view
mapView = MKMapView()
// Set it as *the* view of this view controller
view = mapView
 }
 override func viewDidLoad() {
super.viewDidLoad()
print("MapViewController loaded its view.")
 }
}
```

> When a view controller is created, its view property is nil. If a view controller is asked for its view and its view is nil, then the **loadView()** method is called.

As subclasses of UIViewController, all view controllers inherit an important property:

``` swift
var view: UIView!
```

This property points to a UIView instance that is the root of the view controller’s view hierarchy.

## Programmatic Constraints

- create these constraints in loadView()
  
```swift
let topConstraint
= segmentedControl.topAnchor.constraint(equalTo: view.topAnchor)
let leadingConstraint
= segmentedControl.leadingAnchor.constraint(equalTo: view.leadingAnchor)
let trailingConstraint
= segmentedControl.trailingAnchor.constraint(equalTo: view.trailingAnchor)

topConstraint.isActive = true
leadingConstraint.isActive = true
trailingConstraint.isActive = true
```

## Programmatic Controls

Here are a few of the common control events that you
will use:
|control|description|
|---|---|
|UIControlEvents.touchDown     |A touch down on the control.|
|UIControlEvents.touchUpInside  |A touch down followed by a touch up while still within the bounds of the control.|
|UIControlEvents.valueChanged   |A touch that causes the value of the control to change.|
|UIControlEvents.editingChanged|   A touch that causes an editing change for a UITextField.|

```swift
segmentedControl.addTarget(self,
action: #selector(MapViewController.mapTypeChanged(_:)),
for: .valueChanged)

func mapTypeChanged(_ segControl: UISegmentedControl) {
 switch segControl.selectedSegmentIndex {
 case 0:
mapView.mapType = .standard
 case 1:
mapView.mapType = .hybrid
 case 2:
mapView.mapType = .satellite
 default:
break
 }
}
```

## Debug

- Continue program execution – resumes normal execution of the program
- Step over – executes a single line of code without entering any function or method call
- Step into – executes the next line of code, including entering a function or method call
- Step out – continues execution until the current function or method is exited

## Network

## TableView

## CollectionView

## UINavigationController

the UINavigationController’s stack is an array, it will take ownership of any view controller added to it. Thus, the DetailViewController is owned only by the UINavigationController after the segue finishes. When the stack is popped, the DetailViewController is destroyed. The next time a row is tapped, a new instance of DetailViewController is created.

## Event handling

when you tap a**UIButton** within its bounds, it will receive the touch event and respond in button-like fashion – by calling the action method on its targe.

For both the shake and keyboard events, there is no event location within your view hierarchy to determine which view will receive the event, so another mechanism must be used. This mechanism is the **first responder** status

Instances of UITextField and UITextView have an uncommon response to touch events. When touched, a text field or a text view becomes the first responder, which in turn triggers the system to put the keyboard onscreen and send the keyboard events to the text field or view. The keyboard and the text field or view have no direct connection, but they work together through the first responder status.

## The Responder Chain

A UIResponder can be a first responder and receive touch events. UIView is one example of a UIResponder subclass, but there are many others,
including UIViewController, UIApplication, and UIWindow. You are probably thinking, “But you can’t touch a UIViewController. It’s not an onscreen object.” You are right – you cannot send a touch event directly to a UIViewController, but view controllers can receive events through the responder
chain.

Every UIResponder can reference another UIResponder through its next property, and together these objects make up the responder chain . A touch event starts at the view that was touched.
The next responder of a view is typically its UIViewController (if it has one) or its superview (if it does not). The next responder of a view controller is typically its view’s superview. The top-most superview is the window. The window’s next responder is the singleton instance of UIApplication.

## UIControl

The class UIControl is the superclass for several classes in Cocoa Touch, including UIButton and UISlider.Assigning the target and actionprogrammatically would look like this:

```swift
button.addTarget(self,
action: #selector(Thermostat.resetTemperature(_:)),
for: [.touchUpInside, .touchUpOutside])
```

UIControl handles UIControlEvents.touchUpInside like this:

```swift
override func touchesEnded(_ touches: Set<UITouch>, with event: UIEvent?) {
 // Reference to the touch that is ending
 let touch = touches.first!
 // Location of that point in this control's coordinate system
 let touchLocation = touch.location(in: self)
 // Is that point still in my viewing bounds?
 if bounds.contains(touchLocation) {
// Send out action messages to all targets registered for this event!
sendActions(for: .touchUpInside)
 }
 else {
// The touch ended outside the bounds: different control event
sendActions(for: .touchUpOutside)
 }
}
```

At the end of the UIResponder method implementations, the control calls the method sendActions(for:) on itself. This method looks at all of the target-action pairs the control has. If any of them are registered for the control event passed as the argument, the corresponding action method is called on those targets.

However, a control never calls a method directly on its targets. Instead, it routes these method calls through the UIApplication object. Why not have controls call the action methods directly on the targets? Controls can also have nil-targeted actions. If a UIControl’s target is nil, the UIApplication
finds the first responder of its UIWindow and calls the action method on it.

## Design Patterns

A design pattern solves a common software engineering problem. Design patterns are not actual snippets of code, but instead are abstract ideas or approaches that you can use in your applications.Good design patterns are valuable and powerful tools for any developer.

The consistent use of design patterns throughout the development process reduces the mental overhead in solving a problem so you can create complex applications more easily and rapidly. Here are some of the design patterns that you have already used:

- Delegation:
  
One object delegates certain responsibilities to another object. You used delegation with the UITextField to be informed when the contents of the text field change.

- Data source:
  
A data source is similar to a delegate, but instead of reacting to another object, a data source is responsible for providing data to another object when requested. You used the data source pattern with table views: Each table view has a data source that is responsible for, at a minimum, telling the table view how many rows to display and which cell it should display at each index path.

- Model-View-Controller:
Each object in your applications fulfills one of three roles. Model objects
are the data. Views display the UI. Controllers provide the glue that ties the models and views
together.
- Target-action pairs:
  
One object calls a method on another object when a specific event occurs.The target is the object that has a method called on it, and the action is the method being called.For example, you used target-action pairs with buttons: When a touch event occurs, a method will be called on another object (often a view controller)

## Error Handling and exceptions

``` swift
let pi = "Apple Pie"
let numberFromString = Int(pi)
```

The string “Apple Pie” cannot be represented as an Int, so numberFromString will contain nil. An optional works well for representing failure here because you do not care why it failed. You just want to know whether it was successful. When you need to know why something failed, an optional will not provide enough information.
If a method could generate an error, its method signature needs to indicate this using the throws keyword.

```swift
func removeItem(at URL: URL) throws
```

To call a method that can throw, you use a do-catch statement,Within the catch block, there is an implicit error constant that contains information describing the error. You can optionally give this constant an explicit name.

```swift
func deleteImage(forKey key: String) {
 cache.removeObject(forKey: key as NSString)
 let url = imageURL(forKey: key)
 do {
try FileManager.default.removeItem(at: url)
 } catch {
print("Error removing the image from disk: \(error)")
 }
}
```

```swift
func deleteImage(forKey key: String) {
 cache.removeObject(forKey: key as NSString)
 let url = imageURL(forKey: key)
 do {
try FileManager.default.removeItem(at: url)
 } catch let deleteError {
print("Error removing the image from disk: \(errordeleteError)")
 }
}
```

In Swift,exceptions are nearly always used to indicate programmer error. When an exception is thrown, the information about what went wrong is in an NSException object. That information is usually just a hint to the programmer, like, “You tried to access the seventh object in this array, but there are only two.”

When do you use exceptions, and when do you use error handling? If you are writing a method that should only be called with an odd number as an argument, throw an exception if it is called with an even number – the caller is making an error and you want to help that programmer find the error. If
you are writing a method that wants to read the contents of a particular directory but does not have the necessary privileges, use Swift’s error handling and throw an error to the caller to indicate why you were unable to fulfill this very reasonable request.

see more information on apple offical **[docment](https://developer.apple.com/documentation/uikit/uiviewcontroller)** about uikit.
