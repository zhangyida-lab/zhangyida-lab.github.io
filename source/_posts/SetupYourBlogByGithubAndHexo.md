---
title: How to setup your hexo blog
tags: Blog
categories: Hexo
---

## How to setup your hexo blog

> Hexo can help us to write blog by markdown,and then we can deploy my blog on github or other srevice.

![blog](blog.jpg)

### [What is github page?](https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages)

>GitHub Pages is a static site hosting service that takes HTML, CSS, and JavaScript files straight from a repository on GitHub, optionally runs the files through a build process, and publishes a website. you can set up what branch will the github page use,so that we can use deploy different branch,one branch to deploy our site,the other to write blog.

### [What is hexo?](https://hexo.io/docs/)

>Hexo is a fast, simple and powerful blog framework. You write posts in Markdown (or other markup languages) and Hexo generates static files with a beautiful theme in seconds.
and when you use "hexo deploy"command the generated file will be git push to git repository branch which you have set.

### How to install hexo?

``` bash
npm install -g hexo-cli
```
<!--more-->
### How to create your site?

``` bash
hexo init <folder>
cd <folder>
npm install
```

### What is floder structure in hexo?

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

### How to set about page?

``` bash
hexo new page --path about/me "About me"
```

the above command will create a me.md,so when you click the about tag,the webpage will 404 ,you should rename the file name to index.md.

### How to set tag and other page?

``` bash
cd your-hexo-site
hexo new page tags
hexo new page categories
```

### Create a new post?

``` bash
hexo new "My New Post"
```

### How to set bolg asset floder?

``` bash
post_asset_folder: false
```

### How to generate static files?

``` bash
hexo generate
```

### How to deploy to remote sites?

``` bash
hexo deploy
```

### Clean cache

``` bash
hexo clean
```

clear db.json and public folder

## How to setup your hexo blog envrinment for more than one computer?

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

## How to write blog on hexo?

Before write remmber git pull command,this command will pull the github main branch.
Remmber your new computer do not have your old blog markdown file.
When you just write blog in your new computer,you do not need "hexo deploy",you just need write and push your "hexo g" file.
Remmber moditify your github deploy token and my hexoSource's token is wrong.

``` bash
hexo new [layout] <title>
```

There are three default layouts in Hexo: post, page and draft you can use.


## Hexo offical document is [here](https://hexo.io/docs/commands.html)

### \# This command is used to cerate draft\post and so on 

``` bash
hexo new [layout] <title>
```

### \# This command is used publish your draft

``` bash
hexo publish [layout] <filename>
```

### \# This command is used to generate your html page

``` bash
hexo generate
```

### \# This command is used to preview your blog site

``` bash
hexo server
```

### \# This command is used to list your post\tage\category and so on 

``` bash
hexo list <type>
```

### \# This command is used to delpoy to your site to github page.

- 当执行 hexo deploy 时，Hexo 会将 public 目录中的文件和目录推送至 _config.yml 中指定的远端仓库和分支中，并且完全覆盖该分支下的已有内容

``` bash
hexo clean && hexo deploy
```



### \# How to display image on your hexo blog.

- set the post_asset_folder setting in _config.yml to true
- put the image file in your folder which name is the same as your blog
- writing blog use the below synta.
  
```
![library](wheat.jpg)
```