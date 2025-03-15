---
title: swiftUI中ObservableObject和EnvironmentObject用法实例
date: 2025-03-15 10:48:13
tags:
---

# 应用场景
我使用swiftui从后端拿到了一个视频信息数组，我想在不同的视图下对都使用该数组，只是对该数组进行相关条件筛选比如按不同规则过滤
# 具体实例
在 SwiftUI 中，你可以使用 **ObservableObject** 来存储和管理视频信息数组，并在不同的视图中共享它。然后，在具体视图中应用不同的筛选条件。



### **方案**


1. **创建 VideoManager 作为数据源**

- 让 VideoManager 继承 ObservableObject，存储视频数组，并提供筛选方法。
2. **在多个视图中共享 VideoManager**

- 使用 .environmentObject() 传递 VideoManager
- 在子视图中使用 .filteredVideos(...) 获取筛选后的视频列表。


### **示例代码**


#### **1. 创建 VideoManager**


```swift
import SwiftUI

// 视频模型
struct Video: Identifiable {
    let id: Int
    let title: String
    let category: String
    let likes: Int
}

// ObservableObject 作为数据管理器
class VideoManager: ObservableObject {
    @Published var videos: [Video] = []

    // 按类型筛选
    func filteredVideos(byCategory category: String) -&gt; [Video] {
        return videos.filter { $0.category == category }
    }
    
    // 按点赞数排序
    func sortedVideosByLikes() -&gt; [Video] {
        return videos.sorted { $0.likes &gt; $1.likes }
    }
}
```



#### **2. 创建主视图**


```swift
struct ContentView: View {
    @StateObject private var videoManager = VideoManager()

    var body: some View {
        NavigationView {
            VStack {
                NavigationLink("按分类查看", destination: FilteredView().environmentObject(videoManager))
                NavigationLink("按点赞排序", destination: SortedView().environmentObject(videoManager))
            }
            .onAppear {
                // 加载测试数据
                videoManager.videos = [
                    Video(id: 1, title: "SwiftUI 教程", category: "教育", likes: 100),
                    Video(id: 2, title: "健身训练", category: "健身", likes: 50),
                    Video(id: 3, title: "Swift 开发", category: "教育", likes: 200)
                ]
            }
        }
    }
}
```



#### **3. 分类筛选视图**


```swift
struct FilteredView: View {
    @EnvironmentObject var videoManager: VideoManager

    var body: some View {
        List(videoManager.filteredVideos(byCategory: "教育")) { video in
            Text(video.title)
        }
        .navigationTitle("教育类视频")
    }
}
```



#### **4. 按点赞数排序视图**


```swift
struct SortedView: View {
    @EnvironmentObject var videoManager: VideoManager

    var body: some View {
        List(videoManager.sortedVideosByLikes()) { video in
            HStack {
                Text(video.title)
                Spacer()
                Text("\(video.likes) 👍")
            }
        }
        .navigationTitle("热门视频")
    }
}
```



### **总结**


✅ **VideoManager 作为全局数据存储**
✅ **不同视图对 videos 进行筛选**
✅ **使用 .environmentObject() 共享数据**


这样，你就能在不同的视图中使用同一个视频数组，并进行不同的筛选和排序了！ 🎬🚀