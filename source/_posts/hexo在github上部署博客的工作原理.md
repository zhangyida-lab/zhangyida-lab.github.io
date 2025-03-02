---
title: hexo在github上部署博客的工作原理
date: 2025-03-02 19:14:52
tags:
---


 # hexo在github上部署博客的工作原理 
   
 Hexo 是一个基于 Node.js 的静态博客框架，它可以将 Markdown 文章生成静态网页，并部署到 GitHub Pages。以下是 Hexo 在 GitHub 上部署博客的基本工作原理：

### 1. **Hexo 生成静态网页**

- Hexo 采用 Markdown 作为文章编写格式，并使用主题渲染为 HTML 页面。
- 运行 hexo generate (hexo g) 时，Hexo 会解析 Markdown 文件并根据主题生成静态网页，存放在 public/ 目录下。
### 2. **GitHub Pages 作为托管平台**

- GitHub Pages 提供静态网页托管服务，可以直接将 public/ 目录的内容托管到 GitHub 上，使其通过 <username>.github.io 访问。
### 3. **Hexo 部署机制**

Hexo 使用 `hexo-deployer-git` 插件将 `public/` 目录推送到 GitHub Pages 仓库：

- 运行 hexo deploy (hexo d) 后，Hexo 会：先执行 hexo generate 生成最新的静态网页。切换到 public/ 目录，并初始化 Git 仓库（如果尚未初始化）。将 public/ 目录中的内容提交到 GitHub 指定分支（通常是 gh-pages 或 main）。GitHub Pages 自动更新网站内容，使博客上线。
### 4. **配置 GitHub Pages**

需要在 Hexo 的 `_config.yml` 中配置 GitHub 仓库信息，例如：

```yaml
deploy:
  type: git
  repo: https://github.com/your-username/your-repo.git
  branch: gh-pages  # 或者 main
```

这样在执行 `hexo d` 时，Hexo 就会自动推送 `public/` 目录的内容到指定分支。

### 5. **访问博客**

- 部署完成后，用户可以通过 https://your-username.github.io/ 访问博客。
- 也可以绑定自定义域名，使其解析到 GitHub Pages。
**总结：**
Hexo 通过 `hexo generate` 生成静态网页，并使用 `hexo deploy` 将 `public/` 目录推送到 GitHub Pages 指定分支，GitHub Pages 自动提供访问入口，实现博客托管。
 # 用户问题 
 需要多电脑使用该仓库撰写博客，要给该仓库创建其他分支的原因是什么？ 
 # chatGPT 
 当你需要在多台电脑上撰写和维护 Hexo 博客时，通常会使用 **多个分支** 来区分博客的源码（Hexo 项目文件）和生成的静态网页（`public/` 目录）。这主要是为了更好地管理代码，并适应 GitHub Pages 的部署方式。

### **为什么需要多个分支？**

1. **区分博客源码和生成的静态网页**
- Hexo 的源码（Markdown 文章、主题、配置文件等）需要进行版本控制，便于多台电脑同步编辑。
- public/ 目录是 Hexo 生成的静态网页，GitHub Pages 只需要这一部分，而不需要 Hexo 的源码。
2. **不同分支的职责**
通常会使用两个分支：
- hexoSource：存放 Hexo 的 **源码**（Markdown 文章、主题等），多台电脑都可以克隆该分支进行撰写和管理。
- main：存放 Hexo 生成的 **静态网页**，由 Hexo 部署时自动生成，不需要手动修改。
3. **GitHub Pages 只能托管静态网页**
- GitHub Pages 通常会指定一个分支（如 main）来托管静态网页，而 Hexo 的源码并不是静态网页，所以需要单独一个分支存放 public/ 目录。

 # 我想使用main仓库存放生成的静态网页，新建分支名为hexoSource，我该如何操作 
 
 
 如果你想 **让 main 分支存放 Hexo 生成的静态网页**，而 **新建 hexoSource 分支存放 Hexo 源码**，可以按照以下步骤进行操作：

## **操作步骤**

### **1. 备份当前博客源码**

**（如果仓库已有内容，建议先备份代码，以防误操作导致数据丢失）**

```sh
git clone https://github.com/your-username/your-repo.git
cp -r your-repo your-repo-backup  # 备份代码
```

### **2. 切换 main 分支为存放静态网页**

1. **进入你的 Hexo 目录**：
```sh
cd your-repo
```
2. **新建 hexoSource 分支存放 Hexo 源码**：
```sh
git checkout -b hexoSource
git push origin hexoSource  # 推送新分支到远程
```
3. **切换回 main 分支并清空文件**
```sh
git checkout main
git rm -rf .  # 删除所有文件
echo "Hello GitHub Pages" > index.html  # 创建占位文件，防止仓库为空
git add .
git commit -m "Initialize GitHub Pages branch"
git push origin main --force  # 强制推送（清空原有内容）
```
### **3. 配置 Hexo**

1. **切换到 hexoSource 分支，并安装 Hexo**
```sh
git checkout hexoSource
npm install
```
2. **修改 Hexo 配置 _config.yml**
将 `deploy` 部分改为：
```yaml
deploy:
  type: git
  repo: https://github.com/your-username/your-repo.git
  branch: main
```
**这样 Hexo 生成的静态网页将被推送到 main 分支**。
### **4. 生成并部署博客**

1. **清理旧文件**
```sh
hexo clean
```
2. **生成静态网页**
```sh
hexo generate
```
3. **部署到 GitHub Pages**
```sh
hexo deploy
```
**Hexo 会自动将 public/ 目录的内容推送到 main 分支**，而 `hexoSource` 分支仍然保留 Hexo 的源码。
### **5. 在其他电脑上同步博客**

#### **如果要在另一台电脑上撰写博客**

1. **克隆 hexoSource 分支（Hexo 源码）**
```sh
git clone -b hexoSource https://github.com/your-username/your-repo.git
cd your-repo
npm install
```
2. **写文章**
```sh
hexo new "My New Post"
```
3. **本地预览**
```sh
hexo server
```
4. **提交 Hexo 源码**
```sh
git add .
git commit -m "Update blog post"
git push origin hexoSource
```
5. **部署博客**
```sh
hexo clean && hexo generate && hexo deploy
```
**这样 hexoSource 分支保存源码，main 分支用于 GitHub Pages 托管博客。**
## **总结**

- hexoSource 分支 **存放 Hexo 源码**，用于撰写博客，多台电脑同步该分支即可写作。
- main 分支 **存放静态网页**，Hexo 生成的 public/ 目录会自动部署到该分支，并被 GitHub Pages 托管。
- Hexo _config.yml 需修改 deploy.branch: main，让 Hexo 部署到 main 分支。
这样，你的博客就能在多台设备上协同编辑，并且 GitHub Pages 仍然能正常访问你的博客！ 🚀