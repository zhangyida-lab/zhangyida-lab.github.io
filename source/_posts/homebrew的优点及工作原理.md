---
title: homebrew的优点及工作原理
date: 2025-04-29 23:54:24
tags: [工具,homebrew]
---




# Homebrew 是 macOS 上最流行的包管理器之一，用于安装、更新、配置和删除各种软件包，尤其适用于开发环境。下面将从 **优点**、**工作原理**、**常用命令与使用技巧** 三个方面详细解释 Homebrew 在 macOS 中的作用。



## 一、Homebrew 的优点


### 1. 简化软件安装


Homebrew 类似于 Linux 下的 `apt` 或 `yum`，提供统一的方式安装工具，例如 Git、Node.js、Python、MySQL 等，避免手动下载安装包、配置路径等繁琐操作。


### 2. 用户目录安装（非侵入式）


默认将软件安装在 `/opt/homebrew`（M1/M2 芯片）或 `/usr/local`（Intel 芯片）目录，不会污染系统目录，安全性高、易于管理。

<!--more-->
### 3. 版本管理


通过 `brew link`、`brew unlink`、`brew switch` 等命令，可轻松在多个版本之间切换，适合有多版本需求的开发者。


### 4. 社区驱动，资源丰富


拥有活跃的社区和庞大的 Formula（配方）库，几乎涵盖了所有主流工具。


### 5. 支持图形界面软件（Cask）


可通过 `brew install --cask` 安装 GUI 应用，比如 Chrome、Visual Studio Code、Notion 等。



## 二、Homebrew 的工作原理


### 1. Formula 配方


Homebrew 使用 Ruby 编写的 **Formula（配方）文件** 描述如何从源码安装软件，包括依赖项、编译参数等。


### 2. 下载源代码


执行 `brew install xxx` 后，Homebrew 会从 GitHub 上下载该软件的 Formula，再下载源代码或二进制包。


### 3. 编译或解压


根据配方指示，Homebrew 会编译源代码（或解压预编译包），安装到 Homebrew 的目录（如 `/opt/homebrew/Cellar/xxx`）。


### 4. 链接到系统路径


安装后通过 `brew link` 创建到 `/opt/homebrew/bin`（或 `/usr/local/bin`）的符号链接，加入 PATH 后即可全局使用。



## 三、Homebrew 使用技巧与常用命令


### ✅ 基本操作


```sh
brew install <包名>      # 安装软件
brew uninstall <包名>    # 卸载软件
brew update               # 更新 Homebrew 本身
brew upgrade              # 升级所有已安装软件
brew cleanup              # 清理旧版本、缓存等
```


### ✅ 搜索与信息


```sh
brew search <关键词>     # 搜索软件
brew info <包名>         # 查看软件信息
```


### ✅ 管理链接


```sh
brew link <包名>         # 将软件链接到 PATH（启用）
brew unlink <包名>       # 取消链接（禁用）
```


### ✅ Cask 管理 GUI 软件


```sh
brew install --cask <软件名>     # 安装 GUI 应用
brew uninstall --cask <软件名>   # 卸载 GUI 应用
```


### ✅ 软件版本切换（需要额外工具）


```sh
brew install pyenv        # 管理多个 Python 版本
brew install nvm          # 管理 Node.js 版本
```


### ✅ 备份已安装列表


```sh
brew list >brew-packages.txt         # 备份软件列表
brew install < brew-packages.txt      # 批量安装
```



## 四、实用技巧建议


1. **保持 Homebrew 更新**

```sh
brew update && brew upgrade
```

每月更新一次，避免版本过旧或依赖冲突。
2. **结合 mas 管理 App Store 软件**

```sh
brew install mas
mas install    # 安装 App Store 应用
```
3. **查看依赖关系**

```sh
brew deps <包名&gt;
brew uses <包名&gt;
```
4. **使用 Homebrew Bundle 进行配置管理**
创建 `Brewfile` 文件，实现自动化部署开发环境：

```sh
brew bundle dump      # 导出当前安装的所有包
brew bundle           # 根据 Brewfile 安装所有包
```
5. **Homebrew 安装目录说明**

- `/opt/homebrew/Cellar/`：所有包的实际安装位置
- `/opt/homebrew/bin/`：可执行文件链接目录
- `/opt/homebrew/etc/`：配置文件路径
- `/opt/homebrew/var/`：数据库/日志/运行数据等



