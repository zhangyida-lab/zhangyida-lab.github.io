# 仓库说明

## 设置原理

1.create hexoSource branch on github and set it deafult
2.clone the github repository last step you created,and move other file except .git
3.copy hexo-blog project to your github repository except .deploy_git
4.delete your .git floder in your next theme floder
5.push your repository to github
6.use below conmmand to deploy your hexo-blog enviroment

```bash
git clone https://github.com/zhangyida-lab/zhangyida-lab.github.io.git
# install hexo
npm install hexo-cli -g
# install dependent library
npm install 
# install deploy tool
npm install hexo-deployer-git --save

```

### 1️⃣ 克隆仓库


```bash
git clone https://github.com/zhangyida-lab/zhangyida-lab.github.io.git
cd zhangyida-lab.github.io

```


### 2️⃣ 安装 Hexo CLI（全局）


```bash
npm install -g hexo-cli

```


⚠️ 这样你才能在命令行中使用 `hexo` 命令。




### 3️⃣ 安装项目依赖


```bash
npm install

```


会根据 `package.json` 安装所有主题和插件依赖。




### 4️⃣ 安装部署插件（如果你用 GitHub Pages 部署）


```bash
npm install hexo-deployer-git --save

```


这个插件会在 Hexo 里用 `hexo deploy` 把生成的静态文件推送到 GitHub 仓库。




### 5️⃣ 生成静态文件 & 本地预览


```bash
hexo clean       # 清理缓存
hexo generate    # 生成静态文件
hexo server      # 本地预览，访问 http://localhost:4000

```


### 6️⃣ 部署到 GitHub（可选）


如果 `_config.yml` 配置了 deploy：


```bash
hexo deploy

```







## 操作说明

```bash
alias ht='hexo clean && hexo g && hexo s'
测试部署hexoBlog快捷键

alias hd='hexo clean && hexo deploy'
部署到github快捷键

alias hnp='hexo new post'
新建博客

alias hnd='hexo new draft'
新建草稿

alias hp='hexo publish'
发布草稿

alias gp='git add .&& git commit -m "update" && git push -f'

```

## 添加多个tag的方法

'''
---
title: swift中async/await工作原理
date: 2025-03-22 22:30:17
tags: [swift编程,异步编程]
---
'''