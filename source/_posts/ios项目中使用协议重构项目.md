---
title: ios项目中使用协议重构项目
date: 2025-03-28 22:25:12
tags: [重构,swift编程]
---



# json数据结构示例
[
  {
    "author": "駱賓王",
    "paragraphs": [
      "西陸蟬聲唱，南冠客思侵。",
      "那堪玄鬢影，來對白頭吟。",
      "露重飛難進，風多響易沈。",
      "無人信高潔，誰爲表予心。"
    ],
    "tags": [
      "唐诗三百首",
      "咏物",
      "咏物诗",
      "五言律诗"
    ],
    "title": "在嶽詠蟬",
    "id": "c65539db-4e2b-4ce4-a22b-563b6ef3f4f1"
  },
  {
    "author": "陳子昂",
    "paragraphs": [
      "前不見古人，後不見來者。",
      "念天地之悠悠，獨愴然而涕下。"
    ],
    "tags": [
      "唐诗三百首",
      "隋・唐・五代",
      "八年级下册(课外)",
      "伤怀",
      "初中古诗",
      "七言古诗"
    ],
    "title": "登幽州臺歌",
    "id": "c244a5b4-0ed0-48fe-8694-95309acac184"
  }]



# SwiftUI 构建这个应用的一个基本示例：


### 1. 数据模型


我们需要先定义数据模型来解析你的 JSON 数据。


```swift
import Foundation

struct Poem: Identifiable, Codable {
    var id: String
    var author: String
    var paragraphs: [String]
    var tags: [String]
    var title: String
}
```


### 2. 示例 JSON 解析


接下来，我们创建一个 ViewModel 来加载和解析这个 JSON 数据。


```swift
import Foundation

class PoemViewModel: ObservableObject {
    @Published var poems: [Poem] = []
    
    init() {
        loadPoems()
    }
    
    func loadPoems() {
        // 假设你已经有一个 JSON 数据作为字符串或从文件中读取
        let jsonData = """
        [
            {
                "author": "駱賓王",
                "paragraphs": [
                    "西陸蟬聲唱，南冠客思侵。",
                    "那堪玄鬢影，來對白頭吟。",
                    "露重飛難進，風多響易沈。",
                    "無人信高潔，誰爲表予心。"
                ],
                "tags": [
                    "唐诗三百首",
                    "咏物",
                    "咏物诗",
                    "五言律诗"
                ],
                "title": "在嶽詠蟬",
                "id": "c65539db-4e2b-4ce4-a22b-563b6ef3f4f1"
            },
            {
                "author": "陳子昂",
                "paragraphs": [
                    "前不見古人，後不見來者。",
                    "念天地之悠悠，獨愴然而涕下。"
                ],
                "tags": [
                    "唐诗三百首",
                    "隋・唐・五代",
                    "八年级下册(课外)",
                    "伤怀",
                    "初中古诗",
                    "七言古诗"
                ],
                "title": "登幽州臺歌",
                "id": "c244a5b4-0ed0-48fe-8694-95309acac184"
            }
        ]
        """.data(using: .utf8)!
        
        let decoder = JSONDecoder()
        do {
            let decodedPoems = try decoder.decode([Poem].self, from: jsonData)
            self.poems = decodedPoems
        } catch {
            print("Error decoding JSON: \(error)")
        }
    }
}
```


### 3. 主界面 (PoemListView)


这是展示所有诗词的界面。


```swift
import SwiftUI

struct PoemListView: View {
    @StateObject var viewModel = PoemViewModel()
    
    var body: some View {
        NavigationView {
            List(viewModel.poems) { poem in
                NavigationLink(destination: PoemDetailView(poem: poem)) {
                    VStack(alignment: .leading) {
                        Text(poem.title)
                            .font(.headline)
                        Text(poem.author)
                            .font(.subheadline)
                            .foregroundColor(.gray)
                        HStack {
                            ForEach(poem.tags, id: \.self) { tag in
                                Text(tag)
                                    .font(.caption)
                                    .padding(4)
                                    .background(Color.gray.opacity(0.2))
                                    .cornerRadius(8)
                            }
                        }
                    }
                }
            }
            .navigationTitle("诗词列表")
        }
    }
}
```


### 4. 诗词详情页面 (PoemDetailView)


