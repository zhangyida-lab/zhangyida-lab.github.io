---
title: git常用命令指南
date: 2025-05-13 09:54:27
tags:
---



# git常用命令总结




## 🔧 Git 基本配置


```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --list        # 查看当前配置
```
<!--more-->


## 🗂️ 仓库操作


```bash
git init                 # 初始化仓库
git clone           # 克隆远程仓库
```



## 📄 文件操作


```bash
git status               # 查看当前状态
git add            # 添加文件到暂存区
git add .                # 添加所有改动文件
git restore        # 撤销未提交的修改
git restore --staged   # 撤销已暂存的文件
```



## ✅ 提交更改


```bash
git commit -m "message"  # 提交到本地仓库
git commit -am "message" # 跳过 add，直接提交已跟踪文件
```



## ⏪ 版本回退与记录查看


```bash
git log                  # 查看提交日志
git log --oneline        # 简洁日志
git diff                 # 查看尚未提交的更改
git diff --staged        # 查看已暂存的更改
git reset --soft HEAD^   # 回退到上一个版本，保留修改
git reset --hard HEAD^   # 回退到上一个版本，丢弃修改
```



## 🌲 分支操作


```bash
git branch               # 查看本地分支
git branch         # 创建新分支
git checkout       # 切换分支
git checkout -b    # 创建并切换分支
git merge        # 合并分支
git branch -d      # 删除分支
```



## 🌍 远程仓库


```bash
git remote -v            # 查看远程地址
git remote add origin   # 添加远程仓库
git push -u origin master    # 第一次推送，设置跟踪
git push                 # 推送改动
git pull                 # 拉取并合并远程内容
git fetch                # 拉取但不合并
```



## 🧹 清理与忽略


```bash
git clean -fd            # 删除未被跟踪的文件和目录
echo "filename" &gt;&gt; .gitignore  # 忽略文件
```



## 🔍 标签操作


```bash
git tag                  # 查看标签
git tag            # 创建标签
git push origin     # 推送标签
git push origin --tags   # 推送所有标签
```



## 💡 其他技巧


```bash
git stash                # 暂存当前工作区
git stash apply          # 恢复最近 stash
git cherry-pick  # 应用指定提交
```





# xcode的git账户配置和在命令行给mac的git账户配置事如何区分的

**Xcode 中配置的 Git 账户** 和 **命令行中配置的 Git 账户** 之间的区别和关系如下：



## ✅ 1. **命令行配置 (git config) 是全局或项目级别的 Git 配置**


在命令行中配置 Git 用户信息时，使用的是：


```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```


这会修改全局配置文件（位于 `~/.gitconfig`），适用于所有 Git 仓库。


也可以针对某一个项目设置：


```bash
git config user.name "Project Name"
git config user.email "project@email.com"
```


这会在该项目的 `.git/config` 中设置，仅对该仓库生效。



## ✅ 2. **Xcode 中的 Git 账户配置是 GUI 层面用于认证与签名的**


Xcode 的 Git 设置主要包括：


- Source Control → Accounts 中配置的 GitHub / GitLab / Bitbucket 等账号。
- Xcode 使用这些账号进行 **远程仓库的认证** 和拉取推送操作。
- 如果你连接的是 GitHub，Xcode 会用你的 Apple Keychain 中的凭证或 SSH Key 去认证。


💡 Xcode 中并不会影响你命令行中 `git config` 的 user.name 和 user.email，但会影响你在 Xcode 中提交代码时，Git 的认证方式（例如是否能推送到 GitHub）。




## ⚠️ 区别总结表


| 项目 | 命令行配置 (git config) | Xcode 中的账户配置 |
| ---- | ---- | ---- |
| 设置方式 | git config 命令 | Xcode → Settings → Accounts |
| 配置作用 | 提交信息中的 user.name/email | 用于远程仓库的认证 |
| 是否影响彼此 | 否 | 否 |
| 用途 | 控制 Git 提交者信息 | 用于与远程仓库交互（拉取/推送） |
| 储存位置 | ~/.gitconfig 或 .git/config | macOS 钥匙串 / Xcode 配置 |



## ✅ 建议做法


为了避免混淆：


1. **命令行设置提交信息：**

```bash
git config --global user.name "你的真实姓名"
git config --global user.email "你的邮箱"
```
2. **Xcode 设置账户：**

- 添加 GitHub / GitLab 等账号用于认证；
- 确保 SSH Key 正确配置并上传到远程仓库。



# xcode中有个author name和author email这个是怎么回事

 **Xcode 中的 “Author Name” 和 “Author Email”**，它们是用来设置 **提交（commit）时的作者信息**，本质上对应的是 Git 中的以下两项配置：


```bash
git config user.name
git config user.email
```



## 🧭 它们的作用是什么？


