---
title: swiftUI中通过闭包传递筛选条件到相应视图
date: 2025-03-27 14:19:59
tags: [swift编程,swiftUI,闭包]
---



# 我想在其他tabview里复用VideoListView，然后显示的视频只是对  ForEach(viewModel.videos) 中的数组进行相应的筛选

为了在其他 `TabView` 中复用 `VideoListView` 并根据不同的筛选条件显示不同的视频，我们可以通过向 `VideoListView` 传递一个筛选条件或视图模型来实现复用。这可以通过使用 **参数传递** 和 **视图模型的过滤** 来实现。


### 方案：


1. 在 `VideoListView` 中添加一个筛选条件的参数，比如根据视频类型、标签或其他属性进行筛选。
2. 传递不同的参数给 `VideoListView`，以便根据不同的条件渲染不同的视频。
<!--more-->
### 步骤：


1. **修改 VideoListView**：接受一个筛选条件或筛选函数。
2. **在 TabView 中使用不同的筛选条件**：根据不同的标签传递不同的筛选条件来复用 `VideoListView`。

### 代码示例：


#### 1. **修改 VideoListView，添加筛选条件**


```swift
import SwiftUI

struct VideoListView: View {
    @StateObject private var viewModel = VideoViewModel()
    @State private var searchText: String = ""  // 搜索文本
    @State private var searchTimer: Timer?  // 用于防抖处理
    
    let columns: [GridItem] = [
        GridItem(.flexible()),
        GridItem(.flexible())
    ]
    
    // 新增参数：一个用于筛选的视频数组
    var filter: (Video) -> Bool
    
    var body: some View {
        ZStack {
            if viewModel.isLoading {
                ProgressView("Loading videos...")
                    .progressViewStyle(CircularProgressViewStyle())
            } else {
                VStack {
                    HStack {
                        TextField("Search videos...", text: $searchText)
                            .padding()
                            .background(Color.white.opacity(0.2))
                            .cornerRadius(10)
                            .foregroundColor(.black)
                            .padding(.horizontal)
                            .padding(.top, 30)
                            .onChange(of: searchText) {
                                searchTimer?.invalidate() // 取消上一个计时器
                                // 启动新的计时器，防止频繁请求
                                searchTimer = Timer.scheduledTimer(withTimeInterval: 1, repeats: false) { _ in
                                    if searchText.isEmpty {
                                        viewModel.fetchVideos() // 如果搜索框为空，加载所有视频
                                    } else {
                                        viewModel.searchVideos(query: searchText) // 启动搜索
                                    }
                                }
                            }
                    }
                    .padding(.top) // 确保搜索框不会被其他元素遮挡

                    ScrollView(.vertical, showsIndicators: false) {
                        LazyVGrid(columns: columns, spacing: 10) {
                            // 通过filter筛选视频
                            ForEach(viewModel.videos.filter(filter)) { video in
                                VideoItemView(video: video)
                                    .aspectRatio(16/9, contentMode: .fit) // 　保持16:9的宽高比
                                    .padding(.bottom, 10) // 给每个视频项增加底部间距
                            }
                        }
                        .padding([.leading, .trailing])
                        .onAppear {
                            if viewModel.videos.isEmpty {
                                viewModel.fetchVideos() // 初次加载视频
                            }
                        }
                    }
                    .padding(.bottom)  // 防止底部被遮挡
                    .refreshable {
                        viewModel.fetchVideos()  // 下拉刷新
                    }
                }
                .edgesIgnoringSafeArea(.top)  // 防止顶部被遮挡
            }
        }
        .onAppear {
            if viewModel.videos.isEmpty {
                viewModel.fetchVideos() // 初次加载视频
            }
        }
    }
}
```


#### 2. **在 TabView 中使用不同的筛选条件**



```swift
struct ContentView: View {
    var body: some View {
        NavigationView {
            TabView {
                VideoListView(filter: { _ in true })
                    .tabItem {
                        Label("All Videos", systemImage: "video")
                    }

                VideoListView(filter: { video in
                    video.type == "Comedy"
                })
                .tabItem {
                    Label("Comedy", systemImage: "play.circle")
                }

                VideoListView(filter: { video in
                    video.likes &gt; 100
                })
                .tabItem {
                    Label("Popular", systemImage: "star.fill")
                }
            }
            .accentColor(.blue)
        }
    }
}
```


### 解释：


