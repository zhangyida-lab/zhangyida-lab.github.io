---
title: How to setup your hexo blog
---

## [What is github page?](https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages)
>GitHub Pages is a static site hosting service that takes HTML, CSS, and JavaScript files straight from a repository on GitHub, optionally runs the files through a build process, and publishes a website. you can set up what branch will the github page use,so that we can use deploy different branch,one branch to deploy our site,the other to write blog.


## [What is hexo?](https://hexo.io/docs/)
>Hexo is a fast, simple and powerful blog framework. You write posts in Markdown (or other markup languages) and Hexo generates static files with a beautiful theme in seconds.
and when you use "hexo deploy"command the generated file will be git push to git repository branch which you have set.

## Quick Start
### Install hexo

``` bash
npm install -g hexo-cli
```
<!--more-->
### Create your site
``` bash
hexo init <folder>
cd <folder>
npm install
```
### Floder structure
``` bash
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

### Set about page

``` bash
hexo new page --path about/me "About me"
```
the above command will create a me.md,so when you click the about tag,the webpage will 404 ,you should rename the file name to index.md. 
### Set tag and other page
``` bash
cd your-hexo-site
hexo new page tags
hexo new page categories
```
### Create a new post

``` bash
$ hexo new "My New Post"
```
### Set bolg asset floder


``` bash
post_asset_folder: false
```

### Generate static files

``` bash
hexo generate
```

### Deploy to remote sites

``` bash
hexo deploy
```

### Clean cache
``` ruby
hexo clean
```
clear db.json and public folder

## Setup your hexo blog envrinment for more than one computer

1.create  hexoSource branch on github and set it deafult
2.clone the github repository last step you created,and move other file except .git
3.copy hexo-blog project to your github repository except .deploy_git
4.delete your .git floder in your next theme floder
5.push your repository to github 
6.use below conmmand to deploy your hexo-blog enviroment

``` bash
git clone https://github.com/zhangyida-lab/zhangyida-lab.github.io.git.github.io.git
# install hexo
npm install hexo-cli -g
# install dependent library
npm install 
# install deploy tool
npm install hexo-deployer-git --save
```
## write blog
Before write remmber git pull command,this command will pull the github main branch.
Remmber your new computer do not have your old blog markdown file.
When you just write blog in your new computer,you do not need "hexo deploy",you just need write and push your "hexo g" file.
Remmber moditify your github deploy token and my hexoSource's token is wrong.
``` bash
hexo new [layout] <title>
```
There are three default layouts in Hexo: post, page and draft you can use.