这个页面展示诗词的完整内容。


```swift
import SwiftUI

struct PoemDetailView: View {
    var poem: Poem
    
    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 20) {
                Text(poem.title)
                    .font(.largeTitle)
                    .fontWeight(.bold)
                
                Text("作者: \(poem.author)")
                    .font(.title3)
                    .foregroundColor(.gray)
                
                VStack(alignment: .leading, spacing: 15) {
                    ForEach(poem.paragraphs, id: \.self) { paragraph in
                        Text(paragraph)
                            .font(.body)
                            .padding(.bottom, 10)
                    }
                }
                
                VStack(alignment: .leading) {
                    Text("标签:")
                        .font(.title2)
                        .fontWeight(.semibold)
                    HStack {
                        ForEach(poem.tags, id: \.self) { tag in
                            Text(tag)
                                .font(.caption)
                                .padding(6)
                                .background(Color.blue.opacity(0.1))
                                .cornerRadius(12)
                        }
                    }
                }
                .padding(.top, 30)
            }
            .padding()
        }
        .navigationTitle("诗词详情")
    }
}
```


### 5. 主界面展示


通过 `PoemListView` 展示所有诗词，并且通过 `NavigationLink` 来跳转到每首诗的详情页面。


### 6. 运行效果


- 在 `PoemListView` 中，你可以看到所有的诗词标题、作者和标签。
- 点击进入每首诗的详情页面，展示诗句内容及标签。



# 我的json文件在当前ios项目的JsonData文件夹下，json文件的名字为tangshi300.json

如果你的 JSON 文件保存在项目的 `JsonData` 文件夹下，并且文件名为 `tangshi300.json`，你可以按照下面的方法在 SwiftUI 项目中加载这个文件。


### 步骤：加载本地 JSON 文件


我们将更新 `PoemViewModel` 中的 `loadPoems` 方法，使它能够从本地加载 `tangshi300.json` 文件。


### 1. 修改 PoemViewModel 来加载本地 JSON 文件


在 `PoemViewModel` 中，我们会使用 `Bundle.main` 来加载本地的 JSON 文件。


```swift
import Foundation

class PoemViewModel: ObservableObject {
    @Published var poems: [Poem] = []
    
    init() {
        loadPoems()
    }
    
    func loadPoems() {
        // 获取本地 JSON 文件的 URL
        guard let url = Bundle.main.url(forResource: "tangshi300", withExtension: "json", subdirectory: "JsonData") else {
            print("JSON 文件未找到")
            return
        }
        
        do {
            // 读取文件内容
            let jsonData = try Data(contentsOf: url)
            
            // 使用 JSONDecoder 解析 JSON 数据
            let decoder = JSONDecoder()
            let decodedPoems = try decoder.decode([Poem].self, from: jsonData)
            self.poems = decodedPoems
        } catch {
            print("加载或解析 JSON 文件时出错: \(error)")
        }
    }
}
```


### 2. 确保 tangshi300.json 被正确添加到 Xcode 项目中


确保 `tangshi300.json` 文件已经正确地添加到项目中，且在 Xcode 中的设置里勾选了“Copy Bundle Resources”，使它能够被打包进应用。


1. 在 Xcode 项目中右键点击你的 `JsonData` 文件夹，选择 `Add Files to [Your Project]`，并选择 `tangshi300.json` 文件。
2. 确保选中了 `Copy items if needed` 和 `Add to targets`，然后点击 `Add`。

### 3. 更新 Info.plist（如果需要）


通常情况下，直接将文件添加到项目中并不会需要修改 `Info.plist`，但是如果你遇到任何文件读取问题，可以检查 `Info.plist` 中的路径配置，确保文件在正确的位置。


### 4. 运行和测试


完成这些修改后，当你运行应用时，`PoemViewModel` 会从项目的 `JsonData` 文件夹加载并解析 `tangshi300.json` 文件中的数据，并通过 SwiftUI 界面展示出来。




# 将PoemListView() 放进我的tabview

将 `PoemListView` 放进 `TabView` 里非常简单。你只需要将 `TabView` 作为根视图，并将 `PoemListView` 作为其中的一个 tab 来展示。


### 1. 修改主界面 (TabView)


