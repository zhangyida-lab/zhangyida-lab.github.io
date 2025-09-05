---
title: lsp程序的一个实例
date: 2025-08-15 11:17:49
tags:
---

# lsp程序的一个实例
通过cad命令，选中一个区域快速帮我快速把dwg文件中手工绘制的表格快速网格化，网格的线单独在一个辅助线图层，并为红色，然后标注网格间的尺寸和纵横格子数量


你这个需求可以通过 AutoLISP（LSP） 来实现，功能目标是：

手动框选一个区域；
<!--more-->
分析其中的手工绘制表格线（一般是直线）；

自动生成等价的网格结构线（在单独图层，红色）；

标注每个单元格的尺寸；

输出纵横格子数量。



# 用户问题
(defun remove-duplicates (lst)
  (if lst
    (cons (car lst)
          (remove-duplicates (vl-remove (car lst) (cdr lst))))
    nil
  )
)

(defun index-to-letter (n)
  ;; 将 1 转为 A，2 转为 B，... 26-&gt;Z，27-&gt;AA，支持多列
  (if (&lt; n 1)
    ""
    (strcat
      (index-to-letter (/ (1- n) 26))
      (chr (+ 64 (rem (1- n) 26) 1))
    )
  )
)

(defun c:ZZ ( / p1 p2 ss i ent hor ver
                 pt1 pt2 x1 x2 y1 y2
                 hor-pos ver-pos
                 x y x-min x-max y-min y-max
                 row-count col-count offset grid-lines
                 doc layers newLayer
                 diments dim
                 seq-text-height seq-x seq-y)

  (vl-load-com)
  (setq offset 30) ;; 辅助线延长长度
  (setq seq-text-height 4) ;; 编号文字高度

  ;; 创建红色辅助图层（如果不存在）
  (if (not (tblsearch "LAYER" "GRID-LAYER"))
    (progn
      (setq doc (vla-get-ActiveDocument (vlax-get-Acad-Object)))
      (setq layers (vla-get-Layers doc))
      (setq newLayer (vla-Add layers "GRID-LAYER"))
      (vla-put-Color newLayer 1) ; 红色
    )
  )
  (setvar "CLAYER" "GRID-LAYER")

  ;; 选取区域两个角点
  (setq p1 (getpoint "\n选择表格区域的第一个角点: "))
  (setq p2 (getcorner p1 "\n选择对角点: "))

  ;; 获取区域内所有线段
  (setq ss (ssget "W" p1 p2 '((0 . "LINE"))))

  (if ss
    (progn
      (setq hor '() ver '() i 0)

      ;; 分类横线和竖线
      (while (&lt; i (sslength ss))
        (setq ent (entget (ssname ss i)))
        (setq pt1 (cdr (assoc 10 ent)))
        (setq pt2 (cdr (assoc 11 ent)))
        (setq x1 (car pt1) y1 (cadr pt1))
        (setq x2 (car pt2) y2 (cadr pt2))
        (cond
          ((&lt; (abs (- y1 y2)) 1e-6) (setq hor (cons y1 hor)))
          ((&lt; (abs (- x1 x2)) 1e-6) (setq ver (cons x1 ver)))
        )
        (setq i (1+ i))
      )

      ;; 去重排序
      (setq hor-pos (vl-sort (remove-duplicates hor) '&lt;))
      (setq ver-pos (vl-sort (remove-duplicates ver) '&lt;))

      ;; 行列数量
      (setq row-count (1- (length hor-pos)))
      (setq col-count (1- (length ver-pos)))

      ;; 边界
      (setq x-min (car ver-pos))
      (setq x-max (last ver-pos))
      (setq y-min (car hor-pos))
      (setq y-max (last hor-pos))

      ;; UNDO分组：辅助线
      (command "UNDO" "GROUP")
      ;; 竖线
      (foreach x ver-pos
        (entmake (list
          (cons 0 "LINE") (cons 8 "GRID-LAYER") (cons 62 1)
          (cons 10 (list x (- y-min offset) 0.0))
          (cons 11 (list x (+ y-max offset) 0.0))
        ))
      )
      ;; 横线
      (foreach y hor-pos
        (entmake (list
          (cons 0 "LINE") (cons 8 "GRID-LAYER") (cons 62 1)
          (cons 10 (list (- x-min offset) y 0.0))
          (cons 11 (list (+ x-max offset) y 0.0))
        ))
      )
      (command "UNDO" "END")

      ;; UNDO分组：尺寸标注
      (command "UNDO" "GROUP")
      (setq diments (ssadd))

      ;; 行高标注（左侧）
      (setq i 0)
      (while (&lt; i row-count)
        (command "_.DIMLINEAR"
                 "_H"
                 (list x-min (nth i hor-pos) 0.0)
                 (list x-min (nth (1+ i) hor-pos) 0.0)
                 (list (- x-min 50) (/ (+ (nth i hor-pos) (nth (1+ i) hor-pos)) 2) 0.0)
        )
        (setq dim (entlast))
        (if dim (ssadd dim diments))
        (setq i (1+ i))
      )

      ;; 列宽标注（顶部）
      (setq i 0)
      (while (&lt; i col-count)
        (command "_.DIMLINEAR"
                 "_V"
                 (list (nth i ver-pos) y-max 0.0)
                 (list (nth (1+ i) ver-pos) y-max 0.0)
                 (list (/ (+ (nth i ver-pos) (nth (1+ i) ver-pos)) 2) (+ y-max 50) 0.0)
        )
        (setq dim (entlast))
        (if dim (ssadd dim diments))
        (setq i (1+ i))
      )
      (command "UNDO" "END")

      ;; UNDO分组：编号文字、说明文字
      (command "UNDO" "GROUP")

      ;; 添加说明文字
      (command "_.TEXT"
               (list x-max (+ y-max 50) 0.0)
               10
               0
               (strcat (itoa row-count) "行 × " (itoa col-count) "列"))

      ;; 行号编号（左边，数字，从上往下）
      (setq i 1)
      (while (&lt; i (length hor-pos))
        (setq seq-x (- x-min 60))
        (setq seq-y (/ (+ (nth (1- i) hor-pos) (nth i hor-pos)) 2))
        (command "_.TEXT"
                 (list seq-x seq-y 0.0)
                 seq-text-height
                 0
                 (itoa i))
        (setq i (1+ i))
      )

      ;; 列号编号（顶部，字母，从左往右）
      (setq i 1)
      (while (&lt; i (length ver-pos))
        (setq seq-x (/ (+ (nth (1- i) ver-pos) (nth i ver-pos)) 2))
        (setq seq-y (+ y-max 60))
        (command "_.TEXT"
                 (list seq-x seq-y 0.0)
                 seq-text-height
                 0
                 (index-to-letter i))
        (setq i (1+ i))
      )

      ;; 辅助线图层置底
      (setq grid-lines (ssget "X" '((8 . "GRID-LAYER"))))
      (if grid-lines
        (command "_DRAWORDER" grid-lines "" "B")
      )
      (command "UNDO" "END")

      (princ (strcat "\n[OK] 已生成表格，共 " (itoa row-count) " 行 × " (itoa col-count) " 列"))
    )
    (prompt "\n[警告] 区域中未检测到线段，请重新选择。")
  )
  (princ)
)
# 回答
你的这段 AutoLISP 脚本功能非常完整，下面是它的核心功能总结和一些优化建议（如果你想进一步精进）：



## ✅ 当前功能回顾


你定义的 `c:ZZ` 命令主要完成以下任务：


### 1. **辅助图层与图形生成**


- 创建图层 `GRID-LAYER`，设为红色。
- 根据选定区域内的线段分类为横线和竖线，并去重、排序。
- 在这些位置绘制延伸的红色辅助线。

### 2. **尺寸标注**


- 为每行高度添加线性标注（标注在线左侧）。
- 为每列宽度添加线性标注（标注在线顶部）。

### 3. **编号与文字说明**


- 行编号：左侧，数字，从上往下。
- 列编号：顶部，字母，从左往右。
- 在右上角添加“n行 × m列”的文字说明。

### 4. **图形图层排序**


- 将辅助线图层 `GRID-LAYER` 移到最底层。


## 📌 建议优化与扩展（仅供参考）


### 1. **支持选择图层**


当前是固定操作 `LINE` 实体，若扩展支持选择特定图层可能更通用。


```lisp
(setq ss (ssget "W" p1 p2 '((0 . "LINE") (8 . "YourLayerName"))))

```

可添加提示让用户选择要处理的图层。



### 2. **调整辅助线延长长度为参数**


你现在写死 `offset 30`，可以让用户输入：


```lisp
(initget 6)
(setq offset (getdist "\n输入辅助线延长长度 &lt;默认30&gt;: "))
(if (null offset) (setq offset 30))

```


### 3. **支持对标注文字样式的控制**


目前直接使用 `command "_.TEXT"`，可以增加文字样式选择或使用 `entmake` 更细致地控制样式。



### 4. **行列编号位置灵活设置**


你目前写死了：


- 行号放左侧；
- 列号放顶部。

可以进一步扩展：


- 行号支持放右侧；
- 列号支持放底部。

通过让用户选择：`("顶部" "底部")` / `("左侧" "右侧")`。



## ✅ 程序结构与稳定性良好


- 使用了 `vl-sort` 和 `remove-duplicates` 结合，自定义函数处理数据整洁；
- 使用 `UNDO GROUP` 分组操作非常专业，方便用户回退；
- 结构清晰、易于维护。


## 🎯 小结


这个 `c:ZZ` 命令在 AutoCAD 表格网格标注场景中已经具备了很高的实用性，功能完整、鲁棒性强。你已经完成了一个 **专业级的自动编号和标注工具**！


如果你希望我帮你输出 `.lsp` 文件版本或继续增加某项功能（比如增加自动标注样式或更复杂表头布局支持），我可以协助你继续开发。


需要我帮你生成 `.lsp` 文件吗？或者还有其他需求？
# 用户问题
修改 行号标注从上往下，从1开始
# 回答
你当前的代码中，**行号编号从上往下、从1开始**，**实际上是反了的**，因为 `hor-pos` 是从下到上排序的，而你从前往后读取列表元素，导致数字是从下往上编号的。



## ✅ 修改方法


你只需要 **将 hor-pos 列表反转** 后再进行编号即可。


### 👇 修改前这部分代码：


```lisp
;; 行号编号（左边，数字，从上往下）
(setq i 1)
(while (&lt; i (length hor-pos))
  (setq seq-x (- x-min 60))
  (setq seq-y (/ (+ (nth (1- i) hor-pos) (nth i hor-pos)) 2))
  (command "_.TEXT"
           (list seq-x seq-y 0.0)
           seq-text-height
           0
           (itoa i))
  (setq i (1+ i))
)

```


### ✅ 修改为如下代码：


```lisp
;; 行号编号（左边，数字，从上往下）
(setq rev-hor-pos (reverse hor-pos))
(setq i 1)
(while (&lt; i (length rev-hor-pos))
  (setq seq-x (- x-min 60))
  (setq seq-y (/ (+ (nth (1- i) rev-hor-pos) (nth i rev-hor-pos)) 2))
  (command "_.TEXT"
           (list seq-x seq-y 0.0)
           seq-text-height
           0
           (itoa i))
  (setq i (1+ i))
)

```


这样，行号将从上往下编号，且从1开始，与你的需求一致 ✅


如果你希望我把完整修正后的 `.lsp` 文件内容打包发你，我可以马上提供。是否需要？
# 用户问题
我这个lsp脚本cad2010支持吗
# 回答
你的这段 LISP 脚本在 **AutoCAD 2010** 中基本是 **兼容的**，但要特别注意以下几点兼容性问题：



## ✅ **兼容性良好的部分**


| 功能 | CAD 2010 支持 | 说明 |
| ---- | ---- | ---- |
| (vl-load-com) | ✅ | CAD 2000 以上版本都支持 |
| vla-get-ActiveDocument 等 COM 接口 | ✅ | 完全支持 |
| tblsearch, entget, entmake, command 等核心函数 | ✅ | 标准 AutoLISP |
| ssget, sslength, ssname, entlast 等选择集函数 | ✅ | 标准 AutoLISP |
| vl-sort | ✅ | Visual LISP 支持 |
| chr, strcat, itoa, nth 等基本函数 | ✅ | 全支持 |
| 图层颜色设置 (vla-put-Color newLayer 1) | ✅ | 支持 |



## ⚠️ **你需要注意的问题**


### 1. ✅ (vl-remove ...) 是 CAD 2010 支持的


你用了：


```lisp
(remove-duplicates (vl-remove (car lst) (cdr lst)))

```

这是完全 OK 的，在 CAD 2000+ 支持 Visual LISP 的版本中都可用。



### 2. ⚠️ DRAWORDER 命令（图形置底）


```lisp
(command "_DRAWORDER" grid-lines "" "B")

```

- AutoCAD 2010 支持 `DRAWORDER` 命令 ✅；
- 但 `grid-lines` 是选择集变量，使用时必须是有效的 `ssget` 结果；
- 确保 `grid-lines` 不为 `nil`，且里面确实有实体。


### 3. ❗ Unicode 字符支持（如中文路径、字体）


- 你的脚本没有使用中文文字内容，所以 **没有问题**；
- 但在 AutoCAD 2010 的中文版本中，**默认字体和 _.TEXT 的显示效果可能有兼容性问题**，例如文字显示为问号 `???`；


建议：使用文字样式（Text Style）或指定字体（如 `txt.shx` 或 `simhei.ttf`）来避免乱码。


## ✅ 建议测试前设置


在 CAD 2010 加载前建议：


1. 打开 AutoCAD；
2. 执行 `APPLOAD`，加载你的 `.lsp` 文件；
3. 运行命令 `ZZ`；
4. 观察命令行与绘图结果是否一致；
5. 如果提示 `(vl-load-com)` 找不到，说明未启用 Visual LISP，建议切换 AutoCAD 2010 的完整版（非 LT 版）。


## ❌ AutoCAD LT 不支持


AutoCAD LT 是“精简版”，**完全不支持 AutoLISP 或 Visual LISP**，包括：


- `vl-*` 系列函数（如 `vl-load-com`）
- `entmake`、`vla-...` 系列 COM 接口

**结论**：请确认你使用的是 AutoCAD 2010 标准版或高级版，不是 LT 版。



## ✅ 小结


| 项目 | 状态 |
| ---- | ---- |
| 在 AutoCAD 2010 标准版中运行 | ✅ 支持 |
| 使用了兼容的函数 | ✅ 无问题 |
| 可能需注意文字显示或图层顺序问题 | ⚠️ 轻微注意即可 |
| AutoCAD LT 支持 | ❌ 不支持任何 LISP 脚本 |



如果你希望我把当前代码打包为 `.lsp` 文件供你直接加载，我可以立即生成。需要吗？