---
layout: blog
title: UsefulCommand
date: 2023-04-17 19:08:48
tags: usefulcommand
categories: ceshi
---
# Useful Commands

## 测试是否支持netflix脚本

```jsx
$ bash <(curl -L -s https://raw.githubusercontent.com/lmc999/RegionRestrictionCheck/main/check.sh)
```

# 🍺 homebrew/

<aside>
💡 使用深港2.5倍有效快速更新brew！！！！！

</aside>

homebrew启动mysql以及redis等后台服务的命令: 

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

# 🧑🏻‍💻 **github**

git代理设置:

<aside>
💡 git设完代理，shell也需要设置proxy

</aside>

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

## 🖥️ lunix

ssh登录

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

设置lunix服务器密钥对登录

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

centos更新

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

关于防火墙

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

远程复制文件_将本地clash文件夹复制到服务器root

```python
scp -r /Users/zhangyi/Documents/clash root@8.131.231.122:/root
```

```bash
scp -r /mnt/c/Users/Administrator/desktop/acspaceQuizSamplePictures/ root@8.131.231.122:/root/quizResource
```

My zsh  shortcut

```bash
alias goproxy='export http_proxy=http://127.0.0.1:7890 https_proxy=http://127.0.0.1:7890'
alias disproxy='unset http_proxy https_proxy'
```

翻牌子记忆卡

```jsx
#使用备份数据启动记忆卡docker容器
docker run -d -p 8000:8000 --name cs-flash-cards -v /Users/zhangyi/Documents/GitHub/computer-science-flash-cards/flashCardDB:/src/db cs-flash-cards
```

npm

```jsx
npm config set registry http://registry.npmjs.org/
npm config set proxy http://127.0.0.1:7890
npm config set https-proxy http://127.0.0.1:7890

```

## 设置ssh远程阿里云服务器时，短时间不操作直接卡死不能输入的问题

<aside>
💡 注意修改的是客户端的！！！！！

</aside>

```python
找到所在用户的.ssh目录,如root用户该目录在：
/root/.ssh/
在该目录创建config文件
vi /root/.ssh/config
加入下面一句：
ServerAliveInterval 60
保存退出，重新开启root用户的shell，则再ssh远程服务器的时候，
不会因为长时间操作断开。应该是加入这句之后，ssh客户端会每隔一
段时间自动与ssh服务器通信一次，所以长时间操作不会断开。
```

## python

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

## ios

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

## 通过docker在阿里云上部署clash

<aside>
💡 注意，在配置UI的时候一定要注意在config文件里进行配置

</aside>

```python
docker run -d --name clash -p 7890:7890 -p 7891:7891 -p 9090:9090 -v /root/clash/config.yaml:/root/.config/clash/config.yaml -v /root/clash/ui:/ui dreamacro/clash
```

## 另一种办法，使用docker compose


# yt-dlp 命令

Created: February 24, 2022
Created by: yi zhang
Tags: CC

## 1.设置只下载mp3格式

```jsx
yt-dlp -f 'ba' -x --audio-format mp3 https://www.youtube.com/watch?v=E338aF6QHu8 -o "%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s"
```

## 2.设置当前目录

```jsx
-o "%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s"
```

## 3.示例_下载youtube播放列表到当前文件路径，存储为mp3

```jsx
yt-dlp -f 'ba' -x --audio-format mp3 https://www.youtube.com/playlist?list=PLzCxunOM5WFJ7sbHi_9Zwq2xOwtkYeZlx  -o "%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s"
```

## 4.下载播放列表MP4视频

```powershell
yt-dlp -f "bv" yt-dlp -f 'ba' -x --audio-format mp3 https://www.youtube.com/watch?v=n2oTA5JSk80 -o  "%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s" -o "%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s"
```

## 4.下载单个MP4视频

```powershell
yt-dlp -f "bestvideo[ext=mp4]" https://www.youtube.com/watch?v=uY9hVl_69BU -o "%(title)s.%(ext)s"
```

## 5.下载汉语字幕

```jsx
yt-dlp --write-subs --sub-format vtt --sub-langs zh-CN --skip-download https://www.youtube.com/playlist?list=PLrxlAuU-npiX9JeW4yO1Wj_r_BgyGYHBu -o "%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s"

```

## 6.列出字幕列表

```jsx
yt-dlp --list-subs  https://www.youtube.com/playlist?list=PL8dPuuaLjXtNlUrzyH5r6jN9ulIgZBpdo
```

## 7.b站

```jsx
yt-dlp https://www.bilibili.com/bangumi/play/ep320672?theme=movie&spm_id_from=333.337.0.0
```

## 8.强制下载mp4

```jsx

yt-dlp -f 'bv[ext=mp4]+ba[ext=m4a]' https://www.youtube.com/watch?v=d4lfTXXzQ-o&list=PLDtcD-trW1QSREXci1R_yg-Pmv0QV8XU0&index=26 -o "%(title)s.%(ext)s"
```

## 下载质量最好的视频

```jsx
yt-dlp  https://www.youtube.com/watch?v=d4lfTXXzQ-o -o "%(title)s.%(ext)s"
```

[](https://www.notion.so/e1ff8fadd7f0453e87c13825e9739460)




# CC 视频剪辑基本知识

Created: February 16, 2022
Created by: yi zhang
Tags: CC

## 1. finalcut 支持的基本视频格式

- 3GP
- MOV (QuickTime)
- MP4
- MTS/M2TS
- MXF

## 2.转换视频格式

mkv to mp4

```jsx
for i in *.mpg; do ffmpeg -i "$i" "${i%.*}.mp4"; done
```

```bash
for f in *.mpg; do ffmpeg -i "$f" -c copy "${f%.mpg}.mp4"; done
```

```powershell
Get-ChildItem -Filter '*.mkv' | % { &ffmpeg -i .\$($_.BaseName).mkv -c copy .\$($_.BaseName).mp4 }
```

avi to mp4

```powershell
Get-ChildItem -Filter '*.avi' | % { &ffmpeg -i .\$($_.BaseName).avi -c copy .\$($_.BaseName).mp4 }
```

ts to mp4

```powershell
Get-ChildItem -Filter '*.ts' | % { &ffmpeg -i .\$($_.BaseName).ts -c copy .\$($_.BaseName).mp4 }
```

mkv to mp4 映射不同轨道流(音频、视频、字幕）

```powershell
Get-ChildItem -Filter '*.mkv' | % { &ffmpeg -i .\$($_.BaseName).mkv -map 0:0 -map 0:2 -c copy .\$($_.BaseName).mp4 }

```

```powershell
Get-ChildItem -Filter '*.mkv' | % { &ffmpeg -i .\$($_.BaseName).mkv -map 0:0 -map 0:2 -map 0:4 -c copy .\$($_.BaseName).mp4 }
```

webm to MP4

```powershell
Get-ChildItem -Filter '*.webm' | % { &ffmpeg -i .\$($_.BaseName).webm -c copy .\$($_.BaseName).mp4 }
```
## hexo command
```bash
# add local search
npm install hexo-generator-search --save
```