1. **filter 参数**：我们向 `VideoListView` 传递了一个筛选函数 `filter`，该函数会用来过滤 `viewModel.videos` 数组。你可以根据视频的不同属性进行筛选，比如视频类型、点赞数等。
2. **在 TabView 中复用 VideoListView**：通过为每个 `Tab` 传递不同的 `filter` 函数，我们可以在每个标签页中展示不同筛选后的内容。

### 其他建议：


- 你可以根据需要将筛选条件更细化，比如筛选视频的时长、上传时间等。
- 如果筛选条件很多，可以考虑将筛选逻辑提取到 `ViewModel` 中，而不是直接在视图中做筛选，以提高可维护性。






# 进入comedy tabview需要验证密码
要在进入 `Comedy` Tab 时进行密码验证，你可以在 `TabView` 的 `Comedy` 标签页面中添加一个密码验证逻辑。可以使用 `Alert` 或自定义视图来实现密码验证。


以下是一个简单的示例，在进入 `Comedy` Tab 时需要用户输入密码进行验证。


### 1. **添加密码验证逻辑**


在 `Comedy` Tab 中，你可以通过 `Alert` 或自定义的模态视图来实现密码验证。


#### 使用 Alert 实现密码验证：


你可以在 `Comedy` Tab 中使用 `Alert` 来进行密码验证。以下是修改后的代码：


```swift
import SwiftUI

struct ContentView: View {
    @State private var showPasswordAlert = false
    @State private var enteredPassword = ""
    @State private var isPasswordCorrect = false

    var body: some View {
        TabView {
            NavigationView {
                // 第一个Tab：显示所有视频
                VideoListView(filter: { _ in true })
                    .tabItem {
                        Label("All Videos", systemImage: "video")
                    }
            }

            // 第二个Tab：需要密码验证才能进入
            NavigationView {
                if isPasswordCorrect {
                    // 密码验证通过，显示视频内容
                    VideoListView(filter: { video in
                        video.type == "Comedy" // 根据视频类型进行筛选
                    })
                } else {
                    // 密码验证未通过，显示提示输入密码的视图
                    VStack {
                        Text("Enter Password to Access Comedy Videos")
                            .font(.title)
                            .padding()

                        SecureField("Enter Password", text: $enteredPassword)
                            .padding()
                            .background(Color.white.opacity(0.2))
                            .cornerRadius(10)
                            .foregroundColor(.black)
                            .padding(.horizontal)
                            .padding(.top, 30)
                        
                        Button(action: {
                            checkPassword()
                        }) {
                            Text("Submit")
                                .font(.title2)
                                .padding()
                                .background(Color.blue)
                                .foregroundColor(.white)
                                .cornerRadius(10)
                        }
                        .padding(.top, 20)
                    }
                    .padding()
                    .alert(isPresented: $showPasswordAlert) {
                        Alert(title: Text("Invalid Password"), message: Text("Please enter the correct password."), dismissButton: .default(Text("OK")))
                    }
                }
            }
            .tabItem {
                Label("Comedy", systemImage: "play.circle")
            }

            NavigationView {
                // 第三个Tab：只显示点赞数多的视频
                VideoListView(filter: { video in
                    video.likes &gt; 100 // 根据点赞数筛选
                })
                .tabItem {
                    Label("Popular", systemImage: "star.fill")
                }
            }
        }
        .accentColor(.blue)
    }
    
    func checkPassword() {
        // 假设正确密码是 "1234"
        if enteredPassword == "1234" {
            isPasswordCorrect = true
        } else {
            showPasswordAlert = true
        }
    }
}
```


### 2. **代码解释**


- **showPasswordAlert**: 控制密码错误时显示的警告框。
- **enteredPassword**: 绑定密码输入框的值。
- **isPasswordCorrect**: 用来判断密码是否正确，若正确则展示视频内容。
- **checkPassword()**: 验证密码的函数，简单示例中我们假设正确密码为 `1234`，如果密码正确，则 `isPasswordCorrect` 为 `true`，否则显示警告框。

#### 密码验证流程：


1. 初始时，如果 `isPasswordCorrect` 为 `false`，就会显示一个输入密码的界面。
2. 用户输入密码并点击“Submit”按钮时，调用 `checkPassword()` 进行验证。
3. 如果密码正确，`isPasswordCorrect` 会被设置为 `true`，然后显示视频列表。
4. 如果密码错误，则会弹出一个提示框提醒用户密码无效。