你可以在主界面中使用 `TabView` 来组织多个视图。每个标签页面可以展示不同的视图（比如 `PoemListView`，其他内容视图等）。


下面是如何将 `PoemListView` 放进 `TabView` 的示例：


```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        TabView {
            // 第一标签页：诗词列表
            PoemListView()
                .tabItem {
                    Label("诗词", systemImage: "book.fill")
                }
            
            // 你可以添加其他标签页面
            Text("其他页面")
                .tabItem {
                    Label("其他", systemImage: "star.fill")
                }
        }
        .accentColor(.blue) // 可以自定义选中标签的颜色
    }
}
```


### 2. PoemListView


这个视图已经是我们之前构建的 `PoemListView`，不需要做修改。


```swift
import SwiftUI

struct PoemListView: View {
    @StateObject var viewModel = PoemViewModel()
    
    var body: some View {
        NavigationView {
            List(viewModel.poems) { poem in
                NavigationLink(destination: PoemDetailView(poem: poem)) {
                    VStack(alignment: .leading) {
                        Text(poem.title)
                            .font(.headline)
                        Text(poem.author)
                            .font(.subheadline)
                            .foregroundColor(.gray)
                        HStack {
                            ForEach(poem.tags, id: \.self) { tag in
                                Text(tag)
                                    .font(.caption)
                                    .padding(4)
                                    .background(Color.gray.opacity(0.2))
                                    .cornerRadius(8)
                            }
                        }
                    }
                }
            }
            .navigationTitle("诗词列表")
        }
    }
}
```


### 3. 运行效果


现在，`PoemListView` 已经是 `TabView` 的其中一个标签页了。你可以通过点击标签来切换不同的视图。


- 你可以添加更多的标签视图，只需继续在 `TabView` 中添加 `tabItem` 即可。
- 通过 `.tabItem` 设置每个标签的名称和图标（例如使用 SF Symbols 图标）。

### 4. 自定义 TabBar 样式


如果你想自定义 TabBar 的样式或颜色，你可以使用 `.accentColor()` 来修改选中的标签颜色。


例如：


```swift
TabView {
    PoemListView()
        .tabItem {
            Label("诗词", systemImage: "book.fill")
        }
    Text("其他页面")
        .tabItem {
            Label("其他", systemImage: "star.fill")
        }
}
.accentColor(.blue)  // 设置选中标签的颜色
```


### 总结


- 使用 `TabView` 包裹你的视图，每个标签页通过 `tabItem` 设置。
- `PoemListView` 放入 `TabView` 后会成为一个标签页，你可以轻松切换视图。

这样，你就可以将 `PoemListView` 和其他视图放在同一个 TabBar 中，用户可以切换不同的功能页面。如果有更多自定义需求或问题，随时告诉我！



# Add file to xcode project 的作用

在 Xcode 中，`Add Files to Xcode Project` 是一个常见的操作，它允许你将外部文件（如图像、JSON 文件、代码文件、资源文件等）添加到 Xcode 项目中。这个操作的主要作用和意义包括：


### 1. **将文件添加到项目中**


`Add Files to Xcode Project` 使得你能够将文件添加到 Xcode 项目中，从而使这些文件可以被 Xcode 识别和管理。例如，你可以将 JSON 文件、图像、音频文件、源代码文件、配置文件等添加到项目中。


**作用**：


- 文件将被纳入项目管理系统，Xcode 会帮助你跟踪这些文件。
- 你可以使用这些文件作为项目的资源、数据文件或代码文件。

### 2. **控制文件是否复制到项目目录**


当你通过 `Add Files to Xcode Project` 添加文件时，Xcode 会询问你是否需要将这些文件复制到项目目录（本地磁盘）。你可以根据需求选择是否复制文件：


- **Copy items if needed**：勾选此选项时，Xcode 会将你添加的文件复制到项目的文件夹中（例如项目的资源文件夹）。即使原文件保存在其他地方，Xcode 会将它们复制过来，并作为项目的一部分来管理。
- **Do not copy items**：如果不勾选该选项，Xcode 会添加文件的引用而不会复制文件到项目目录中。换句话说，文件仍然保留在其原始位置，Xcode 只会在项目中引用该文件。

**选择的含义**：


