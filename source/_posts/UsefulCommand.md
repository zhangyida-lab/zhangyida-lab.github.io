---
layout: blog
title: UsefulCommand
date: 2023-04-17 19:08:48
tags: usefulcommand
categories: command
---



![usefulCommand](usefulCommand.jpg)

(source:  [www.pexels.com](https://www.pexels.com/photo/crop-cyber-spy-hacking-system-while-typing-on-laptop-5935794/))

> 仰观宇宙之大，俯察品类之盛。所以游目骋怀，足以极视听之娱。——《兰亭集序》

*In this blog, i will list many useful command about homebrew/github/npm/linux/yt-dlp and son.*

## \# hexo shortcut

``` bash

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

## \# Is suported netflix

```jsx
bash <(curl -L -s https://raw.githubusercontent.com/lmc999/RegionRestrictionCheck/main/check.sh)
```

## \# About homebrew

>update brew by proxy

homebrew start up mysql:

```bash
brew services start mysql
```

查询mysql安装路径:

```bash
brew list mysql
```

使用代理更新软件库:

```bash
ALL_PROXY=socks5://192.168.0.107:7890 brew upgrade
```
<!--more-->
## \# About github

git set proxy:

git设完代理，shell也需要设置proxy

```bash
//设置全局代理
//http
git config --global https.proxy http://192.168.0.107:7890
//https
git config --global https.proxy https://192.168.0.107:7890

git config --global http.proxy socks5://192.168.0.107:7890
git config --global https.proxy socks5://192.168.0.107:7890

//只对github.com使用代理，其他仓库不走代理
git config --global http.https://github.com.proxy socks5://127.0.0.1:7890
git config --global https.https://github.com.proxy socks5://127.0.0.1:7890
//取消github代理
git config --global --unset http.https://github.com.proxy
git config --global --unset https.https://github.com.proxy

//取消全局代理
git config --global --unset http.proxy
git config --global --unset https.proxy
#查看git代理设置
git config --global --list
```

将本地git仓库添加到远程仓库

```python
git remote add origin https：//.........
```

## \# About lunix

- ssh登录

```bash
ssh root@8.131.231.XX
#设置ssh长时间连接服务器不断开
vi /zhangyi/.ssh/config
#修改文件加入下面一句
ServerAliveInterval 60
#保存退出，重新开启root用户的shell，则再ssh远程服务器的时候，不会因为长时间操作断开。
#登录阿里云服务器
ssh acspace
# 设置别名登陆服务器
cd ~/.ssh
vi config
#config文件
Host            acspace
HostName        8.131.231.122
Port            22
User            root
IdentityFile    ~/.ssh/wangchen.pem
```

- 设置lunix服务器密钥对登录

```bash
#记得绑定密钥对
#修改权限
chmod 400 ~/.ssh/ecs.pem
#修改配置
# 输入ECS实例的别名，用户SSH远程连接。
Host ecs
# 输入ECS实例的公网IP地址。
HostName 121.196.**.**
# 输入端口号，默认为22。
Port 22
# 输入登录账号。
User root
# 输入.pem私钥文件在本机的地址。
IdentityFile ~/.ssh/ecs.pem
```

- centos update

```bash
#检查更新
sudo yum check-update
#安装单个更新
sudo yum install curl
#更新所有包
sudo yum update
#锁定软件版本
sudo yum install yum-plugin-versionlock
sudo yum versionlock php-*
#查询跟新日志
sudo tail /var/log/yum.log
```

- Abot firewall

```bash
#开启防火墙
sudo ufw enable
#重启防火墙
sudo ufw reload
#允许端口
ufw allow 9000
#查看防火墙状态
ufw status
```

- Remote copy

```python
scp -r /Users/zhangyi/Documents/clash root@8.131.231.122:/root
```

```bash
scp -r /mnt/c/Users/Administrator/desktop/acspaceQuizSamplePictures/ root@8.131.231.122:/root/quizResource
```

- My zsh  shortcut

```bash
alias goproxy='export http_proxy=http://127.0.0.1:7890 https_proxy=http://127.0.0.1:7890'
alias disproxy='unset http_proxy https_proxy'
```

## \# Set npm registry

```jsx
npm config set registry http://registry.npmjs.org/
npm config set proxy http://127.0.0.1:7890
npm config set https-proxy http://127.0.0.1:7890

```

## \# Make terminal active for long time when ssh connect to ali

> we do it on client！

```python

/root/.ssh/

vi /root/.ssh/config

# setting this filed
ServerAliveInterval 60

```

## \# About python

```python
python3 -m venv scrapy-env
#创建python虚拟环境scrapy-env
#激活虚拟环境
source scrapy-env/bin/activate
#取消激活python虚拟环境
deactivate
#列出python安装包
pip3 list
#列出虚拟环境python安装包
pip3 freeze
```

## \# About ios

```python
cat << EOF > Podfile &&
platform :ios, '8.0'
use_frameworks!
target 'myYoutubeApi' do
    pod 'GoogleAPIClientForREST/YouTube', '~> 1.2.1'
    pod 'Google/SignIn', '~> 3.0.3'
end
EOF
pod install &&
open myYoutubeApi.xcworkspace
```

## \# About yt-dlp command

- Only downlad mp3

```jsx
yt-dlp -f 'ba' -x --audio-format mp3 https://www.youtube.com/watch?v=E338aF6QHu8 -o "%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s"
```

- Set download folder

```jsx
-o "%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s"
```

- Example_download a list of mp3 on spacial folder

```jsx
yt-dlp -f 'ba' -x --audio-format mp3 https://www.youtube.com/playlist?list=PLzCxunOM5WFJ7sbHi_9Zwq2xOwtkYeZlx  -o "%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s"
```

- Download MP4 videos by a playlist

```powershell
yt-dlp -f "bv" yt-dlp -f 'ba' -x --audio-format mp3 https://www.youtube.com/watch?v=n2oTA5JSk80 -o  "%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s" -o "%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s"
```

- Download single MP4 video

```powershell
yt-dlp -f "bestvideo[ext=mp4]" https://www.youtube.com/watch?v=uY9hVl_69BU -o "%(title)s.%(ext)s"
```

- Download chinese cc

```jsx
yt-dlp --write-subs --sub-format vtt --sub-langs zh-CN --skip-download https://www.youtube.com/playlist?list=PLrxlAuU-npiX9JeW4yO1Wj_r_BgyGYHBu -o "%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s"

```

- list cc

```jsx
yt-dlp --list-subs  https://www.youtube.com/playlist?list=PL8dPuuaLjXtNlUrzyH5r6jN9ulIgZBpdo
```

- Download mp4

```jsx

yt-dlp -f 'bv[ext=mp4]+ba[ext=m4a]' https://www.youtube.com/watch?v=d4lfTXXzQ-o&list=PLDtcD-trW1QSREXci1R_yg-Pmv0QV8XU0&index=26 -o "%(title)s.%(ext)s"
```

- Download best video

```jsx
yt-dlp  https://www.youtube.com/watch?v=d4lfTXXzQ-o -o "%(title)s.%(ext)s"
```

- Download auto-sub only
  
```shell
yt-dlp --write-auto-sub --skip-download  https://m.youtube.com/watch?v=EN0mUrbiJP4
```

- Download auto-sub only and convert to str
  
```shell
yt-dlp --write-auto-sub --skip-download --convert-sub=srt https://m.youtube.com/watch?v=EN0mUrbiJP4
```

- Download all-sub only
  
```shell
yt-dlp --all-subs --skip-download https://m.youtube.com/watch?v=EN0mUrbiJP4
```



## \# video clipper

### 1. Finalcut souperted

- 3GP
- MOV (QuickTime)
- MP4
- MTS/M2TS
- MXF

### 2.Convert video fommate

- mkv to mp4

```jsx
for i in *.mpg; do ffmpeg -i "$i" "${i%.*}.mp4"; done
```

```bash
for f in *.mpg; do ffmpeg -i "$f" -c copy "${f%.mpg}.mp4"; done
```

```powershell
Get-ChildItem -Filter '*.mkv' | % { &ffmpeg -i .\$($_.BaseName).mkv -c copy .\$($_.BaseName).mp4 }
```

- avi to mp4

```powershell
Get-ChildItem -Filter '*.avi' | % { &ffmpeg -i .\$($_.BaseName).avi -c copy .\$($_.BaseName).mp4 }
```

- ts to mp4

```powershell
Get-ChildItem -Filter '*.ts' | % { &ffmpeg -i .\$($_.BaseName).ts -c copy .\$($_.BaseName).mp4 }
```

- mkv to mp4 映射不同轨道流(音频、视频、字幕）

```powershell
Get-ChildItem -Filter '*.mkv' | % { &ffmpeg -i .\$($_.BaseName).mkv -map 0:0 -map 0:2 -c copy .\$($_.BaseName).mp4 }

```

```powershell
Get-ChildItem -Filter '*.mkv' | % { &ffmpeg -i .\$($_.BaseName).mkv -map 0:0 -map 0:2 -map 0:4 -c copy .\$($_.BaseName).mp4 }
```

- webm to MP4

```powershell
Get-ChildItem -Filter '*.webm' | % { &ffmpeg -i .\$($_.BaseName).webm -c copy .\$($_.BaseName).mp4 }
```
