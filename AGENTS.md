# AGENTS.md

Hugo 静态博客。内容 = `content/` 下的 Markdown，主题已 vendored 进 `themes/PaperMod/`。

- 写作与发布流程：[README.md](README.md)
- 网页端编辑器（Sveltia CMS）的配置与排错：[docs/cms-setup.md](docs/cms-setup.md)

## 三条约束

**日期必须带时区。** 写成 `2026-10-29T23:26:00+08:00`。无时区的 `date` 会被 Hugo 当作 UTC，
在东八区环境下晚 8 小时，成为"未来文章"而被静默过滤——编辑器显示保存成功，线上什么都没有。
`hugo.toml` 里的 `timeZone` 和 `buildFuture = true` 是兜底，新内容仍按带时区格式写。

**主题靠重新下载覆盖升级。** 没有 submodule、没有 Hugo Modules，`hugo mod` 一类命令不适用。
上游版本记在 `themes/PaperMod/.upstream-commit`。

**`main` 就是线上。** push 即构建部署，没有预发环境，验证在本地做完再推。

## 验证

```bash
rm -rf public && hugo --gc --minify
```

完成标准：输出零 `WARN` / `ERROR`，且期望的文章出现在 `public/posts/` 下。
新增或修改文章时，一并确认它的 `date` 早于当前时间。

## 仓库外依赖

网页端登录依赖仓库外的 Cloudflare Worker：`~/Projects/sveltia-cms-auth`，地址
`https://sveltia-cms-auth.word232386.workers.dev`，`static/admin/config.yml` 的
`backend.base_url` 指向它。改这个地址前先在 `/admin/` 确认登录仍可用。

该 Worker 零运行时依赖，部署只走 `npx wrangler deploy`。Cloudflare 的部署按钮会失败：
上游 `pnpm-lock.yaml` 记录了一条 `undici` override，`package.json` 里没有，
`--frozen-lockfile` 直接报 `ERR_PNPM_LOCKFILE_CONFIG_MISMATCH`。