# 题外话
brew显示我的电脑装了三个版本的python，3.10，3.13，3.9，为什么会这样？

你的 Mac 上通过 Homebrew 安装了多个 Python 版本（3.9、3.10、3.13），出现这种情况有几个**常见原因**，以下是详细分析：



## 一、可能的原因


### 1. **手动安装多个版本**


你可能在不同时间手动执行了：


```bash
brew install python@3.9
brew install python@3.10
brew install python@3.13
```


Homebrew 支持多个版本并存，不会强制卸载旧版本。



### 2. **依赖安装引入旧版本**


某些 Homebrew 包依赖特定 Python 版本。例如你安装了一个软件，它依赖 `python@3.9`，Homebrew 会自动安装该版本作为依赖。


可以使用以下命令查看谁依赖旧版本：


```bash
brew uses --installed python@3.9
```



### 3. **默认 python3 指向的是最新版，但旧版本未被清理**


例如：


```bash
ls -l /opt/homebrew/bin/python3
```


可能链接的是 `python3.13`，但 `3.10` 和 `3.9` 还在 Cellar 中没有删除。



## 二、如何检查和管理这些版本


### ✅ 查看所有已安装的 Python 版本：


```bash
brew list | grep python
```


### ✅ 查看版本路径：


```bash
ls -l /opt/homebrew/Cellar | grep python
```


### ✅ 查看哪个版本当前被链接（全局默认）：


```bash
which python3
python3 --version
```


### ✅ 查看 Homebrew 链接状态：


```bash
brew list --versions python@3.9 python@3.10 python@3.13
brew link --overwrite --force python@3.13
```



## 三、如何卸载不需要的版本


如果你确定不再需要旧版本，可以执行：


```bash
brew uninstall python@3.9
brew uninstall python@3.10
```


卸载前建议先检查依赖项（避免卸载后其他包异常）：


```bash
brew uses --installed python@3.9
```



## 四、推荐做法


- 保留你实际使用的一个版本（比如 3.13）
- 使用工具如 `pyenv` 管理多个 Python 版本更方便灵活，尤其在开发中：

```bash
brew install pyenv
pyenv install 3.10.12
pyenv global 3.10.12
```


# 题外话（关于隐藏文件夹显示）
finder中找不到usr/local文件夹

`/usr/local` 在 macOS 中默认是一个**系统隐藏文件夹**，在 Finder 中是不可见的。但你仍然可以通过以下几种方式**访问和显示**它：



## ✅ 方法一：使用 Finder 快捷方式直接进入


1. 打开 Finder
2. 点击顶部菜单栏的 **“前往”**（Go）>**“前往文件夹…”**（Go to Folder…）
3. 输入路径：

```bash
/usr/local
```
4. 回车，即可打开该隐藏文件夹。


## ✅ 方法二：使用终端命令让 /usr/local 显示在 Finder 中


```bash
sudo chflags nohidden /usr/local
```


然后你可以在 Finder 的“Macintosh HD”或“电脑”下直接看到它。



## ✅ 方法三：用 Finder 显示所有隐藏文件（临时切换）


你可以在 Finder 中按下快捷键：


```nginx
Command + Shift + .
```


这个组合键可以**切换隐藏文件显示状态**。再去 `/usr/` 看，会发现 `/usr/local` 显现了出来。再次按下组合键可以隐藏回来。



## ✅ 方法四：通过终端列出它是否存在


在终端运行：


```bash
ls -ld /usr/local
```


如果存在，它会显示权限、所有者等信息。如果没有，可能是 M 系列芯片的 Mac，默认 Homebrew 会安装到 `/opt/homebrew`，不是 `/usr/local`。


你可以确认 Homebrew 的安装位置：


```bash
brew --prefix
```


输出如果是：


- `/opt/homebrew`：说明你是 Apple Silicon（M1/M2/M3）
- `/usr/local`：说明你是 Intel 芯片


# speedtest-cli 是做什么用的

