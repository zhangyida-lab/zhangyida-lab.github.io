---
title: AboutAirport
tags: internet
---

> 吾妻之美我者，私我也；妾之美我者，畏我也；客之美我者，欲有求于我也。——《邹忌讽齐王纳谏》

## 代理模式跨越防火墙的原理

- 使用互联网的软件（例如浏览器、命令行）会把数据发送给你电脑中的代理工具（如clash）；
- 该工具把数据进行【加密】，然后发送给【国外】的某个代理服务器（就是所谓的“机场”）；
- 该代理服务器把数据解密，然后发送给你要访问的网站。
- 从该网站回传的数据，也是经过上述途径，最终回到你使用的互联网软件。

## 什么是机场？

> 机场就是代理服务器,也就是“替身”，一些你不能直接做的事情，他都给你干了，而且你和替身之间的沟通方式使用的是加密语言即“行业黑话”

## 什么是代理软件？

> 代理软件通过各种加密协议，将你的网络数据加密，传递给“机场”，机场解密你的网络请求，进行互联网访问，然后将访问结果再加密传输给代理软件，代理解密数据，交还给客户端。

### 常代理软件

- 苹果移动设备
  - Shad­owrocket 俗称 小火箭
- 苹果笔记本
  - clashx
- 安卓
  - clash
- Windows电脑
  - clashForWindows
- Linux系统
  - clash
  