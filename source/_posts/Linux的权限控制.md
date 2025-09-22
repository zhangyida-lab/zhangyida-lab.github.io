---
title: Linux的权限控制
date: 2025-09-22 22:14:16
tags:
---

# linux 文件权限控制

我们来系统讲解 **Linux 文件权限控制**，从基本概念到高级技巧，帮助你彻底理解权限管理。


<!--more-->
# Linux 文件权限控制


在 Linux 中，**权限控制**是保证系统安全、隔离用户和进程的重要机制。



## 1️⃣ 权限对象


每个文件/目录都有三个基本属性：


1. **所有者（User, u）**

- 文件所属的用户（通常是创建者）
2. **用户组（Group, g）**

- 文件所属的组
- 文件可以被同组用户共享权限
3. **其他用户（Others, o）**

- 系统上不属于文件所属组的用户


## 2️⃣ 权限类型


Linux 权限主要有三种：


| 权限 | 符号 | 含义 |
| ---- | ---- | ---- |
| 读 | r | 可以查看文件内容或列出目录 |
| 写 | w | 可以修改文件内容或在目录中创建/删除文件 |
| 执行 | x | 可以执行文件（程序、脚本），或进入目录 |



## 3️⃣ 权限表示方法


### (1) 符号表示法


```bash
-rwxr-xr--

```

- 第一个字符：文件类型


`-` 普通文件


`d` 目录


`l` 符号链接
- 接下来的九个字符：权限


`rwx` → 所有者权限


`r-x` → 组权限


`r--` → 其他用户权限

示例：


```bash
-rw-r--r-- 1 user group 1024 Sep 22 file.txt

```

- 普通文件
- 所有者可读写
- 组可读
- 其他用户可读


### (2) 数字表示法（八进制）


- r=4, w=2, x=1
- 每三位权限相加表示一个对象权限
- 例子：

```text
-rwxr-xr--  →  754

```

- 所有者 rwx → 4+2+1=7
- 组 r-x → 4+0+1=5
- 其他 r-- → 4+0+0=4

**命令设置：**


```bash
chmod 754 file.txt

```


## 4️⃣ 常用命令


### (1) 查看权限


```bash
ls -l

```

### (2) 修改权限


```bash
chmod [选项] 权限 文件

```

- 符号方式：

```bash
chmod u+x file.txt    # 给所有者增加执行权限
chmod g-w file.txt    # 移除组写权限
chmod o+r file.txt    # 给其他用户增加读权限
chmod a+x file.txt    # 所有人增加执行权限

```

- 数字方式：

```bash
chmod 755 file.txt

```


### (3) 修改所有者和用户组


```bash
chown user file.txt    # 修改文件所有者
chown user:group file.txt   # 修改所有者和组
chgrp group file.txt   # 修改文件所属组

```


### (4) 特殊权限


| 权限 | 符号 | 含义 |
| ---- | ---- | ---- |
| SUID | s (owner) | 可执行文件以文件所有者身份运行 |
| SGID | s (group) | 可执行文件以所属组身份运行；目录中新建文件继承目录组 |
| Sticky bit | t | 目录内文件只能被文件所有者或 root 删除 |


**示例：**


```bash
chmod 4755 program   # 设置 SUID
chmod 2755 /shared   # 设置 SGID
chmod 1777 /tmp      # 设置 sticky bit

```


## 5️⃣ 高级权限管理


### (1) ACL（Access Control List）


- 可以给 **单个用户或组** 单独设置权限，而不受文件所属组限制
- 命令：

```bash
setfacl -m u:username:rwx file.txt
getfacl file.txt

```

### (2) 文件掩码 umask


- 控制 **新建文件/目录默认权限**
- 查看：

```bash
umask

```

- 设置：

```bash
umask 022  # 新建文件默认权限 644，新建目录默认权限 755

```


## 6️⃣ 总结


1. Linux 权限模型：**所有者(u)、组(g)、其他(o)** + **读(r)、写(w)、执行(x)**
2. 权限表示：

- 符号法（`rwxr-xr--`）
- 数字法（`754`）
3. 常用命令：

- 查看权限：`ls -l`
- 修改权限：`chmod`
- 修改所有者：`chown` / `chgrp`
4. 特殊权限：SUID / SGID / Sticky Bit
5. 高级管理：

- ACL（单用户或组独立权限）
- umask（新建文件默认权限）
