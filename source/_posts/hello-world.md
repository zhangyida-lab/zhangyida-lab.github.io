---
title: How to setup your hexo blog
---


## Quick Start
### Install hexo

``` bash
$ npm install -g hexo-cli
```
<!--more-->
### Create your site
``` bash
$ hexo init <folder>
$ cd <folder>
$ npm install
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
$ hexo new page --path about/me "About me"
```
### Set tag and other page
``` bash
$ cd your-hexo-site
$ hexo new page tags
$ hexo new page categories
```
### Create a new post

``` bash
$ hexo new "My New Post"
```
### Generate static files

``` bash
$ hexo generate
```

### Deploy to remote sites

``` bash
$ hexo deploy
```

### Clean cache
``` ruby
$ hexo clean
```
clear db.json and public folder

## Setup your hexo blog envrinment for more than one computer
``` bash
$ git clone https://github.com/zhangyida-lab/zhangyida-lab.github.io.git.github.io.git
# install hexo
$ npm install hexo-cli -g
# install dependent library
$ npm install 
# install deploy tool
$ npm install hexo-deployer-git --save
```
## write blog
before write remmber git pull command,this command will pull the github main branch.
remmber moditify your github deploy token.my hexoSource's token is wrong.

### create  hexo branch on github and set it deafult
### clone hexo move other file except .git
### copy hexo old folder except .deploy_git
