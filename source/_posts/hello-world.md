---
title: How to setup your hexo blog
---


## Quick Start
### Install hexo

``` bash
$ npm install -g hexo-cli
```
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
``` bash
$ hexo clean
```
clear db.json and public folder

## Setup your hexo blog envrinment for more than one computer
