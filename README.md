# Cheems 的博客

基于 [Hugo](https://gohugo.io/) + [PaperMod](https://github.com/adityatelange/hugo-PaperMod) 的静态博客。
没有数据库、没有后台，所有内容都是 `content/` 下的 Markdown 文件。

## 写文章

```bash
./new "文章标题"          # 生成 content/posts/2026-10-29-文章标题.md
./new "Hello World" hello-world   # 手动指定文件名
```

设置过 `$EDITOR` 的话，创建完会自动用编辑器打开。

## 本地预览

```bash
hugo server -D
```

打开 <http://localhost:1313>。改内容会自动刷新，`-D` 表示草稿也一起显示。

## 发布

```bash
./publish                      # 提交信息自动生成
./publish "feat: 新文章"        # 或者自己写
```

推送到 GitHub 后，Actions 自动构建并部署到 GitHub Pages，一两分钟后线上生效。

## 目录结构

```
content/
├── posts/          文章（Markdown）
├── about.md        关于页
├── archives.md     归档页
└── search.md       搜索页
static/             图片等静态资源，引用路径 /xxx.jpg
themes/PaperMod/    主题（已内置，不需要额外安装）
hugo.toml           站点配置
new / publish       两个脚本
```

## 文章开头的 front matter

```yaml
---
title: "标题"
date: 2026-10-29T20:00:00+08:00
draft: false           # true = 草稿，不上线
tags: ["标签"]
categories: ["分类"]
summary: "列表页显示的摘要"
---
```

## 常用配置（`hugo.toml`）

| 想改什么 | 改哪里 |
| --- | --- |
| 站点标题 | `title` |
| 首页大字介绍 | `[params.homeInfoParams]` |
| 作者名 | `[params] author` |
| 社交图标 | `[[params.socialIcons]]` |
| 导航菜单 | `[[menu.main]]` |
| 网站地址 | `baseURL` |

图片直接放在 `static/images/` 下，文章里写 `![描述](/images/xxx.jpg)`。

## 部署

首次部署需要一次性配置：

1. 在 GitHub 创建仓库并推送（详见仓库根目录操作记录）。
2. 仓库 **Settings → Pages → Build and deployment → Source** 选择 **GitHub Actions**。
3. 推送任意提交，`.github/workflows/deploy.yml` 会自动构建部署。

之后每次 `./publish` 就是完整流程。

## 更新主题

主题是直接复制进 `themes/PaperMod/` 的，没有用 submodule。想更新时重新下载覆盖即可，
当前版本记录在 `themes/PaperMod/.upstream-commit`。
