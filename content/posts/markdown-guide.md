---
title: "Markdown 速查表"
date: 2026-10-29T22:00:00+08:00
draft: false
tags: ["Markdown"]
categories: ["笔记"]
summary: "写文章时会用到的语法，忘了就回来翻一眼。"
---

## 标题

```markdown
# 一级标题
## 二级标题
### 三级标题
```

## 强调

```markdown
**加粗**  *斜体*  ~~删除线~~  `行内代码`
```

## 列表

```markdown
- 无序列表
- 第二项
  - 嵌套

1. 有序列表
2. 第二项
```

## 链接与图片

```markdown
[链接文字](https://example.com)
![图片描述](/images/photo.jpg)
```

图片放到 `static/images/` 下，引用路径写 `/images/photo.jpg`。

## 引用

```markdown
> 这是一段引用。
```

## 代码块

````markdown
```python
def hello(name):
    print(f"你好，{name}")
```
````

效果：

```python
def hello(name):
    print(f"你好，{name}")
```

```bash
hugo server -D
```

## 表格

```markdown
| 左对齐 | 居中 | 右对齐 |
| :--- | :---: | ---: |
| a | b | c |
```

| 左对齐 | 居中 | 右对齐 |
| :--- | :---: | ---: |
| a | b | c |

## 分隔线

```markdown
---
```

## 目录

文章会自动生成右侧目录（由标题层级决定），不用手动写。

## 折叠块

```markdown
{{</* collapse summary="点我展开" */>}}
被折叠的内容
{{</* /collapse */>}}
```

{{< collapse summary="点我展开" >}}
被折叠的内容。
{{< /collapse >}}