- 如果你选择 `Copy items if needed`，文件将成为项目的一部分，之后即使原始文件发生变化，Xcode 也会使用复制的文件。
- 如果你选择 `Do not copy items`，你只能在原始位置使用文件。如果原文件被移动、删除或修改，Xcode 将无法找到它。

### 3. **选择是否添加到目标**


Xcode 会让你选择将这些文件添加到一个或多个目标（Target）中。目标是你想要构建和部署的内容（例如应用、扩展、测试目标等）。


**作用**：


- 如果你选择将文件添加到目标中，那么这些文件将被包括在构建过程里。你可以将不同的资源文件或代码文件关联到不同的目标中。
- 例如，你可能有多个目标（比如 `iOS App` 和 `WatchOS App`），你可以将文件分别添加到适当的目标中，确保它们在适当的平台上可用。

### 4. **设置文件的访问控制**


当你添加文件时，Xcode 会为每个文件设置权限。你可以控制文件是公开的（可以在整个项目中访问）还是私有的（仅在某些部分访问）。


### 5. **添加文件并确保它们能被正确打包**


通过 `Add Files to Xcode Project` 添加的资源文件（如图片、JSON 文件、音频文件等）会被正确地添加到项目的 `Build Phases` 中。例如，如果你添加了一个 JSON 文件，它会被自动添加到项目的 `Copy Bundle Resources` 阶段，确保文件在应用运行时可以正确访问。


### 6. **管理文件版本**


如果项目使用了版本控制（例如 Git），将文件添加到 Xcode 项目中会使得这些文件成为 Git 版本控制的一部分。你可以通过 Git 跟踪和管理文件的修改。


### 小结


`Add Files to Xcode Project` 的作用就是将文件（如资源文件、数据文件、源代码文件等）添加到 Xcode 项目中，使得它们能够被 Xcode 管理、编译、打包，并且可以在应用程序中使用。你可以选择是否将文件复制到项目目录、是否添加到特定目标、以及是否将其纳入版本控制。

# 用户问题
我的jsondata在项目的根目录下
# ChatGPT
好的，既然你的 `jsondata` 文件夹位于项目的根目录下，加载这个 JSON 文件的方式会更简单。你可以直接通过指定 `subdirectory` 来访问根目录下的 `jsondata` 文件夹。


假设项目结构如下：


```markdown
MyProject
│
├── JsonData
│   └── tangshi300.json
│
└── PoemViewModel
    └── PoemViewModel.swift
```


在这种结构下，你可以使用以下方法来加载 JSON 文件。


### 1. 修改 PoemViewModel.swift 中的 loadPoems 方法


```swift
import Foundation

class PoemViewModel: ObservableObject {
    @Published var poems: [Poem] = []
    
    init() {
        loadPoems()
    }
    
    func loadPoems() {
        // 获取 JSON 文件的 URL，文件位于项目根目录的 jsondata 文件夹
        guard let url = Bundle.main.url(forResource: "tangshi300", withExtension: "json", subdirectory: "JsonData") else {
            print("JSON 文件未找到")
            return
        }
        
        do {
            // 读取 JSON 文件数据
            let jsonData = try Data(contentsOf: url)
            
            // 使用 JSONDecoder 解码 JSON 数据
            let decoder = JSONDecoder()
            let decodedPoems = try decoder.decode([Poem].self, from: jsonData)
            self.poems = decodedPoems
        } catch {
            print("加载或解析 JSON 文件时出错: \(error)")
        }
    }
}
```


### 2. 文件路径解释


- `Bundle.main.url(forResource: "tangshi300", withExtension: "json", subdirectory: "JsonData")`


`subdirectory: "JsonData"`：指定根目录下的 `JsonData` 文件夹作为 `subdirectory`，告诉 `Bundle` 去 `JsonData` 文件夹中查找 `tangshi300.json` 文件。

### 3. 确保 JSON 文件被正确添加到 Xcode 项目中


请确保你的 `JsonData` 文件夹中的 `tangshi300.json` 文件已经被正确添加到 Xcode 项目，并且文件的 `Target Membership` 已经勾选为该应用的目标。


- 在 Xcode 中，右键点击 `JsonData` 文件夹，选择 `Add Files to [Your Project]`。
- 在弹出的对话框中，确保勾选了 `Copy items if needed` 和 `Add to targets`，然后点击 `Add`。

