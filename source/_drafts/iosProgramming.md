---
title: iosProgramming
tags: iosProgramming
---

> 老当益壮，宁移白首之心；穷且益坚，不坠青云之志。——《滕王阁序》
 in this post ,i will record what i think is important.
 > -- The View Hierarchy




- The View Hierarchy
Every application has a single instance of UIWindow that serves as the container for all the views in the
application. UIWindow is a subclass of UIView, so the window is itself a view. The window is created
when the application launches. Once the window is created, other views can be added to it.
- add view program

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


## some methods that are called during the lifecycle of a view controller and its view.

- init(coder:) is the initializer for UIViewController instances created from a storyboard.
When a view controller instance is created from a storyboard, its init(coder:) gets called once.
You will learn more about this method in Chapter 16.
- init(nibName:bundle:) is the designated initializer for UIViewController.
When a view controller instance is created without the use of a storyboard, its
- init(nibName:bundle:) gets called once. Note that in some apps, you may end up creating
several instances of the same view controller class. This method will get called once on each view
controller as it is created.
- loadView() is overridden to create a view controller’s view programmatically.
- viewDidLoad() is overridden to configure views created by loading an interface file. This method
gets called after the view of a view controller is created.
- viewWillAppear(_:) is overridden to configure views created by loading an interface file.
This method and viewDidAppear(_:) get called every time your view controller is moved
onscreen. viewWillDisappear(_:) and viewDidDisappear(_:) get called every time your view
controller is moved offscreen.

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

> When a view controller is created, its view property is nil. If a view controller is asked for its view and
its view is nil, then the **loadView()** method is called.

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
>UIControlEvents.touchDown     A touch down on the control.
UIControlEvents.touchUpInside  A touch down followed by a touch up while still within
the bounds of the control.
UIControlEvents.valueChanged   A touch that causes the value of the control to change.
UIControlEvents.editingChanged   A touch that causes an editing change for a UITextField.

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
