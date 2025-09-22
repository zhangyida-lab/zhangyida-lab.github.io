---
title: 一个将word文件转换为markdown文件的脚本
date: 2025-09-08 10:25:52
tags: [代码实例,python]
---




## 功能说明

- 遍历当前目录下所有 .docx 文件；

- 使用 pypandoc 调用 Pandoc，把 .docx 转换成 .md；
- Markdown 图片路径只保留文件名：![](image59.png)。

- 图片拷贝到 Markdown 文件同目录，无需依赖 _media 文件夹

<!--more-->

```python


import os
import re
import pypandoc
import shutil

def convert_img_to_md_syntax(md_file, media_dir, target_dir):
    """把 <img> 标签或带路径的 Markdown 图片替换成 ![](文件名)"""
    with open(md_file, "r", encoding="utf-8") as f:
        content = f.read()

    # 替换 <img src="xxx" ...> 为 ![](文件名)
    content = re.sub(
        r'<img\s+[^>]*src="([^"]+)"[^>]*>',
        lambda m: f"![Image]({os.path.basename(m.group(1))})",
        content
    )

    # 替换 Markdown ![](路径) 为 ![](文件名)
    content = re.sub(
        r'!\[.*?\]\(([^)]+)\)',
        lambda m: f"![Image]({os.path.basename(m.group(1))})",
        content
    )

    with open(md_file, "w", encoding="utf-8") as f:
        f.write(content)

    # 拷贝 _media 文件夹中的所有图片到 Markdown 文件同目录
    if os.path.exists(media_dir):
        for root, _, files in os.walk(media_dir):
            for file in files:
                src_file = os.path.join(root, file)
                dst_file = os.path.join(target_dir, file)
                # 如果目标目录已存在同名文件，则覆盖
                shutil.copy2(src_file, dst_file)

def convert_docx_to_md(folder='.'):
    for filename in os.listdir(folder):
        if filename.lower().endswith('.docx'):
            input_path = os.path.join(folder, filename)
            output_path = os.path.splitext(input_path)[0] + '.md'
            media_dir = os.path.splitext(filename)[0] + "_media"
            target_dir = folder  # Markdown 同目录

            try:
                # 转换 docx -> md
                pypandoc.convert_file(
                    input_path,
                    'gfm',
                    outputfile=output_path,
                    extra_args=[f'--extract-media={media_dir}']
                )

                # 修改 Markdown 图片路径，并拷贝图片到 Markdown 同目录
                convert_img_to_md_syntax(output_path, media_dir, target_dir)

                print(f"✅ 已转换: {filename} -> {os.path.basename(output_path)} (图片已拷贝到同目录)")
            except Exception as e:
                print(f"❌ 转换失败 {filename}: {e}")

if __name__ == '__main__':
    convert_docx_to_md('.')

```