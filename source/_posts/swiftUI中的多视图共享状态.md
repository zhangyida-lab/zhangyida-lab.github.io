---
title: swiftUI中的多视图共享状态
date: 2025-03-24 21:58:03
tags: [swift编程,swiftUI]
---


# swiftui中多个视图之间共享状态的代码示例

在 SwiftUI 中，多个视图之间共享状态可以通过 `@Binding`、`@ObservedObject` 或 `@EnvironmentObject` 等方式来实现。这里我将给你展示两种常用方法：**@Binding** 和 **@ObservedObject**。


### 1. 使用 @Binding 共享状态


`@Binding` 允许一个父视图将它的状态传递给子视图，子视图通过绑定这个状态来更新父视图的状态。


#### **示例：父子视图共享状态（使用 @Binding）**


```swift
import SwiftUI

// 子视图：使用 @Binding 获取父视图的状态
struct ChildView: View {
    @Binding var isToggled: Bool // 绑定父视图的状态

    var body: some View {
        Toggle("开关", isOn: $isToggled) // 绑定到父视图的状态变量
            .padding()
    }
}

// 父视图
struct ParentView: View {
    @State private var isToggled = false // 声明本地状态

    var body: some View {
        VStack {
            Text(isToggled ? "开" : "关")
                .font(.largeTitle)
                .padding()

            // 将父视图的状态通过 @Binding 传递给子视图
            ChildView(isToggled: $isToggled)
                .padding()
        }
        .padding()
    }
}

struct ContentView: View {
    var body: some View {
        ParentView() // 父视图
    }
}

#Preview {
    ContentView()
}
```


### **解析**：


- `@State`：父视图中声明了一个本地状态 `isToggled`，它用于保存开关的状态。
- `@Binding`：子视图 `ChildView` 使用 `@Binding` 来绑定父视图的 `isToggled` 状态，这样子视图的切换会直接影响父视图的状态。
- `Toggle`：子视图中的 `Toggle` 通过 `$isToggled`（即绑定的状态）来更新父视图的状态。当用户切换开关时，父视图的 `isToggled` 状态会随之改变，UI 自动更新。

### **优点**：


- **简洁明了**，适合父视图向子视图传递简单的状态。


### 2. 使用 @ObservedObject 共享状态


`@ObservedObject` 适合用于多个视图之间共享一个 **复杂的状态**，并且这个状态不依赖于视图层级。通常和 `ObservableObject` 配合使用。


#### **示例：使用 @ObservedObject 实现共享状态**


```swift
import SwiftUI

// 定义一个状态模型，遵循 ObservableObject 协议
class AppState: ObservableObject {
    @Published var counter: Int = 0
}

struct ChildView: View {
    @ObservedObject var appState: AppState // 观察父视图传递过来的状态

    var body: some View {
        VStack {
            Text("子视图计数：\(appState.counter)")
                .font(.title)
            Button("增加") {
                appState.counter += 1 // 更新共享的状态
            }
            .padding()
        }
    }
}

struct ParentView: View {
    @StateObject private var appState = AppState() // 创建一个共享状态对象

    var body: some View {
        VStack {
            Text("父视图计数：\(appState.counter)")
                .font(.largeTitle)
            Button("增加") {
                appState.counter += 1 // 更新共享的状态
            }
            .padding()

            // 将共享状态传递给子视图
            ChildView(appState: appState)
                .padding()
        }
        .padding()
    }
}

struct ContentView: View {
    var body: some View {
        ParentView() // 父视图
    }
}

#Preview {
    ContentView()
}
```


### **解析**：


- `AppState`：这是一个 `ObservableObject`，它包含一个被 `@Published` 修饰的变量 `counter`，该变量会随着值的变化自动通知所有观察它的视图。
- `@StateObject`：在父视图中使用 `@StateObject` 来创建并管理 `AppState` 的实例。这会保证在父视图生命周期内，状态对象不会被重新创建。
- `@ObservedObject`：子视图 `ChildView` 使用 `@ObservedObject` 来观察 `appState`，从而共享 `counter` 的状态。每次状态变化，`ChildView` 会重新渲染。

### **优点**：


