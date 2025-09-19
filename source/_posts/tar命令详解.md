---
title: tar命令详解
date: 2025-09-19 09:19:49
tags: [运维]
---


# `tar` 命令主要用来 **打包和解压文件归档**，在 Linux/Unix 系统里非常常见。它最早是 *tape archive*（磁带归档）的缩写，用来把很多文件和目录打包成一个文件，方便备份和传输。


<!--more-->
## 基本语法


```bash
tar [选项] [归档文件名] [要处理的文件或目录]

```


## 常用选项


### 创建归档


```bash
tar -cvf archive.tar file1 file2 dir/

```

- `c` → create，创建归档
- `v` → verbose，显示过程
- `f` → file，指定归档文件名

结果：生成一个 `archive.tar` 包含 `file1 file2 dir/`



### 解包归档


```bash
tar -xvf archive.tar

```

- `x` → extract，解包

结果：把 `archive.tar` 解开到当前目录



### 查看归档内容


```bash
tar -tvf archive.tar

```

- `t` → list，列出归档内容


### 带压缩（常见组合）


1. **gzip 压缩**

```bash
tar -czvf archive.tar.gz file1 dir/
tar -xzvf archive.tar.gz

```

- `z` → 使用 gzip
2. **bzip2 压缩**

```bash
tar -cjvf archive.tar.bz2 file1 dir/
tar -xjvf archive.tar.bz2

```

- `j` → 使用 bzip2
3. **xz 压缩**

```bash
tar -cJvf archive.tar.xz file1 dir/
tar -xJvf archive.tar.xz

```

- `J` → 使用 xz


## 常见用法总结


- 打包目录：

```bash
tar -czvf backup.tar.gz /home/user/

```
- 解压到指定目录：

```bash
tar -xzvf backup.tar.gz -C /opt/

```
- 只解压某个文件：

```bash
tar -xzvf backup.tar.gz path/in/archive/file.txt

```


## 命令解释
```bash
tar -czvf backup.tar.gz /home/user/
```

这条命令的意思是：把 `/home/user/` 目录整体打包，并用 **gzip** 压缩成一个名为 `backup.tar.gz` 的归档文件。


逐个参数解释：


- `tar` → 打包工具
- `c` (**create**) → 创建新归档文件
- `z` (**gzip**) → 通过 gzip 压缩归档
- `v` (**verbose**) → 显示详细过程（列出正在打包的文件）
- `f` (**file**) → 指定归档文件名，紧跟其后 `backup.tar.gz`


📌 执行后效果：


- 会在当前目录下生成一个 **backup.tar.gz** 文件
- 里面包含 `/home/user/` 目录及其所有子目录、文件
- 压缩比一般能减少体积


📝 补充：
如果你想让归档文件里不要带 `/home/user/` 的完整路径，而是只保存 `user` 文件夹本身，可以这样：


```bash
tar -czvf backup.tar.gz -C /home user

```

这样 `backup.tar.gz` 里面的内容顶层就是 `user/`。


要不要我给你画一个「执行前后目录结构对比图」，让效果更直观？
# 用户问题
tar -xzvf backup.tar.gz path/in/archive/file.txt
# 回答
你写的这条命令：


```bash
tar -xzvf backup.tar.gz path/in/archive/file.txt

```

👉 作用是：**从压缩包 backup.tar.gz 中，只解压 path/in/archive/file.txt 这个文件**（保持原来的目录结构）。



### 参数拆解：


- `x` → extract，解压
- `z` → gzip，解压 `.gz` 格式
- `v` → verbose，显示解压过程
- `f` → 指定归档文件名（这里是 `backup.tar.gz`）
- `path/in/archive/file.txt` → 指定要提取的文件路径（必须和归档内部的路径一致）


### 使用场景


- 如果只需要从一个大压缩包里取出某个文件，而不想解压全部时就用这个方法。


# 用户问题
解压全部
# 回答
如果要把整个 `backup.tar.gz` 解压出来，可以直接用：


```bash
tar -xzvf backup.tar.gz

```


### 参数说明


- `x` → extract，解压
- `z` → gzip 格式
- `v` → 显示过程（可以省略，不想看过程就去掉）
- `f` → 指定归档文件


### 常见变体


1. **解压到当前目录**（默认行为）

```bash
tar -xzvf backup.tar.gz

```
2. **解压到指定目录**

```bash
tar -xzvf backup.tar.gz -C /opt/

```

会把压缩包里的目录结构还原到 `/opt/` 下。
3. **静默解压（不显示文件列表）**

```bash
tar -xzf backup.tar.gz

```


# 命令解析
```
tar -xzvf backup.tar.gz -C /opt/
```

这条命令的意思是：**把 backup.tar.gz 解压到 /opt/ 目录下**，并保持原有的目录层级。



### 参数拆解


```bash
tar -xzvf backup.tar.gz -C /opt/

```

- `x` → extract，解压
- `z` → gzip 压缩格式
- `v` → verbose，显示过程
- `f` → 指定归档文件（`backup.tar.gz`）
- `-C /opt/` → 解压到 `/opt/` 目录（必须先存在，否则会报错）


### 执行效果


假设 `backup.tar.gz` 里包含：


```bash
user/
 ├── file1.txt
 └── docs/file2.md

```

解压后 `/opt/` 里会变成：


```bash
/opt/
 └── user/
      ├── file1.txt
      └── docs/file2.md

```


### 常见补充


1. **确保目录存在**