### 总结


通过 `subdirectory: "JsonData"`，你可以轻松访问根目录下的 `JsonData` 文件夹，并加载其中的 `tangshi300.json` 文件。如果有其他问题，随时告诉我！
# 用户问题
JSON 文件未找到
# ChatGPT
如果你遇到 `"JSON 文件未找到"` 错误，可能是路径或者文件没有正确添加到项目中。我们可以一步步检查和解决问题：


### 1. **检查文件路径**


确保 JSON 文件的路径是正确的。由于你已经确认文件在项目的根目录下的 `JsonData` 文件夹中，我们可以确认一下路径是否正确。


- 确保 `tangshi300.json` 文件确实存在于 `JsonData` 文件夹中，并且 `JsonData` 文件夹在 Xcode 项目的根目录下。
- `subdirectory: "JsonData"` 应该指向根目录中的文件夹，而不是项目内其他位置。

### 2. **检查文件是否正确添加到 Xcode 项目中**


确保你的 `JsonData` 文件夹和文件已正确添加到 Xcode 中，并且已经设置为包含在构建中：


- 在 Xcode 中右键点击 `JsonData` 文件夹，选择 **Add Files to [Your Project]**。
- 在弹出的对话框中，确保：


勾选了 **Copy items if needed**（如果你希望将文件复制到项目文件夹中）。


勾选了 **Add to targets**，并确保选择了你的应用目标。
- 这样可以确保文件会被正确包含在项目的构建过程中。

### 3. **尝试直接访问文件路径**


如果上述方法仍然无法找到文件，可以尝试手动检查文件路径。你可以在项目中使用文件的绝对路径来测试是否可以访问文件。


# 重构
我还有唐诗，宋词等也有类似前端需求，但是数据结构稍微不一样，如何复用现在的代码。

你可以将其他类似的数据结构示例发给我，我会帮助你如何复用当前的代码以支持不同数据结构的情况。针对你提到的唐诗、宋词等内容，数据结构虽然不完全相同，但可以通过适当的抽象和处理来实现代码复用。


我们可以通过以下几种方式来优化和复用当前的代码：


### 1. **使用通用模型**


你可以创建一个通用的数据模型来处理所有诗词和词曲类型的数据，然后通过条件判断或者配置来决定显示的内容和结构。具体来说，可以创建一个基类或者协议，所有诗词类型都遵循该协议。


### 2. **解析不同格式的 JSON**


在解析 JSON 时，基于不同的数据结构，可以通过配置不同的 `Decodable` 模型来支持不同格式的 JSON。根据 JSON 文件的结构差异，只需要在 `PoemViewModel` 中增加逻辑来判断并加载适当的模型。


### 3. **根据数据类型选择展示方式**


通过简单的条件判断，根据不同的数据类型（如唐诗、宋词等）来展示不同的布局或视图。