- 适用于 **多个视图** 之间共享一个复杂的状态，可以是全局状态。
- `ObservableObject` 能够管理更复杂的数据结构，并支持多个视图之间的共享和响应式更新。


### **总结**：


- 使用 `@Binding` 时，父视图将状态传递给子视图，并且通过绑定来共享和更新状态。适用于简单的状态共享。
- 使用 `@ObservedObject` 时，可以管理更复杂的状态对象，适合多个视图之间共享数据，并且这些数据可能会发生变化，进而影响多个视图。

希望这两个例子能帮助你理解在 SwiftUI 中如何共享状态！如果有更多问题，随时告诉我！😊

# 一个 @EnvironmentObject 多个视图共享状态的代码示例
在 SwiftUI 中，`@EnvironmentObject` 用来实现跨多个视图的全局共享状态。与 `@Binding` 和 `@ObservedObject` 不同，`@EnvironmentObject` 更适合用于跨多个视图层次结构共享的数据，尤其是当你不想显式地将数据从父视图传递到子视图时。


### **示例：使用 @EnvironmentObject 共享状态**


我们将创建一个简单的示例，展示如何使用 `@EnvironmentObject` 来在多个视图之间共享状态。


#### **步骤**：


1. 创建一个 `ObservableObject` 作为共享状态。
2. 在父视图中初始化该状态对象，并通过 `@EnvironmentObject` 在子视图中访问它。
3. 子视图可以更新共享的状态，父视图和其他子视图会自动响应这些变化。

#### **完整代码示例：**


```swift
import SwiftUI

// 1. 定义一个共享状态对象，遵循 ObservableObject 协议
class AppState: ObservableObject {
    @Published var counter: Int = 0
}

struct ChildView: View {
    // 2. 使用 @EnvironmentObject 访问共享的状态
    @EnvironmentObject var appState: AppState
    
    var body: some View {
        VStack {
            Text("子视图计数：\(appState.counter)")
                .font(.title)
            
            Button("增加") {
                appState.counter += 1 // 更新共享状态
            }
            .padding()
        }
    }
}

struct ParentView: View {
    // 3. 父视图也可以访问共享的状态
    @EnvironmentObject var appState: AppState
    
    var body: some View {
        VStack {
            Text("父视图计数：\(appState.counter)")
                .font(.largeTitle)
            
            Button("增加") {
                appState.counter += 1 // 更新共享状态
            }
            .padding()

            // 将共享状态传递给子视图
            ChildView()
                .padding()
        }
        .padding()
    }
}

struct ContentView: View {
    // 4. 在顶层视图创建并注入共享状态
    @StateObject private var appState = AppState() // 创建共享状态对象
    
    var body: some View {
        ParentView()
            .environmentObject(appState) // 将共享状态注入到环境中
    }
}

#Preview {
    ContentView()
}
```


### **解析：**


1. **AppState**：我们定义了一个 `ObservableObject` 类，包含一个 `@Published` 状态变量 `counter`，它用来保存计数器的值。当 `counter` 更新时，所有观察它的视图都会自动更新。
2. **@EnvironmentObject**：在子视图和父视图中使用 `@EnvironmentObject` 来访问共享的 `appState`。每次更新 `counter` 时，所有依赖于这个状态的视图都会自动刷新。
3. **@StateObject**：在 `ContentView` 中，我们使用 `@StateObject` 来创建 `AppState` 的实例，并通过 `.environmentObject()` 将它注入到视图层级的环境中。这样所有子视图都可以访问并更新这个共享的状态。

### **优点：**


- **无需显式传递状态**：使用 `@EnvironmentObject` 时，父视图不需要将状态显式传递给每个子视图。只需要在根视图中创建一次状态对象，然后通过 `.environmentObject()` 注入，所有子视图都可以通过 `@EnvironmentObject` 访问和更新状态。
- **全局共享**：适用于跨多个视图层次结构的共享状态，尤其在多层嵌套视图中非常方便。

### **适用场景：**


- **全局设置或配置信息**：比如主题、语言设置等全局状态。
- **应用级别的数据**：如用户认证状态、应用内的购物车数量等。


### **总结**：