在你通过 **Xcode 提交代码（commit）到 Git 仓库时**，Xcode 会使用这个“Author Name”和“Author Email”来标记每次提交的作者信息：


- `Author Name` → 对应 commit 的作者名字（显示在提交记录里）
- `Author Email` → 对应 commit 的作者邮箱（也是 GitHub / GitLab 识别作者身份的关键）

这些信息会写入每一次提交的元数据中，供 Git 日志等使用。



## 🧩 它们和命令行的 Git 配置有什么关系？


它们和命令行中的 `git config` 是**两套独立配置**，但用途相同。


- **命令行设置：**

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```
- **Xcode 设置位置：**
打开一个 Git 仓库项目 →
菜单栏 `Source Control` → `Working Copy` → `Configure...` →
可以看到 `Author Name` 和 `Author Email` 的设置。

💡 每个仓库的 Xcode 设置是 **局部的**，不会覆盖全局 Git 配置。


## ✅ 如果两者不同会发生什么？


如果你在命令行设置的是 A（比如 GitHub 上的用户名），在 Xcode 设置的是 B，那么：


- 用 Xcode 提交的提交记录显示的是 B（Xcode 配置的 Author Name 和 Email）。
- 用终端提交的记录显示的是 A（命令行配置的 user.name 和 user.email）。
- 在 GitHub 的提交历史中，**只有 email 和 GitHub 账户绑定**的提交，才会关联你的头像和链接。


## 🔐 提示：隐藏 Email 的 GitHub 推荐做法


GitHub 支持使用一个“隐私邮箱地址”，你可以这样设置 Xcode 或命令行：


- Email 设置为：`your_github_username@users.noreply.github.com`
- 在 GitHub 里启用 "Keep my email address private"。

这样可以防止泄露真实邮箱。



## ✅ 推荐做法


| 场景 | 推荐设置 |
| ---- | ---- |
| 仅你自己开发 | 设置一致的姓名和邮箱（Xcode 与命令行） |
| 提交要显示 GitHub 身份 | 邮箱设置为你 GitHub 账户的邮箱地址 |
| 需要隐藏真实邮箱 | 设置 GitHub 提供的 noreply 邮箱地址 |






# Git 和 GitHub 的操作不仅限于基本命令，还包含很多提升效率和避免坑的**实用技巧**。
以下是我为你总结的一些进阶技巧和最佳实践，涵盖 Git、GitHub，以及团队协作常见问题。



## 🌟 Git 使用技巧


### 1. **多次提交合并为一次提交（交互式 rebase）**


```bash
git rebase -i HEAD~3
```


- 把最近的 3 次提交合并成一条（如用于代码合并前的整理）。
- 将其中的 `pick` 改成 `squash` 或 `fixup`，合并提交信息。


### 2. **撤销最近的 commit（但保留修改）**


```bash
git reset --soft HEAD~1
```


- 取消提交但不清除修改，便于重新组织 commit。


### 3. **忽略某个已经被 Git 追踪的文件变动**


```bash
git update-index --assume-unchanged 
```


- 对本地配置文件等有用，防止每次 diff 都提醒。


### 4. **创建 .gitignore 后如何移除已被跟踪的文件**


```bash
git rm -r --cached .
git add .
git commit -m "Remove tracked files now in .gitignore"
```


- 清除缓存区的已跟踪文件，不删除本地文件。


### 5. **为当前仓库设置别名（提高效率）**


```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
```


- 然后可以用 `git st`、`git co`、`git br` 简化命令。


## 🔧 GitHub 使用技巧


### 6. **使用 SSH 免密登录 GitHub**


```bash
ssh-keygen -t rsa -C "you@example.com"
pbcopy &lt; ~/.ssh/id_rsa.pub  # 复制到剪贴板
```


- 然后将公钥粘贴到 GitHub 账号的 SSH Keys 中。


### 7. **在 Pull Request (PR) 中自动关闭 Issue**


在 PR 的描述中写上：


```nginx
Fixes #123
```


- 合并该 PR 后，GitHub 会自动关闭对应的 issue。


### 8. **使用 GitHub CLI**


GitHub 官方的命令行工具 `gh`：


```bash
gh pr create
gh repo clone user/repo
gh issue list
```



### 9. **查看谁修改了某行代码（blame）**


```bash
git blame 
```


- 查看每行最后一次是谁提交的，便于追溯问题。


### 10. **Fork 项目后同步上游仓库更新**


```bash
git remote add upstream https://github.com/original-owner/repo.git
git fetch upstream
git merge upstream/main
```


- 让你 fork 的仓库保持更新。


## 🤝 团队协作建议


- 避免直接向 `main/master` 推送，使用分支+PR流程。
- Commit message 推荐使用 **英文，结构统一**（如 `feat: 新功能` / `fix: 修复问题`）。
- 对于多人协作，使用 **rebase 保持提交历史清晰**，但在合并前进行。
