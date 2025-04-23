---
title: git工作原理及常用命令
date: 2025-03-28 08:14:35
tags: [xcode,git]
---



# git的工作原理与常用命令

Git 是一个分布式版本控制系统，用于管理文件的更改和版本，特别适合开发团队进行协作。Git 的工作原理包括多个核心概念和操作，其中最重要的包括：工作区、暂存区和本地仓库。下面是 Git 的基本工作原理及常用命令：


### Git 工作原理


1. **工作区（Working Directory）**：
<!--more-->
- 就是你本地文件系统中的目录，包含了当前项目的所有文件。你在这里可以自由修改文件。
2. **暂存区（Staging Area / Index）**：

- 暂存区是一个临时区域，用于存放你准备提交到版本库的文件。你可以通过 `git add` 命令将文件从工作区移动到暂存区，表示它们准备好被提交。
3. **本地仓库（Local Repository）**：

- 这是 Git 为你在本地维护的一个数据库，保存了项目的历史记录（提交记录）。你通过 `git commit` 将暂存区的更改提交到本地仓库。
4. **远程仓库（Remote Repository）**：

- 是 GitHub、GitLab、Bitbucket 等平台上的仓库，或者是你设置的某个服务器上的仓库，用于存储项目的共享版本。你可以将本地的更改推送到远程仓库，或者从远程仓库拉取最新的更改。

### Git 常用命令


1. **配置 Git**

- `git config --global user.name "Your Name"`：配置全局用户名。
- `git config --global user.email "your_email@example.com"`：配置全局用户邮箱。
- `git config --list`：查看当前 Git 配置。
2. **初始化 Git 仓库**

- `git init`：在当前目录初始化一个新的 Git 仓库。执行后，会生成一个 `.git` 目录，标识这是一个 Git 仓库。
3. **查看状态**

- `git status`：查看当前工作区和暂存区的状态，显示哪些文件被修改、哪些文件已暂存、哪些文件未追踪等。
4. **追踪文件**

- `git add &lt;file&gt;`：将指定文件添加到暂存区，准备提交。
- `git add .`：将当前目录下的所有文件添加到暂存区。
- `git add -A`：将所有更改（包括新增、删除、修改）添加到暂存区。
5. **提交更改**

- `git commit -m "Commit message"`：将暂存区的文件提交到本地仓库，`-m` 后跟提交说明。
6. **查看历史**

- `git log`：查看提交历史，按时间倒序列出所有提交记录。
- `git log --oneline`：简洁地查看提交历史，每个提交以一行显示。
- `git diff`：查看文件在工作区和暂存区之间的差异。
7. **分支操作**

- `git branch`：查看当前项目中的所有分支。
- `git branch &lt;branch-name&gt;`：创建一个新分支。
- `git checkout &lt;branch-name&gt;`：切换到指定分支。
- `git checkout -b &lt;branch-name&gt;`：创建并切换到新分支。
- `git merge &lt;branch-name&gt;`：将指定分支的更改合并到当前分支。
8. **推送与拉取**

- `git push origin &lt;branch-name&gt;`：将本地分支的更改推送到远程仓库。
- `git pull origin &lt;branch-name&gt;`：从远程仓库拉取指定分支的最新更改并合并。
9. **克隆远程仓库**

- `git clone &lt;repository-url&gt;`：将远程仓库克隆到本地，创建一个新的 Git 仓库。
10. **查看远程仓库**

- `git remote -v`：查看远程仓库的 URL。
- `git remote add origin &lt;repository-url&gt;`：为当前仓库添加一个新的远程仓库。
- `git fetch origin`：从远程仓库获取更新，但不合并。
11. **撤销更改**

- `git reset &lt;file&gt;`：撤销某个文件的暂存区更改，使其回到工作区。
- `git reset --hard`：撤销所有更改，包括工作区和暂存区的修改，回到上一次提交状态。
12. **删除文件**

- `git rm &lt;file&gt;`：从工作区和暂存区删除文件，并将删除记录添加到暂存区。
13. **标签操作**

- `git tag &lt;tag-name&gt;`：为当前提交打一个标签。
- `git tag`：查看所有标签。
- `git push origin &lt;tag-name&gt;`：将标签推送到远程仓库。

### Git 分支管理


- **创建新分支**：


