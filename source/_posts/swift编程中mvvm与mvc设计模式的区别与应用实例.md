---
title: swift编程中mvvm与mvc设计模式的区别与应用实例
date: 2025-03-22 13:44:54
tags: swift编程
---




# 在swift编程中mvvm与mvc设计模式的区别与应用实例

在 Swift 编程中，MVC（Model-View-Controller）和 MVVM（Model-View-ViewModel）是两种常见的架构模式，它们的主要区别如下：


## **MVC（Model-View-Controller）**


### **概念**

<!--more-->
- **Model（模型）**：数据层，负责数据的存储、获取和处理。
- **View（视图）**：UI 层，负责显示数据，直接与用户交互。
- **Controller（控制器）**：负责协调 Model 和 View 之间的交互，处理用户输入，并更新 View。

### **特点**


- 结构简单，易于理解。
- Controller 过于臃肿，容易变成“大胖子控制器”。
- View 和 Controller 耦合较高，不易于测试。

### **MVC 示例**


```swift
import UIKit

// Model
struct User {
    let name: String
    let age: Int
}

// Controller
class UserViewController: UIViewController {
    private let user = User(name: "Tom", age: 25)
    
    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .white
        showUserInfo()
    }
    
    func showUserInfo() {
        print("User: \(user.name), Age: \(user.age)")
    }
}
```


**问题**：在 MVC 中，`UserViewController` 既负责 UI，又负责数据处理，容易造成代码耦合过高，导致难以维护。



## **MVVM（Model-View-ViewModel）**


### **概念**


- **Model（模型）**：数据层，和 MVC 一样，处理数据存储、获取和逻辑。
- **View（视图）**：负责 UI 显示，与用户交互。
- **ViewModel（视图模型）**：负责业务逻辑和数据绑定，将 Model 处理后提供给 View。

### **特点**


- 通过 ViewModel 分离了 UI 和业务逻辑，提高代码的可测试性。
- View 层和 ViewModel 之间可以使用数据绑定（如 Combine、RxSwift）来简化交互。
- 适用于 SwiftUI，结合 @StateObject 和 @Published 实现响应式 UI。

### **MVVM 示例**


```swift
import SwiftUI
import Combine

// Model
struct User {
    let name: String
    let age: Int
}

// ViewModel
class UserViewModel: ObservableObject {
    @Published var user: User = User(name: "Tom", age: 25)
    
    func updateUser() {
        user = User(name: "Jerry", age: 30)
    }
}

// View
struct UserView: View {
    @StateObject private var viewModel = UserViewModel()
    
    var body: some View {
        VStack {
            Text("User: \(viewModel.user.name), Age: \(viewModel.user.age)")
                .padding()
            Button("Update User") {
                viewModel.updateUser()
            }
        }
    }
}
```


### **MVVM 优势**


- **解耦**：View 只负责 UI，ViewModel 处理业务逻辑，避免 Controller 过于臃肿。
- **易于测试**：ViewModel 逻辑可以独立测试，而不依赖 UI。
- **适用于 SwiftUI**：结合 @Published 进行数据绑定，实现响应式编程。


## **何时使用？**


|  | MVC | MVVM |
| ---- | ---- | ---- |
| 代码复杂度 | 适合小型项目 | 适合中大型项目 |
| 可读性 | 可能会导致 Controller 代码过多 | View 和 ViewModel 分离，提高可读性 |
| 可测试性 | UI 和逻辑耦合，难以单独测试 | ViewModel 可独立测试 |
| 适用场景 | UIKit 传统开发 | SwiftUI 和响应式编程 |


如果你是 **UIKit 项目**，且逻辑简单，MVC 可能更直观；如果你使用 **SwiftUI**，或项目较大，建议采用 MVVM，以便管理复杂的 UI 交互和数据流。

# 详细解释下mvvm中viewmodel的作用

在 **MVVM（Model-View-ViewModel）** 架构中，**ViewModel** 扮演着至关重要的角色，它位于 **Model（数据层）** 和 **View（UI 层）** 之间，负责数据的管理、转换和业务逻辑处理，使 View 变得更轻量化，并减少 View 与 Model 之间的直接交互。



## **1. ViewModel 的主要作用**


### **(1) 负责业务逻辑处理**


ViewModel 主要负责处理数据的获取、转换、存储和操作，避免这些逻辑直接出现在 View 层，从而保持 View 的简洁性。例如，ViewModel 可以：


- 调用 API 获取数据，并转换成 UI 需要的格式
- 处理数据的计算、筛选、排序等业务逻辑
- 执行数据持久化（如存储到数据库或 UserDefaults）

### **(2) 提供数据绑定**


ViewModel 负责管理数据，并且与 View 进行数据绑定（SwiftUI 使用 `@Published` 和 `@StateObject`，UIKit 可以使用 Combine 或者 RxSwift）。


- **当数据变化时，View 会自动更新**
- **ViewModel 处理数据，不需要 View 直接操作 Model**

### **(3) 促进模块解耦**


- View 只负责 UI 展示，不直接操作 Model
- Model 只负责数据存储和管理
- ViewModel 充当桥梁，使 View 和 Model 互不依赖，降低耦合度


