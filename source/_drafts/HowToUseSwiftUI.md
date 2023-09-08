---
title: HowToUseSwiftUI
tags: ios development
---

![FoodDelivery](FoodDelivery.jpg)
> 仰观宇宙之大，俯察品类之盛。所以游目骋怀，足以极视听之娱。——《兰亭集序》

## The key concept of swiftUI

- app.swift

An app that uses the SwiftUI app life cycle has a structure that conforms to the App protocol. The structure’s body property returns one or more scenes, which in turn provide content for display. The @main attribute identifies the app’s entry point.

```swift

import SwiftUI
@main
struct LandmarksApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}

```

- contentView.swift

By default, SwiftUI view files declare two structures. The first structure conforms to the View protocol and describes the view’s content and layout. The second structure declares a preview for that view.

```swift
import SwiftUI


struct ContentView: View {
    var body: some View {
        Text("Hello, World!")
            .padding()
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}


```

- modifiers
To customize a SwiftUI view, you call methods called **modifiers**. Modifiers wrap a view to change its display or other properties. Each modifier returns a new view, so it’s common to chain multiple modifiers, stacked vertically.

- spacer
A spacer expands to make its containing view use all of the space of its parent view, instead of having its size defined only by its contents.
- padding
Use the padding() modifier method to give the landmark’s name and details a little more space.

see more information on apple offical **[docment](https://developer.apple.com/tutorials/swiftui/creating-and-combining-views)** about swiftUI.

- swiftUI preview

Xcode’s canvas automatically recognizes and displays any type in the current editor that conforms to the PreviewProvider protocol. A preview provider returns one or more views, with options to configure the size and device



