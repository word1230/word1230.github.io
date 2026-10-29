---
title: "第一篇文章：这里是怎么跑起来的"
date: 2026-10-29T20:00:00+08:00
draft: false
tags: ["随笔", "Hugo"]
categories: ["建站"]
summary: "记录这个博客的搭建方式，以及以后怎么写新文章。"
---

## 整体结构

这个博客是纯静态的：所有内容都是 `content/` 下的 Markdown 文件，构建时由 Hugo 生成 `public/` 里的 HTML。

```
blog-site/
├── content/
│   ├── posts/          ← 文章都放这里
│   ├── about.md
│   ├── archives.md
│   └── search.md
├── themes/PaperMod/    ← 主题（已内置，不用额外安装）
├── hugo.toml           ← 站点配置
├── new                 ← 新建文章
└── publish             ← 一键发布
```

## 写一篇新文章

```bash
./new "我的第二篇文章"
```

它会生成 `content/posts/2026-10-29-我的第二篇文章.md`（设置了 `$EDITOR` 的话会顺手帮你打开）。

本地预览：

```bash
hugo server -D
```

浏览器打开 <http://localhost:1313>，改文件时会自动刷新。

## 发布

```bash
./publish                      # 自动生成提交信息
./publish "feat: 新增一篇文章"   # 或者自己写
```

推送到 GitHub 后，GitHub Actions 会自动构建并部署，一两分钟后线上就能看到。

## 文章开头的元信息

每篇文章顶部的 `---` 之间是 front matter：

```yaml
---
title: "标题"
date: 2026-10-29T22:30:00+08:00
draft: false            # true = 草稿，不会发布
tags: ["标签一", "标签二"]
categories: ["分类"]
summary: "列表页显示的摘要，可以不写"
---
```

`tags` 和 `categories` 会自动生成对应的归档页面。
