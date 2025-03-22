---
title: 深入理解mvvm设计模式
date: 2025-03-21 18:01:43
tags: swift编程
---


# 用MVVM 模式解释

### **MVVM（Model-View-ViewModel）模式**


MVVM 是 SwiftUI 推荐的架构模式，它将应用的逻辑分成 **Model（数据层）**、**View（视图层）** 和 **ViewModel（视图模型层）**，实现 **数据与 UI 解耦**，提升代码的可维护性。



## **MVVM 三大核心组件**


### **1. Model（数据层）**


- 负责存储和管理数据，可以是结构体 (struct) 或类 (class)。
- 不直接与 UI 交互，通常遵循 Codable 或 Identifiable 协议。

```swift
struct Video: Identifiable, Codable {
    let id: Int
    let title: String
    let url: String
    var likes: Int
}
```



### **2. ViewModel（视图模型层）**


- **核心作用**：负责 **业务逻辑** 和 **数据处理**，并通过 @Published 触发 UI 更新。
- 遵循 ObservableObject 协议，以便 View 可以订阅它的数据变化。

```swift
import SwiftUI

class VideoViewModel: ObservableObject {
    @Published var videos: [Video] = []
    
    init() {
        fetchVideos()
    }

    func fetchVideos() {
        // 模拟获取数据
        self.videos = [
            Video(id: 1, title: "SwiftUI 教程", url: "https://example.com/video1.mp4", likes: 100),
            Video(id: 2, title: "iOS 开发", url: "https://example.com/video2.mp4", likes: 200)
        ]
    }

    func likeVideo(id: Int) {
        if let index = videos.firstIndex(where: { $0.id == id }) {
            videos[index].likes += 1
        }
    }
}
```



### **3. View（视图层）**


- 负责 UI 显示，并订阅 ViewModel 数据。
- 使用 @StateObject 或 @ObservedObject 绑定 ViewModel。

```swift
struct VideoListView: View {
    @StateObject var viewModel = VideoViewModel()

    var body: some View {
        NavigationView {
            List(viewModel.videos) { video in
                HStack {
                    VStack(alignment: .leading) {
                        Text(video.title).font(.headline)
                        Text("👍 \(video.likes)").font(.subheadline)
                    }
                    Spacer()
                    Button(action: {
                        viewModel.likeVideo(id: video.id)
                    }) {
                        Image(systemName: "hand.thumbsup.fill")
                    }
                }
            }
            .navigationTitle("视频列表")
        }
    }
}
```



## **MVVM 的优势**


1. **分离关注点**：Model 负责数据，ViewModel 负责业务逻辑，View 仅负责 UI 渲染。
2. **可测试性强**：ViewModel 易于单元测试，不依赖 UI 层。
3. **易扩展和维护**：业务逻辑独立于 UI，方便后期修改。
4. **提升代码复用性**：多个 View 可共享相同的 ViewModel 逻辑。


## **SwiftUI 中 MVVM 的最佳实践**


✅ **数据绑定**：用 `@Published` 让 `ViewModel` 变动时自动更新 UI。
✅ **解耦 UI 和数据逻辑**：避免在 `View` 里写太多逻辑，把逻辑放进 `ViewModel`。
✅ **异步数据加载**：`ViewModel` 负责网络请求、数据库读取等任务。
✅ **多 View 共享 ViewModel**：通过 `@EnvironmentObject` 让多个 `View` 共享数据。



### **MVVM 进阶：结合网络请求**


如果 `ViewModel` 需要从服务器获取数据，可以使用 `async/await` 处理异步请求：


```swift
class VideoViewModel: ObservableObject {
    @Published var videos: [Video] = []
    
    func fetchVideos() async {
        guard let url = URL(string: "https://api.example.com/videos") else { return }
        do {
            let (data, _) = try await URLSession.shared.data(from: url)
            let decodedVideos = try JSONDecoder().decode([Video].self, from: data)
            DispatchQueue.main.async {
                self.videos = decodedVideos
            }
        } catch {
            print("获取数据失败: \(error)")
        }
    }
}
```


在 `View` 里调用：


```swift
struct VideoListView: View {
    @StateObject var viewModel = VideoViewModel()
    
    var body: some View {
        List(viewModel.videos) { video in
            Text(video.title)
        }
        .task {
            await viewModel.fetchVideos()
        }
    }
}
```



### **总结**


- **MVVM = Model（数据）+ ViewModel（逻辑）+ View（UI）**。
- **ViewModel 通过 @Published 绑定数据，View 自动更新**。
- **解耦 UI 和逻辑，便于扩展、测试和复用**。

你可以在你的 **视频播放项目** 里使用 MVVM，比如：


- **Model**：存储视频信息（Video）。
- **ViewModel**：管理视频数据、播放状态、点赞逻辑。
- **View**：显示视频列表，并订阅 ViewModel 变化。