`speedtest-cli` 是一个基于命令行的网络测速工具，用于测试你的网络带宽速度（包括 **下载速度**、**上传速度** 和 **延迟 ping**）。它是由 Ookla 的 Speedtest.net 提供服务支持，与网页上的 [Speedtest.net 测速页面](https://www.speedtest.net/) 是同一个服务接口。



## ✅ 它能做什么？


### 1. **测试当前网络带宽**


运行：


```bash
speedtest-cli
```


你会看到如下结果：


```makefile
Ping: 12.34 ms
Download: 120.45 Mbit/s
Upload: 20.34 Mbit/s
```


### 2. **不打开浏览器测试网络速度**


非常适合远程服务器、SSH 登录场景或 Mac 终端中快速查看网络状态。


### 3. **指定服务器测试（可选）**


你可以指定服务器 ID 来测试某个特定地区的节点：


```bash
speedtest-cli --server 12345
```


### 4. **以 JSON 或 CSV 格式输出，便于脚本处理**


```bash
speedtest-cli --json
speedtest-cli --csv
```



## ✅ 如何安装（macOS）


```bash
brew install speedtest-cli
```


或者使用 pip（如果你有 Python 环境）：


```bash
pip install speedtest-cli
```



## ✅ 实用用法示例


```bash
speedtest-cli                # 默认测试最近的服务器
speedtest-cli --simple       # 只显示 ping、下载、上传
speedtest-cli --share        # 测试后生成分享链接（图片）
speedtest-cli --json         # 以 JSON 输出结果
```



## ✅ 使用场景举例


- 测试远程服务器是否掉速
- 查看 ISP（电信/联通/移动）是否限速
- 写自动化脚本记录网络波动
- 网管/IT 运维用来监控网络健康状态




# 链接到系统路径这个操作的原理
homebrew 链接到系统路径这个操作的原理是什么？
Homebrew 将安装的工具“链接”到系统路径（如 `/usr/local/bin` 或 `/opt/homebrew/bin`）的**本质原理是创建符号链接（symbolic link）**，也就是我们常说的 “软链接”。



## 🧠 本质原理：符号链接（Symbolic Link）


以 Homebrew 安装的某个软件为例（比如 `python@3.13`）：


### Homebrew 实际安装路径：


```bash
/opt/homebrew/Cellar/python@3.13/3.13.0/bin/python3
```


这个路径太深了，也不是系统默认的 PATH。为了让你在终端可以直接运行 `python3`，Homebrew 会：


### ➜ 在 PATH 路径下创建软链接：


```bash
/opt/homebrew/bin/python3 ->/opt/homebrew/Cellar/python@3.13/3.13.0/bin/python3
```


这样，当你执行 `python3` 时，终端会去 PATH 中查找 `/opt/homebrew/bin/python3`，实质上就调用了那个深层目录下的实际二进制文件。



## 🛠️ Homebrew 链接命令详解


- **自动链接（默认安装时）**

```bash
brew install python@3.13
```

通常会自动做链接，除非冲突或已有其他版本。
- **手动链接指定版本**

```bash
brew link python@3.13 --force --overwrite
```

强制将某版本链接到 PATH，有时用于替换已存在的版本。
- **取消链接（但不卸载）**

```bash
brew unlink python@3.10
```


## 🔍 如何查看链接情况


你可以用下面命令检查路径：


```bash
which python3
# 输出: /opt/homebrew/bin/python3

ls -l /opt/homebrew/bin/python3
# 输出: ->../Cellar/python@3.13/3.13.0/bin/python3
```



## 🧭 Intel 与 Apple Silicon 的差异


| 芯片架构 | Homebrew 主目录 | 链接目录（PATH） |
| ---- | ---- | ---- |
| Intel (x86_64) | /usr/local/Cellar/ | /usr/local/bin/ |
| Apple Silicon | /opt/homebrew/Cellar/ | /opt/homebrew/bin/ |


终端执行的命令最终都是通过 `which` 查找这些链接路径。



## ✅ 总结一句话：



**Homebrew 链接软件到系统路径的原理，就是用软链接把深层 Cellar 里的实际程序映射到 PATH 路径中，让你可以全局使用它。**






# 请以intel版本的mac举例

假设你在 **Intel 版本的 Mac** 上使用 Homebrew 安装软件，下面是一个详细的解释，讲解 Homebrew 如何链接安装的软件到系统路径。


### 1. **安装软件时 Homebrew 的工作流程**


假设你运行了以下命令来安装 Python：


```bash
brew install python@3.9
```


#### 安装路径：


Homebrew 在 **Intel 版本的 Mac** 上，默认会将软件安装到 `/usr/local/Cellar/` 目录下。具体路径如下：


```bash
/usr/local/Cellar/python@3.9/3.9.7/bin/python3
```


这是 **Python 3.9** 的实际安装路径，它被安装在了 Homebrew 的 Cellar 目录中。这个路径比较深，且不在常规的 **PATH** 环境变量中，因此你无法通过直接输入 `python3` 来访问它。


### 2. **创建符号链接到 /usr/local/bin/**


为了让你能够在命令行直接执行 `python3`，Homebrew 会在 `/usr/local/bin/` 中创建一个符号链接（soft link），将其指向安装路径。比如，执行：


```bash
ls -l /usr/local/bin/python3
```


你可能会看到类似以下的输出：


```swift
lrwxr-xr-x  1 user  admin  40 Jan  1 12:34 /usr/local/bin/python3 ->/usr/local/Cellar/python@3.9/3.9.7/bin/python3
```


- **符号链接**：`/usr/local/bin/python3` 是指向实际安装路径 `/usr/local/Cellar/python@3.9/3.9.7/bin/python3` 的快捷方式。
- 当你在终端输入 `python3` 时，系统会查找 `/usr/local/bin/python3`，并通过符号链接找到实际的执行文件。

### 3. **为什么需要符号链接？**


- **简化访问**：通过符号链接，`/usr/local/bin` 目录会成为用户 PATH 环境变量的一部分，因此你可以直接在命令行中访问 `/usr/local/bin` 下的所有程序，而不需要记住深层次的 Cellar 路径。
- **版本管理**：如果你安装了多个版本的 Python（例如 `python@3.8` 和 `python@3.9`），Homebrew 会根据你选择的版本（通过 `brew link` 命令）在 `/usr/local/bin/` 目录中创建相应的符号链接，以保证系统调用的是你选择的版本。

### 4. **手动链接和强制链接**


假如你安装了多个版本的 Python，Homebrew 不会自动链接所有版本。你可以手动链接某个版本，或者强制链接（覆盖其他版本）。


```bash
brew link python@3.9 --force --overwrite
```


这会将 **Python 3.9** 链接到 `/usr/local/bin/`，使它成为默认版本。


### 5. **检查已链接的文件**


你可以查看 Homebrew 创建了哪些符号链接：


```bash
ls -l /usr/local/bin | grep python
```


你会看到类似以下内容，说明 Homebrew 已经创建了符号链接：


```bash
python3 ->../Cellar/python@3.9/3.9.7/bin/python3
python3-config ->../Cellar/python@3.9/3.9.7/bin/python3-config
```


### 6. **取消链接**


如果你希望取消某个版本的符号链接（但不卸载它），可以执行：


```bash
brew unlink python@3.9
```


这会删除 `/usr/local/bin/python3` 的符号链接，但实际的安装文件仍然保留在 `/usr/local/Cellar/` 中。


### 总结


- **安装路径**：软件默认安装在 `/usr/local/Cellar/` 中。
- **符号链接**：Homebrew 会在 `/usr/local/bin/` 创建符号链接，方便你在终端中使用这些工具。
- **手动链接**：你可以通过 `brew link` 强制链接某个版本，或者使用 `brew unlink` 来取消链接。


### 提示：


- **检查 PATH**：你可以通过 `echo $PATH` 命令查看当前的环境变量，确保 `/usr/local/bin` 在 PATH 中，以便能够直接使用 Homebrew 链接的命令。

```bash
echo $PATH
```
- **查看符号链接**：通过 `ls -l /usr/local/bin/`，你可以列出 Homebrew 安装的所有符号链接，并查看每个链接指向的实际文件。


是否需要进一步的帮助，例如如何管理多个版本的 Python 或是如何切换版本？

# /usr/local/bin 这个文件路径有什么特点

`/usr/local/bin` 是 macOS 和类 Unix 系统中常见的一个目录，具有以下几个**特点**：



## ✅ **1. 用户本地的可执行文件存放目录**


`/usr/local/bin` 是 **系统目录**，用来存放用户安装的软件和工具的可执行文件。它位于 `/usr` 文件系统内，但通常会由 **用户或管理员**进行管理，而不是系统自带的软件。


在类 Unix 系统中，常见的几个可执行文件路径包括：


- `/bin`：存放系统启动时必须的基本命令。
- `/usr/bin`：存放非系统核心但常用的命令。
- `/usr/local/bin`：存放用户自己安装的软件和工具。

**特点**：


- **本地安装**：这个目录用于安装用户自己的软件和工具，通常是通过 Homebrew、`make` 或手动安装的程序。
- **不是系统默认的安装目录**：相比 `/bin` 和 `/usr/bin`，`/usr/local/bin` 是可由用户随意添加或删除内容的目录。


## ✅ **2. 路径通常包含在 PATH 环境变量中**


当你安装了程序，尤其是通过 **Homebrew** 或者其他包管理工具，它们会把可执行文件放在 `/usr/local/bin` 目录下。为了使得终端能够识别这些程序，你的系统会将 `/usr/local/bin` 加入到 `PATH` 环境变量中。


`PATH` 是一个包含多个目录路径的变量，系统会在这些路径中查找你输入的命令。如果 `/usr/local/bin` 在 `PATH` 中，终端就能识别并执行其中的命令。


### 示例：


```bash
echo $PATH
```


如果 `/usr/local/bin` 在输出的路径中，表示该目录中的程序可以在终端直接调用。



## ✅ **3. 用户自定义和第三方软件安装目录**


`/usr/local/bin` 作为**本地可执行文件目录**，通常是由用户或第三方程序安装的工具和应用的默认位置。例如：


- **Homebrew**：安装的软件包会自动把可执行文件放到 `/usr/local/bin`，例如 `python3`、`git` 等。
- **自定义软件**：如果你手动编译并安装了某个程序，通常会将它的可执行文件放到 `/usr/local/bin`。
- **开发工具**：开发者可能会把自己编译的工具（如通过 `make install`）放到这个目录中，便于管理和使用。


## ✅ **4. 目录权限和管理**


- **所有权**：通常，`/usr/local/bin` 目录的所有权是 `root` 或管理员用户的，但用户可以在其中安装或创建自己的文件。
- **系统更新不影响**：与系统核心目录（如 `/bin` 或 `/usr/bin`）不同，`/usr/local/bin` 不会受到系统更新或升级的影响。它用于存放系统更新或重装过程中不会覆盖的文件。
- **修改权限**：管理员可以通过命令修改这个目录的权限，例如给特定用户读写权限，或添加软链接等。

### 示例：


```bash
sudo chown -R $(whoami) /usr/local/bin
```


这个命令将 `/usr/local/bin` 目录的所有权更改为当前用户。



## ✅ **5. 方便的路径管理**


通过将软件的可执行文件放在 `/usr/local/bin`，你能够确保它们被统一管理。通常情况下，用户安装的软件（如 Homebrew 安装的包）会使用符号链接（soft link）将文件链接到 `/usr/local/bin`。这样，即便实际文件存放在 `/usr/local/Cellar/` 或其他路径，用户仍然可以通过 `/usr/local/bin` 快速访问。



## ✅ **6. 与系统文件的区别**


`/usr/local/bin` 跟系统的 `/bin` 或 `/usr/bin` 有以下区别：


- **系统目录 vs 用户目录**：


`/bin` 和 `/usr/bin` 存放的是系统核心命令和由系统自带的软件，通常由系统管理员控制。


`/usr/local/bin` 存放的是用户自己安装的软件或工具，系统更新不会覆盖该目录。
- **可以更自由地管理**：


用户可以自由地在 `/usr/local/bin` 中安装、删除或修改文件。


例如，如果通过 Homebrew 安装了 `python@3.9`，Homebrew 会将 Python 可执行文件链接到 `/usr/local/bin/python3`。


## ✅ **总结：**


- **存放位置**：`/usr/local/bin` 是一个用户本地的目录，专门用来存放用户或第三方安装的可执行文件。
- **作用**：它通过被包含在 `PATH` 环境变量中，允许用户方便地在终端调用这些命令。
- **管理自由度**：这个目录不会被系统更新覆盖，用户可以自由安装、删除和修改其中的文件。

如果你正在使用 Homebrew，所有安装的工具、软件的可执行文件，都会默认放到 `/usr/local/bin` 中。