`git branch &lt;branch-name&gt;`：创建一个新分支，但不切换过去。


`git checkout -b &lt;branch-name&gt;`：创建并切换到新分支。
- **切换分支**：


`git checkout &lt;branch-name&gt;`：切换到指定分支。
- **合并分支**：


`git merge &lt;branch-name&gt;`：将指定分支的更改合并到当前分支。
- **删除分支**：


`git branch -d &lt;branch-name&gt;`：删除本地分支（已合并的分支）。


`git branch -D &lt;branch-name&gt;`：强制删除本地分支（不管是否已合并）。

### Git 工作流总结


1. **开发新功能**：

- 创建新分支，进行开发，提交到本地仓库。
2. **提交和合并**：

- 使用 `git add` 将更改添加到暂存区，`git commit` 提交到本地仓库，`git push` 推送到远程仓库。
3. **与团队协作**：

- 使用 `git pull` 获取远程仓库的最新更改，与本地修改合并。

Git 的强大之处在于它的分布式特性，每个开发者的本地仓库都包含了完整的历史记录，使得代码的管理和协作更加灵活。

# git add . 和git add -A的区别

`git add .` 和 `git add -A` 都用于将更改添加到 Git 的暂存区（Staging Area），但它们在处理 **删除文件** 方面有区别。


### **区别分析**：


1. **git add .（当前目录下的更改）**：

- 只会添加 **当前目录及其子目录** 中的新增和修改的文件。
- **不会** 追踪被删除的文件（即不会把删除操作放入暂存区）。
2. **git add -A（所有更改，包括删除）**：

- 添加所有修改、新增和删除的文件，无论它们是否在当前目录中。
- 会同时追踪文件的删除操作，即如果你删除了一个文件，`git add -A` 会将这个删除操作也加入到暂存区。

### **示例对比**


假设你的 Git 仓库有以下文件：


```lua
project/
│-- file1.txt
│-- file2.txt
│-- subdir/
│   │-- file3.txt
```


#### **场景 1：你修改了 file1.txt，删除了 file2.txt**


- `git add .`


仅暂存 **修改的 file1.txt**，但不会追踪 `file2.txt` 的删除。
- `git add -A`


**同时** 暂存 **file1.txt 的修改** 和 **file2.txt 的删除**。

#### **场景 2：你在 subdir/ 目录外执行 git add .**


- `git add .` 只会添加当前目录及其子目录中的更改，而不会影响 **其他路径的更改**（例如，如果 `file3.txt` 在 `subdir/` 中被删除，而你在 `project/` 外运行 `git add .`，它不会追踪 `file3.txt` 的删除）。
- `git add -A` 会全局地添加所有更改，无论你在哪个目录执行命令。

### **总结**


| 命令 | 添加修改 | 添加新文件 | 追踪删除 |
| ---- | ---- | ---- | ---- |
| git add . | ✅ | ✅ | ❌（不会追踪删除） |
| git add -A | ✅ | ✅ | ✅（会追踪删除） |


**建议：**


- **如果想确保所有更改（新增、修改、删除）都被暂存，使用 git add -A。**
- **如果只想暂存当前目录下的新增和修改的文件，而不处理删除的文件，使用 git add .。** 

# xcode stageall 与git add 的区别

`git add` 是 Git 命令，用于将文件的更改添加到暂存区（staging area），为提交（commit）做准备。它可以选择性地添加文件或文件夹，以便之后通过 `git commit` 提交到版本库。


而 `xcode stageall` 其实并不是一个标准的 Git 命令，而是与 Xcode 集成的 Git 功能。`xcode stageall` 是 Xcode 中的一个操作，用于将所有更改（包括新增、修改和删除的文件）一次性添加到 Git 暂存区。可以理解为它是一个快捷方式，相当于执行 `git add .` 或 `git add -A` 命令，将当前项目中所有的更改都标记为已暂存状态，准备提交。


### 区别总结：


- **git add**：是一个通用的 Git 命令，可以选择性地添加文件或文件夹的更改。
- **xcode stageall**：是 Xcode 中的一种快捷方式，功能相当于 `git add .`，将所有文件的更改添加到暂存区。

通常，如果你在 Xcode 中工作，`xcode stageall` 是一种便捷的方式来进行版本控制，尤其是当你有很多更改时。



# Xcode中的git设置与原生git的一些区别