## **2. ViewModel 在 SwiftUI 中的应用**


SwiftUI 使用 `ObservableObject` 和 `@Published` 实现 ViewModel，使 UI 和数据能够自动同步。


### **示例：一个用户信息的 MVVM 结构**


#### **(1) Model（数据层）**


```swift
struct User {
    let name: String
    let age: Int
}
```


#### **(2) ViewModel（负责管理数据）**


```swift
import SwiftUI

class UserViewModel: ObservableObject {
    @Published var user: User = User(name: "Tom", age: 25)
    
    func updateUser() {
        user = User(name: "Jerry", age: 30)
    }
}
```


- **继承 ObservableObject**：让 ViewModel 可被 SwiftUI 观察
- **使用 @Published**：使 user 的值变化时，自动通知 UI 更新
- **定义业务逻辑 updateUser()**：修改 user 信息，触发 UI 更新

#### **(3) View（UI 层，绑定 ViewModel）**


```swift
struct UserView: View {
    @StateObject private var viewModel = UserViewModel()
    
    var body: some View {
        VStack {
            Text("User: \(viewModel.user.name), Age: \(viewModel.user.age)")
                .padding()
            Button("Update User") {
                viewModel.updateUser()
            }
        }
    }
}
```


- **@StateObject**：让 View 监听 ViewModel 的变化
- **数据绑定 viewModel.user**：当 user 发生变化，UI 自动更新
- **按钮操作 updateUser()**：触发 ViewModel 的方法，更新数据

##### **最终效果**


当点击 `Update User` 按钮时，用户信息会更新，View 也会自动刷新，无需手动管理 UI。



## **3. ViewModel 在 UIKit + Combine 中的应用**


如果你使用的是 UIKit，而不是 SwiftUI，你可以使用 **Combine** 进行数据绑定。


#### **(1) ViewModel**


```swift
import Combine

class UserViewModel: ObservableObject {
    @Published var user: User = User(name: "Tom", age: 25)
    
    func updateUser() {
        user = User(name: "Jerry", age: 30)
    }
}
```


这里的 `@Published` 使得 `user` 发生变化时，UI 能够自动接收到更新。


#### **(2) ViewController（UIKit 视图层）**


```swift
import UIKit
import Combine

class UserViewController: UIViewController {
    private let viewModel = UserViewModel()
    private var cancellables = Set()
    
    private let nameLabel: UILabel = {
        let label = UILabel()
        label.textAlignment = .center
        return label
    }()
    
    private let button: UIButton = {
        let button = UIButton(type: .system)
        button.setTitle("Update User", for: .normal)
        return button
    }()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        setupUI()
        bindViewModel()
    }
    
    private func setupUI() {
        view.backgroundColor = .white
        view.addSubview(nameLabel)
        view.addSubview(button)
        
        nameLabel.translatesAutoresizingMaskIntoConstraints = false
        button.translatesAutoresizingMaskIntoConstraints = false
        
        NSLayoutConstraint.activate([
            nameLabel.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            nameLabel.centerYAnchor.constraint(equalTo: view.centerYAnchor),
            button.topAnchor.constraint(equalTo: nameLabel.bottomAnchor, constant: 20),
            button.centerXAnchor.constraint(equalTo: view.centerXAnchor)
        ])
        
        button.addTarget(self, action: #selector(buttonTapped), for: .touchUpInside)
    }
    
    private func bindViewModel() {
        viewModel.$user
            .sink { [weak self] user in
                self?.nameLabel.text = "User: \(user.name), Age: \(user.age)"
            }
            .store(in: &cancellables)
    }
    
    @objc private func buttonTapped() {
        viewModel.updateUser()
    }
}
```


### **UIKit + Combine 的数据绑定**


- viewModel.$user.sink {} 监听 user 的变化，并自动更新 UI
- @objc private func buttonTapped() 调用 updateUser() 修改数据，UI 立即刷新


## **4. MVVM 的优势总结**


| **特点** | **MVC** | **MVVM** |
| ---- | ---- | ---- |
| **模块化** | View 和 Controller 耦合较高 | View 与 ViewModel 分离，模块独立 |
| **可测试性** | 逻辑与 UI 混杂，难测试 | 业务逻辑独立，易测试 |
| **数据绑定** | 需要手动更新 UI | SwiftUI + @Published，UIKit + Combine |
| **适用场景** | 适合小型项目 | 适合中大型项目，特别是 SwiftUI |


### **什么时候使用 MVVM？**


- 适用于 **SwiftUI**，可以充分利用数据绑定特性
- 适用于 **复杂的 UI 交互**，让 View 层更轻量
- 适用于 **多人协作项目**，模块化程度更高


## **总结**


**ViewModel 在 MVVM 中起到承上启下的作用**：


- **连接 Model 和 View**
- **处理业务逻辑**
- **管理数据转换**
- **支持数据绑定，使 UI 自动更新**

对于 **SwiftUI**，MVVM 是默认推荐的架构；对于 **UIKit**，MVVM 结合 Combine 也是一种优化方案。这样可以让代码更清晰、易维护，并且提高可测试性。