### 3. **优化 UI**


你可以进一步优化UI，例如将输入密码部分做成弹窗（`Modal`），或者加入一些动画效果来提升用户体验。


### 4. **自定义视图实现密码验证**


如果你不想使用 `Alert`，可以使用自定义视图来实现密码输入。


#### 自定义密码验证视图：


你可以将密码输入和验证部分做成一个 `Modal` 弹窗，点击进入 `Comedy` Tab 时弹出，直到用户正确输入密码才会显示视频内容。


```swift
struct PasswordView: View {
    @Binding var isPasswordCorrect: Bool
    @State private var enteredPassword = ""

    var body: some View {
        VStack {
            Text("Enter Password")
                .font(.title)
                .padding()

            SecureField("Password", text: $enteredPassword)
                .padding()
                .background(Color.white.opacity(0.2))
                .cornerRadius(10)
                .foregroundColor(.black)
                .padding(.horizontal)
            
            Button(action: {
                checkPassword()
            }) {
                Text("Submit")
                    .font(.title2)
                    .padding()
                    .background(Color.blue)
                    .foregroundColor(.white)
                    .cornerRadius(10)
            }
            .padding(.top, 20)
        }
        .padding()
    }
    
    func checkPassword() {
        if enteredPassword == "1234" {  // 密码检查
            isPasswordCorrect = true
        } else {
            enteredPassword = ""  // 错误密码清空输入框
        }
    }
}
```


然后，在 `ContentView` 中使用这个 `PasswordView`：


```swift
NavigationView {
    VStack {
        if isPasswordCorrect {
            VideoListView(filter: { video in
                video.type == "Comedy"
            })
        } else {
            PasswordView(isPasswordCorrect: $isPasswordCorrect)
        }
    }
}
```


### 总结


通过这种方式，你可以在进入 `Comedy` Tab 时进行密码验证。如果密码验证通过，用户将能够查看视频内容。如果密码验证失败，可以显示相应的提示信息或清空输入框让用户重新尝试。



### 基于你提供的代码，修改后的版本：


首先，我会在 `ContentView` 中加入密码验证逻辑，并在进入 `Comedy` Tab 时进行密码检查。密码验证失败时，用户会看到一个提示框（可以选择使用 `Alert` 或者自定义视图），直到密码验证成功才能进入 `Comedy` 页面。


### 代码修改：


```swift
import SwiftUI

struct ContentView: View {
    @State private var showPasswordAlert = false
    @State private var enteredPassword = ""
    @State private var isPasswordCorrect = false

    var body: some View {
        TabView {
            NavigationView {
                // 第一个Tab：显示所有视频
                VideoListView(filter: { _ in true })
                    .tabItem {
                        Label("All Videos", systemImage: "video")
                    }
            }

            // 第二个Tab：需要密码验证才能进入
            NavigationView {
                if isPasswordCorrect {
                    // 密码验证通过，显示视频内容
                    VideoListView(filter: { video in
                        video.type == "Comedy" // 根据视频类型进行筛选
                    })
                } else {
                    // 密码验证未通过，显示密码输入界面
                    PasswordView(isPasswordCorrect: $isPasswordCorrect)
                }
            }
            .tabItem {
                Label("Comedy", systemImage: "play.circle")
            }

            NavigationView {
                // 第三个Tab：只显示点赞数多的视频
                VideoListView(filter: { video in
                    video.likes &gt; 100 // 根据点赞数筛选
                })
                .tabItem {
                    Label("Popular", systemImage: "star.fill")
                }
            }
        }
        .accentColor(.blue)
    }
}

struct PasswordView: View {
    @Binding var isPasswordCorrect: Bool
    @State private var enteredPassword = ""

    var body: some View {
        VStack {
            Text("Enter Password to Access Comedy Videos")
                .font(.title)
                .padding()

            SecureField("Password", text: $enteredPassword)
                .padding()
                .background(Color.white.opacity(0.2))
                .cornerRadius(10)
                .foregroundColor(.black)
                .padding(.horizontal)
            
            Button(action: {
                checkPassword()
            }) {
                Text("Submit")
                    .font(.title2)
                    .padding()
                    .background(Color.blue)
                    .foregroundColor(.white)
                    .cornerRadius(10)
            }
            .padding(.top, 20)
        }
        .padding()
        .alert(isPresented: $isPasswordCorrect) {
            Alert(title: Text("Invalid Password"), message: Text("Please enter the correct password."), dismissButton: .default(Text("OK")))
        }
    }

    func checkPassword() {
        if enteredPassword == "1234" { // 假设密码是 "1234"
            isPasswordCorrect = true
        } else {
            enteredPassword = "" // 如果密码错误，清空输入框
        }
    }
}
```