## Stash Changes 在xcode中的git 是什么作用

在 Xcode 中，`Stash Changes`（隐藏变更）是 Git 的一个功能，用于**临时保存当前工作目录的修改**，以便你可以切换分支或做其他操作而不会丢失这些修改。


### 通俗理解：


当你正在修改代码，但临时需要切换到另一个分支（比如去修个紧急 bug），但是你又 **不想提交当前的改动**，这时候就可以使用 `Stash Changes`：


- 它会把你当前的改动（包括未提交的代码修改和新增的文件）“藏”起来。
- 然后你就可以切换分支或执行其他 Git 操作。
- 等你忙完回来之后，可以用 `Apply Stash` 或 `Pop Stash` 恢复这些改动，继续你的工作。


### 在 Xcode 中的具体操作：


1. 打开 Xcode 的 Source Control（版本控制）菜单。
2. 选择 `Stash Changes...`
3. 输入备注（可选），点击 Stash。
4. 之后你可以在 `Source Control` → `Stashes` 里查看、恢复、或删除之前保存的变更。


### 对比：


| 操作 | 描述 |
| ---- | ---- |
| Commit | 提交到本地仓库，有记录 |
| Discard Changes | 丢弃修改，无法恢复 |
| Stash Changes | 暂存修改，随时可恢复，不提交 |



如果你习惯命令行，其等价命令是：


```bash
git stash
git stash list
git stash apply  # 恢复但保留 stash
git stash pop    # 恢复并删除 stash
```




## 在xcode的sourcecontrol 设置ignored files 与 使用 .gitignore 文件的区别

虽然两种方式**看起来都能忽略文件**，但它们背后的实现方式和适用场景完全不同。下面我就结合 **Xcode 的 Source Control 设置界面** 和 **.gitignore 文件的使用**，给你做一个详细对比。



## ✅ 一、在 Xcode 的 Source Control 中设置 Ignored Files


### 背后原理：


其实这不是修改 `.gitignore` 文件，而是修改了 Git 的本地排除文件：


```pgsql
.git/info/exclude
```


### 主要特点：


| 项目 | 描述 |
| ---- | ---- |
| 🎯 **作用范围** | **仅在本地生效**，不会影响别人的 Git 行为 |
| 📁 **文件位置** | Git 仓库的 .git/info/exclude（不受版本控制） |
| 🧑‍💻 **适合场景** | 临时性的、本地的、不需要别人也忽略的文件（如调试日志、本地脚本） |
| 👀 **设置方式** | 在 Xcode 的 Source Control &gt; Ignore 里操作，属于 GUI 层面 |
| 🔄 **不会自动同步** | 因为不提交到仓库，团队成员无法感知你忽略了什么 |



## ✅ 二、使用 .gitignore 文件


### 背后原理：


`.gitignore` 是一个普通文件，放在仓库里，由 Git 跟踪。


### 主要特点：


| 项目 | 描述 |
| ---- | ---- |
| 🌍 **作用范围** | **全团队通用**，任何克隆仓库的人都会继承这些规则 |
| 📁 **文件位置** | 通常放在项目根目录，或子目录下 |
| 🧑‍🤝‍🧑 **适合场景** | 项目中应该被所有人忽略的文件，例如 .xcworkspace、Pods/、DerivedData/ 等 |
| 🔧 **设置方式** | 手动创建或编辑 .gitignore 文件 |
| ✅ **可以提交** | 是 Git 版本控制的一部分，可协作、回滚、审查 |



## 🔁 总结对比表：


| 比较点 | .gitignore | Xcode Source Control 的 Ignored Files |
| ---- | ---- | ---- |
| 生效范围 | 全团队共享 | 仅你本地 |
| 文件位置 | 项目内，版本控制 | .git/info/exclude，不版本控制 |
| 推荐用途 | 项目应当统一忽略的内容 | 个人临时、本地特有的忽略文件 |
| 设置方式 | 手动写文件 | 图形化勾选 |
| 是否推荐用于协作项目 | ✅ 推荐 | ❌ 不推荐，仅限个人使用 |



## 🧠 小贴士：


- **你设置的 Source Control Ignored Files，并不会写入 .gitignore**，两者不会互相影响。
- 如果你已经添加到 Git 版本控制了，再 ignore 是无效的，需要先 `git rm --cached`。