# 其他类型数据示例
[
    {
        "chapter": "学而篇",
        "paragraphs": [
            "子曰：“学而时习之，不亦说乎？有朋自远方来，不亦乐乎？人不知而不愠，不亦君子乎？”",
            "有子曰：“其为人也孝弟，而好犯上者，鲜矣；不好犯上而好作乱者，未之有也。君子务本，本立而道生。孝弟也者，其为仁之本与！”",
            "子曰：“巧言令色，鲜矣仁！”",
            "曾子曰：“吾日三省吾身：为人谋而不忠乎？与朋友交而不信乎？传不习乎？”",
            "子曰：“道千乘之国，敬事而信，节用而爱人，使民以时。”",
            "子曰：“弟子入则孝，出则弟，谨而信，泛爱众，而亲仁，行有余力，则以学文。”",
            "子夏曰：“贤贤易色；事父母，能竭其力；事君，能致其身；与朋友交，言而有信。虽曰未学，吾必谓之学矣。”",
            "子曰：“君子不重则不威，学则不固。主忠信，无友不如己者，过，则勿惮改。”",
            "曾子曰：“慎终追远，民德归厚矣。”",
            "子禽问于子贡曰：“夫子至于是邦也，必闻其政，求之与，抑与之与？”子贡曰：“夫子温、良、恭、俭、让以得之。夫子之求之也，其诸异乎人之求之与？”",
            "子曰：“父在，观其志；父没，观其行；三年无改于父之道，可谓孝矣。”",
            "有子曰：“礼之用，和为贵。先王之道，斯为美，小大由之。有所不行，知和而和，不以礼节之，亦不可行也。”",
            "有子曰：“信近于义，言可复也。恭近于礼，远耻辱也。因不失其亲，亦可宗也。”",
            "子曰：“君子食无求饱，居无求安，敏于事而慎于言，就有道而正焉。可谓好学也已。”",
            "子贡曰：“贫而无谄，富而无骄，何如？”子曰：“可也。未若贫而乐，富而好礼者也。”子贡曰：“《诗》云：‘如切如磋，如琢如磨’，其斯之谓与？”子曰：“赐也，始可与言《诗》已矣，告诸往而知来者。”",
            "子曰：“不患人之不己知，患不知人也。”"
        ]
    },
    {
        "chapter": "为政篇",
        "paragraphs": [
            "子曰：“为政以德，譬如北辰，居其所而众星共之。”",
            "子曰：“《诗》三百，一言以蔽之，曰：‘思无邪’。”",
            "子曰：“道之以政，齐之以刑，民免而无耻。道之以德，齐之以礼，有耻且格。”",
            "子曰：“吾十有五而志于学，三十而立，四十而不惑，五十而知天命，六十而耳顺，七十而从心所欲，不逾矩。”",
            "孟懿子问孝，子曰：“无违。”樊迟御，子告之曰：“孟孙问孝于我，我对曰‘无违’。”樊迟曰：“何谓也？”子曰：“生，事之以礼；死，葬之以礼，祭之以礼。”",
            "孟武伯问孝。子曰：“父母唯其疾之忧。”",
            "子游问孝。子曰：“今之孝者，是谓能养。至于犬马皆能有养；不敬，何以别乎？”",
            "子夏问孝。子曰：“色难。有事，弟子服其劳；有酒食，先生馔，曾是以为孝乎？”",
            "子曰：“吾与回言终日，不违，如愚。退而省其私，亦足以发，回也不愚。”",
            "子曰：“视其所以，观其所由，察其所安，人焉廋哉？人焉廋哉？”",
            "子曰：“温故而知新，可以为师矣。”",
            "子曰：“君子不器。”",
            "子贡问君子。子曰：“先行其言而后从之。”",
            "子曰：“君子周而不比，小人比而不周。”",
            "子曰：“学而不思则罔，思而不学则殆。”",
            "子曰：“攻乎异端，斯害也已！”",
            "子曰：“由，诲汝知之乎！知之为知之，不知为不知，是知也。”",
            "子张学干禄。子曰：“多闻阙疑，慎言其余，则寡尤；多见阙殆，慎行其余，则寡悔。言寡尤，行寡悔，禄在其中矣。”",
            "哀公问曰：“何为则民服？”孔子对曰：“举直错诸枉，则民服；举枉错诸直，则民不服。”",
            "季康子问：“使民敬、忠以劝，如之何？”子曰：“临之以庄，则敬；孝慈，则忠；举善而教不能，则劝。”",
            "或谓孔子曰：“子奚不为政？”子曰：“《书》云：‘孝乎惟孝，友于兄弟，施于有政。’是亦为政，奚其为为政？”",
            "子曰：“人而无信，不知其可也。大车无輗，小车无軏，其何以行之哉？”",
            "子张问：“十世可知也？”子曰：“殷因于夏礼，所损益，可知也；周因于殷礼，所损益，可知也。其或继周者，虽百世，可知也。”",
            "子曰：“非其鬼而祭之，谄也；见义不为，无勇也。”"
        ]
    },



为了复用现有的代码，并支持不同的数据结构（如唐诗、宋词和《论语》），我们可以进行一些抽象和灵活的设计。下面我会提供一个通用的解决方案，帮助你复用现有代码来支持不同的数据结构。


### 1. **创建通用的数据模型**


我们可以定义一个通用的 `Content` 协议或基类，然后通过不同的结构体来表示唐诗、宋词和《论语》等内容。


#### 示例代码：


```swift
import Foundation

// 通用的内容协议，所有的诗、词、论语都遵循这个协议
protocol Content: Decodable {
    var title: String { get }
    var paragraphs: [String] { get }
}

// 唐诗的数据结构
struct Poem: Content {
    var title: String
    var paragraphs: [String]
    var author: String
}

// 《论语》章节的数据结构
struct Chapter: Content {
    var title: String
    var paragraphs: [String]
    var chapter: String  // 对应的章节名
}

// 宋词的数据结构（假设有类似结构）
struct SongCi: Content {
    var title: String
    var paragraphs: [String]
    var author: String
}

// 示例：加载和解码 JSON 数据
class ContentViewModel: ObservableObject {
    @Published var contents: [Content] = []
    
    func loadContent(fromFile fileName: String, ofType type: String, inFolder folderName: String) {
        // 获取 JSON 文件的 URL
        guard let url = Bundle.main.url(forResource: fileName, withExtension: type, subdirectory: folderName) else {
            print("文件未找到")
            return
        }
        
        do {
            // 读取 JSON 文件数据
            let jsonData = try Data(contentsOf: url)
            
            // 使用 JSONDecoder 解码 JSON 数据
            let decoder = JSONDecoder()
            let decodedContent = try decoder.decode([Content].self, from: jsonData)
            self.contents = decodedContent
        } catch {
            print("加载或解析 JSON 文件时出错: \(error)")
        }
    }
}
```


### 2. **灵活支持不同数据结构**


通过定义通用的协议 `Content`，你可以让不同类型的数据（如唐诗、宋词、论语等）实现该协议，从而使得它们具有一致的结构（如 `title` 和 `paragraphs`）。


- `Poem` 结构体表示唐诗，包含标题、段落和作者。
- `Chapter` 结构体表示《论语》中的章节，包含章节标题和段落。
- `SongCi` 结构体表示宋词，结构类似，但也可以包含其他字段。

### 3. **加载不同类型的数据**


你可以通过修改 `ContentViewModel` 中的 `loadContent` 方法来加载不同类型的 JSON 数据。这个方法现在可以加载唐诗、宋词或者《论语》，只需要改变文件名和路径即可。


例如，你可以在调用时指定不同的数据文件：


```swift
let viewModel = ContentViewModel()

// 加载唐诗
viewModel.loadContent(fromFile: "tangshi300", ofType: "json", inFolder: "JsonData")

// 加载《论语》数据
viewModel.loadContent(fromFile: "lunyu", ofType: "json", inFolder: "JsonData")
```


### 4. **视图展示不同类型的内容**


接下来，你可以根据不同的 `Content` 类型来渲染视图。你可以在视图中使用 `ForEach` 来展示内容，并根据内容类型自适应显示。例如，展示章节标题、作者或内容的不同方式。


#### 示例代码：


```swift
import SwiftUI

struct ContentView: View {
    @StateObject var viewModel = ContentViewModel()
    
    var body: some View {
        NavigationView {
            List(viewModel.contents, id: \.title) { content in
                VStack(alignment: .leading) {
                    Text(content.title)
                        .font(.headline)
                    
                    ForEach(content.paragraphs, id: \.self) { paragraph in
                        Text(paragraph)
                            .font(.body)
                            .padding(.bottom, 2)
                    }
                }
            }
            .onAppear {
                viewModel.loadContent(fromFile: "lunyu", ofType: "json", inFolder: "JsonData")
            }
        }
    }
}
```


### 5. **根据不同内容的需求调整视图**


对于不同类型的数据（例如，唐诗需要显示作者，论语需要显示章节），你可以在视图中做额外的处理：


```swift
struct ContentDetailView: View {
    var content: Content
    
    var body: some View {
        VStack {
            Text(content.title)
                .font(.title)
                .padding()
            
            if let poem = content as? Poem {
                Text("作者: \(poem.author)")
                    .font(.subheadline)
                    .padding(.bottom)
            }
            
            ForEach(content.paragraphs, id: \.self) { paragraph in
                Text(paragraph)
                    .font(.body)
                    .padding(.bottom, 5)
            }
        }
    }
}
```


### 总结


通过上述方式，我们创建了一个灵活的结构来处理不同类型的内容数据（如唐诗、宋词、论语等）。使用通用的 `Content` 协议和对应的具体数据结构，可以确保复用性和扩展性。你可以轻松地为每种数据类型创建合适的模型和视图，并根据不同的数据来调整内容展示。