```bash
mkdir -p /opt
tar -xzvf backup.tar.gz -C /opt/

```
2. **静默解压（不打印文件列表）**

```bash
tar -xzf backup.tar.gz -C /opt/

```
3. **只想解压里面的 user/ 的内容，而不是整个 user/ 目录**
需要配合 `--strip-components` 参数，例如：

```bash
tar -xzvf backup.tar.gz -C /opt/ --strip-components=1

```
这样 `/opt/` 里直接得到 `file1.txt` 和 `docs/`。



# 我要解压到当前文件夹下的zhangsan文件夹如何操作

想要把 `backup.tar.gz` 解压到当前目录下的 `zhangsan/` 文件夹，可以这样做：


```bash
mkdir -p zhangsan
tar -xzvf backup.tar.gz -C zhangsan/

```


### 执行效果


假设 `backup.tar.gz` 里面内容是：


```bash
user/
 ├── file1.txt
 └── docs/file2.md

```

解压后当前目录下的 `zhangsan/` 会变成：


```bash
zhangsan/
 └── user/
      ├── file1.txt
      └── docs/file2.md

```


### 补充：如果不想要外层目录 user/


你可能希望直接把内容解压进 `zhangsan/`，而不是多一层 `user/`。这时可以用：


```bash
mkdir -p zhangsan
tar -xzvf backup.tar.gz -C zhangsan/ --strip-components=1

```

解压后结构：


```bash
zhangsan/
 ├── file1.txt
 └── docs/file2.md

```




# 在 `tar` 里 -C  的含义



在 `tar` 里：


```diff
-C DIR

```

👉 **含义就是在解压或打包前，先切换到指定的目录 DIR 去执行**。
相当于在运行 `tar` 之前，先做了一次 `cd DIR`。



### 使用场景


#### 1. 解压到指定目录


```bash
tar -xzvf backup.tar.gz -C /opt/

```

效果：把 `backup.tar.gz` 的内容解压到 `/opt/` 下，而不是当前目录。


#### 2. 打包时改变目录


```bash
tar -czvf backup.tar.gz -C /home user

```

效果：


- `-C /home` → 先进入 `/home`
- 再打包 `user/` 文件夹
- 所以归档里的路径是 `user/`，而不是 `/home/user/` 的完整路径。


### 补充说明


- `-C` 必须写在 **tar 选项和文件名之后**，紧跟一个目录路径。
- 可以使用 **多个 -C**，比如先切换到一个目录打包，再切到另一个目录继续打包：

```bash
tar -czvf files.tar.gz -C /etc ssh -C /var log

```
会把 `/etc/ssh` 和 `/var/log` 一起打进 `files.tar.gz`。




# tar命令的历史沿革
`tar` 命令虽然在日常用法里很常见，但它背后的历史和演进其实很有趣。下面我给你梳理一下 **tar 命令的历史沿革**。



## 1. 起源阶段（1979 年前后）


- **名字由来**：
`tar` = **tape archive**，最初是为了解决「把一堆文件连续写到磁带上」的问题。
- **早期 Unix**（V7 Unix，1979 左右）中，磁带是主要的存储介质。


磁带是顺序存取设备，不像硬盘能随机访问。


所以需要一个工具，把多个文件「顺序打包」在一起，同时保存元数据（文件名、时间戳、权限）。
- `tar` 就是为此而生，最早功能非常简单：打包、解包，专为磁带设计。


## 2. 标准化阶段（1980s）


- **BSD 与 System V**：


BSD Unix 和 AT&T System V 都包含了 `tar`，但实现略有不同。
- **POSIX 标准**（1988 年）：


定义了 **ustar**（Unix Standard TAR）格式，增加了：


用户名/组名存储


符号链接


更长的文件名（100 → 256 字符）




使得 `tar` 可以在不同 Unix 系统之间移植。


## 3. 扩展与兼容阶段（1990s–2000s）


- **GNU tar**（GNU 项目实现）：


增加了很多新特性，例如：


支持 `gzip` (`-z`)、`bzip2` (`-j`)、`xz` (`-J`) 压缩


支持 `--exclude`、`--wildcards` 等更灵活的操作




GNU tar 一度成为 Linux 世界的标准。
- **其它实现**：


Solaris 有 `star`（speedy tar）


BSD 系统逐渐采用 **libarchive** 库提供的 `bsdtar`（支持更多压缩算法和归档格式，比如 zip、cpio）。


## 4. 现代阶段（2010s–至今）


- **libarchive 统一化**：


BSD 的 `tar` 现在几乎可以解/打包各种归档格式（tar、cpio、zip、pax 等）。
- **Linux 生态**：


大多数发行版默认使用 GNU tar，但也有些（比如 FreeBSD、macOS）使用 bsdtar。
- **tar.gz / tar.bz2 / tar.xz 成为事实标准**：


Linux 软件包分发、源码压缩几乎都用 `tar`。


如：`Linux 内核源码` 就是以 `.tar.xz` 分发。


## 5. tar 的特点与遗产


- 优点：


保留 Unix 文件系统的所有元数据（权限、时间戳、符号链接等）


简单、稳定，几十年不变
- 局限：


单线程，压缩比和速度不如专门的工具（比如 `zstd`）


格式老旧（最初的文件名限制 100 字符，后来才扩展）


✅ **一句话总结**：
`tar` 从 1979 年的磁带打包工具，逐步演变为 Unix/Linux 世界里最重要的归档标准。虽然今天磁带几乎消失了，但 `tar` 依然是软件分发、系统备份的“事实标准”。