### 代码说明：


1. **ContentView**:

- 在 `TabView` 中添加了一个新的选项 `Comedy` Tab，只有在 `isPasswordCorrect` 为 `true` 时，用户才能看到视频列表。
- 密码输入部分封装在 `PasswordView` 视图中，只有当密码验证通过后，用户才能看到视频内容。
2. **PasswordView**:

- `PasswordView` 用来显示密码输入界面，用户输入密码并点击“Submit”按钮时，会调用 `checkPassword` 方法验证密码。
- 密码验证通过后，`isPasswordCorrect` 被设置为 `true`，用户就能看到视频列表。
- 如果密码错误，输入框会被清空，用户可以重新尝试输入。
3. **密码验证**:

- 在这个简单的示例中，密码设置为 `1234`，你可以根据需求修改成实际的密码或者通过其他方式来进行验证。
4. **Alert 提示**:

- 当密码输入错误时，会弹出一个提示框 `Alert` 提示用户重新输入。

### 如何使用：


- 用户点击进入 `Comedy` Tab 后，如果密码未通过验证，将会看到一个密码输入界面。
- 输入正确密码后，才能加载 `Comedy` Tab 中的内容（如视频列表等）。
- 如果输入错误，用户将继续看到密码输入界面，直到输入正确密码为止。


这样，你就可以在 `Comedy` Tab 中实现密码验证了。如果还有其他需求，或希望进一步调整界面和功能，欢迎告诉我！


# struct VideoListView: View中直接定义var filter: (Video) -> Bool 就可以从其他view中传递属性这是什么原理

在 SwiftUI 中，`var filter: (Video) -> Bool` 是通过 **视图的参数传递**实现的，类似于普通函数参数传递的原理。这是 Swift 中常见的功能，即通过传递参数来让一个视图（`VideoListView`）在不同的上下文中根据不同的需求表现出不同的行为。


### 原理解释：


1. **视图作为结构体**：
`VideoListView` 是一个 `View` 结构体，而 Swift 中的 `View` 通常是值类型（`struct`）。这意味着每次创建视图时，都会复制该视图的实例。而你通过 `filter` 这样的变量来改变视图的显示逻辑。
2. **传递闭包参数**：
在 `VideoListView` 中定义 `var filter: (Video) -> Bool`，其实是定义了一个 **闭包（Closure）参数**。闭包是 Swift 中强大的功能，它让你能够将代码块（即函数）作为参数传递，作为执行逻辑的一部分。

- `filter` 是一个接受 `Video` 对象并返回 `Bool` 的函数（或者说是一个闭包）。
- 你可以在创建 `VideoListView` 时，通过参数传递不同的闭包来筛选视频（比如根据视频类型筛选）。
3. **动态行为**：
通过传递不同的闭包，你可以让 `VideoListView` 在不同的上下文中执行不同的筛选操作。这个闭包使得视图的内容和行为变得更为动态和灵活。例如：

```swift
VideoListView(filter: { video in
    video.type == "搞笑"
})
```

在上面的代码中，`filter` 传递了一个闭包，该闭包筛选出类型为 "搞笑" 的视频。你可以根据具体的条件传递不同的闭包，从而让 `VideoListView` 在不同 Tab 中展示不同类型的视频。
4. **视图的重绘和更新**：
SwiftUI 会自动观察传递的参数（如 `filter`）。如果你在其他视图中改变了 `filter` 变量，SwiftUI 会重新计算并刷新视图。因此，视图的显示会基于新的闭包进行更新。

### 总结：


通过在 `VideoListView` 中定义 `filter` 变量，SwiftUI 允许你将行为（如视频筛选逻辑）作为参数传递，从而让视图更加灵活。闭包作为参数的传递使得视图能够根据外部传入的条件动态调整自己的显示内容和行为，这也是 SwiftUI 中“声明式 UI”设计的一个重要特点。


这个原理基于 **函数式编程** 的概念，将行为和数据分离，可以让代码更加灵活、可重用，符合 SwiftUI 的设计理念。