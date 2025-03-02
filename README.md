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
```
