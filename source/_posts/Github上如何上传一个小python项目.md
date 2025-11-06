---
title: Github上如何上传一个小python项目
date: 2025-11-06 15:13:29
tags:
---
# 发布代码到github上使用vscode的界面工具最方便。
# 发布代码到 GitHub Release 上



## 🧩 一、准备环境


首先，请确认电脑上安装了以下工具：

<!--more-->
1. ✅ **Python 3.8+**
2. ✅ **pip 工具可用**
3. ✅ **qrcode 库**

```bash
pip install qrcode[pil]

```
4. ✅ **PyInstaller（用于打包）**

```bash
pip install pyinstaller

```


## 🛠️ 二、打包成 .exe


假设你的脚本名为 `make_qr.py`。


在命令行（PowerShell 或 CMD）中进入该文件所在目录，然后执行：


```bash
pyinstaller --onefile --noconsole make_qr.py

```

参数说明：


- `--onefile`：打包成一个单独的可执行文件
- `--noconsole`：不弹出命令行窗口（如果是图形界面可以加上；你的程序是命令行工具，可不加）
👉 如果你希望能看到打印输出，请改成：

```bash
pyinstaller --onefile make_qr.py

```

执行后，PyInstaller 会生成以下目录结构：


```
dist/
  └── make_qr.exe    ← ✅ 可执行文件就在这里
build/
make_qr.spec

```

然后双击 `dist/make_qr.exe` 就可以直接运行程序啦！



## 📦 三、上传到 GitHub 并发布 Release


1. **提交代码到 GitHub 仓库**

```bash
git init
git add .
git commit -m "Add QR code generator"
git branch -M main
git remote add origin https://github.com/你的用户名/仓库名.git
git push -u origin main

```
2. **将打包的 exe 上传**

- 在 GitHub 网页上进入仓库
- 点击顶部 **“Releases” → “Draft a new release”**
- 填写：


Tag version（例如 `v1.0.0`）


Release title（例如 “初始版本”）


描述说明
- 上传文件：
👉 上传路径 `dist/make_qr.exe`
- 点击 **“Publish release”**

这样别人就能直接在你的 GitHub Release 页面下载 `.exe` 文件使用了。




# “如果用 Python 虚拟环境（venv）开发，然后上传到 GitHub，会不会有问题？”



结论是：
✅ **不会有问题**，但你需要注意 **不要把整个虚拟环境上传**。


我来给你详细解释原因和正确做法👇



## 🧩 一、为什么虚拟环境不能直接上传


当你创建虚拟环境：


```bash
python -m venv venv

```

它会生成一个文件夹，比如：


```arduino
my_project/
 ├── venv/           ← ❌ 这个文件夹不要上传
 ├── make_qr.py
 ├── config.txt
 └── requirements.txt

```

`venv` 目录里包含了所有依赖库和解释器文件（几十 MB），与系统路径有关。
上传它没意义：别人下载后用不了，还会让仓库臃肿。



## ✅ 二、正确的做法


只需上传你的代码和一个依赖说明文件 —— `requirements.txt`


### 1. 生成依赖文件


在虚拟环境中运行：


```bash
pip freeze &gt; requirements.txt

```

它会生成类似内容：


```ini
Pillow==10.0.0
qrcode==7.4.2

```

这样别人（或你自己换电脑）只需执行：


```bash
pip install -r requirements.txt

```

就能复现相同的环境。



## 🧹 三、Git 忽略虚拟环境目录


在项目根目录创建 `.gitignore` 文件（如果没有）：


```markdown
# 忽略虚拟环境目录
venv/
__pycache__/
*.pyc
*.pyo
build/
dist/
*.spec

```

💡 注意：`build/`、`dist/` 和 `.spec` 也是 PyInstaller 打包时自动生成的文件夹，不需要上传。