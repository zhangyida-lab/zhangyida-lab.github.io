---
title: HowToDeployGitLabByYourself
date: 2023-07-07 15:11:43
tags: git
---

> “To go fast, go alone. To go far, go together.” - Proverb

## 0.What env  does this gitlab use?

- docker desktop for windows
- wsl
  - Ubuntu
  - docker-desktop
  - docker-desktop-data

## 1.Set gitlab workdir

``` shell
mkdir -p /srv/gitlab
chmod 777 -R /srv
export GITLAB_HOME=/srv/gitlab
```

## 2.Create docker-compose.yml file

<!--more-->

if you deploy gitlab on your computer for test,you can set **"hostname"** and **"external_url"** as "127.0.0.1".

``` yml
version: '3.6'
services:
  web:
    image: 'gitlab/gitlab-ce:latest'
    restart: always
    hostname: 'gitlab.example.com'
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'https://gitlab.example.com'
        # Add any other gitlab.rb configuration here, each on its own line
    ports:
      - '80:80'
      - '443:443'
      - '22:22'
    volumes:
      - '$GITLAB_HOME/config:/etc/gitlab'
      - '$GITLAB_HOME/logs:/var/log/gitlab'
      - '$GITLAB_HOME/data:/var/opt/gitlab'
    shm_size: '256m'
```

## 3.Change docker-compose.yml mod

``` shell
sudo chmod 755  docker-compose.yml
```

## 4.Pull ang deploy gitlab

```shell
docker-compose pull
docker-compose up -d
```

![docker-compose](docker-compose.png)

## 5.Find root password

- find containerID

![findContainerID](findContainerID.png)

```docker
docker exec -it containerID bash
```

![findRootPassword](findRootPassword.png)

use this command to exist docker container

```shell
ctrl + p + q
```

## 6.Login root account and approvel user

on this page ,you can not only approval user but also add user.
![ROOTUI](ROOTUI.png)

## 7.Set gitlab preferences

the origin language is English ,you can set other languages,such as Chinese French and so on.
![englishUI](englishUI.png)

for example ,set the language to Chinese.

![chineseUI.png](chineseUI.png)

At last ,you can also find what you want on this [website] (<https://docs.gitlab.com/>).