- **@EnvironmentObject** 是一种非常强大的方式，允许你在多个视图之间共享状态，而不需要手动传递数据。
- **创建共享状态对象**：使用 `@StateObject` 创建共享状态，并使用 `.environmentObject()` 注入到视图层级。
- **访问共享状态**：在任意需要访问该状态的视图中，使用 `@EnvironmentObject` 来访问和更新状态。



# 这个注入是所谓的依赖注入模式吗？

是的，`@EnvironmentObject` 在 SwiftUI 中的确有类似 **依赖注入（Dependency Injection）** 的概念，尽管它的实现方式有所不同。我们可以将它看作是一种 **隐式依赖注入**，它让视图能够自动获取外部环境（通常是父视图或全局状态）中的共享数据，而无需显式地将数据传递给每个子视图。


### **依赖注入模式简介：**


依赖注入是一种设计模式，它的核心思想是：**不要让类自己创建依赖对象，而是通过外部注入**。换句话说，依赖注入将对象的创建责任从类内部转移到外部。这样，你可以将不同的对象或服务提供给需要的地方，而不是由类自己去创建它们。


### **在 SwiftUI 中的应用：**


在 SwiftUI 中，使用 `@EnvironmentObject` 就是将某些共享的状态（比如应用级的数据模型）**注入到视图的环境中**，让视图能够直接访问这些共享状态，而无需显式地在视图之间传递这些对象。


### **与传统的依赖注入的对比：**


#### **传统的依赖注入**：


- 在传统的依赖注入中，我们通常通过构造函数或属性注入的方式将依赖注入到对象中。
例如，在一些传统的 OOP（面向对象编程）中，你可能会看到如下的依赖注入：

```swift
class ViewController {
    private let dataService: DataService
    
    // 构造函数注入
    init(dataService: DataService) {
        self.dataService = dataService
    }
}
```

#### **SwiftUI 中的依赖注入：**


- 在 SwiftUI 中，`@EnvironmentObject` 和 `.environmentObject()` 让你能够在视图层级之间注入共享的状态，视图不再需要显式地传递依赖，而是通过 **环境对象** 自动获得依赖。

```swift
// 定义一个共享状态对象
class AppState: ObservableObject {
    @Published var counter: Int = 0
}

// 父视图
struct ParentView: View {
    @StateObject private var appState = AppState()

    var body: some View {
        ChildView()
            .environmentObject(appState)  // 将共享状态对象注入
    }
}

// 子视图
struct ChildView: View {
    @EnvironmentObject var appState: AppState  // 自动获取共享状态
    
    var body: some View {
        Text("Counter: \(appState.counter)")
    }
}
```

在这个例子中，`AppState` 对象通过 `.environmentObject()` 被 **注入** 到视图环境中。子视图 `ChildView` 通过 `@EnvironmentObject` **隐式地** 获取这个共享状态对象，而不需要通过父视图显式地传递它。


### **为什么称之为“依赖注入”？**


- **共享状态**：`@EnvironmentObject` 让不同的视图可以共享一个全局的状态对象，类似于其他设计模式中的“共享资源”。
- **自动注入**：通过 `.environmentObject()` 将对象“注入”到环境中，子视图可以直接获取它，而不需要显式的构造函数或属性传递。

### **@EnvironmentObject 的优势与传统依赖注入的对比**：


1. **简化依赖传递**：在传统的依赖注入中，父视图需要显式传递依赖给子视图，尤其是在有多层视图的情况下，依赖链可能变得非常长。而在 `@EnvironmentObject` 中，视图之间共享的状态自动通过环境注入，不需要手动传递。
2. **隐式传递**：`@EnvironmentObject` 是隐式的，视图之间不需要显式声明依赖关系，这让代码看起来更简洁，但也需要注意它可能带来的 **依赖不明确性**，特别是在大型应用中。
3. **全局共享**：`@EnvironmentObject` 使得一个对象可以在整个视图树中共享，而不需要将其传递给每个视图，这有点类似于传统的依赖注入中使用的“全局服务”或“单例模式”。


### **总结**：


- **是的，@EnvironmentObject 确实可以视作一种依赖注入模式**，它允许跨多个视图共享状态，而不需要显式地通过父视图传递依赖。
- 这种隐式注入方式非常适合在视图树中共享全局状态，减少了冗余的代码传递，并且提高了应用的可维护性。

