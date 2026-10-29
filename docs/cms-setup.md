# 网页端写作（Sveltia CMS）配置

配置好之后，打开 <https://word1230.github.io/admin/> 就能在浏览器里写文章，
支持手机。保存后直接提交到 GitHub，触发自动部署。

> 原理：浏览器不能持有 GitHub 的 client secret，所以需要一个很小的 OAuth 中转服务。
> 这里用 Cloudflare Workers 免费跑官方模板 `sveltia-cms-auth`，只在登录时用一次。

以下步骤只需要做一次，大约 10 分钟。

---

## 第 1 步：部署 OAuth Worker

点这个按钮，用 GitHub 或邮箱登录 Cloudflare（免费账号即可）：

<https://deploy.workers.cloudflare.com/?url=https://github.com/sveltia/sveltia-cms-auth>

一路点确认，部署完成后会得到一个地址，形如：

```
https://sveltia-cms-auth.<你的子域>.workers.dev
```

**把这个地址记下来**，第 3 步和第 4 步都要用。

> 不想用按钮的话：`git clone https://github.com/sveltia/sveltia-cms-auth`，
> 进去 `npx wrangler deploy` 也一样。

---

## 第 2 步：创建 GitHub OAuth App

打开 <https://github.com/settings/applications/new>，填：

| 字段 | 值 |
| --- | --- |
| Application name | 博客编辑器（随便写） |
| Homepage URL | `https://word1230.github.io/` |
| Authorization callback URL | `https://sveltia-cms-auth.<你的子域>.workers.dev/callback` |

**callback 地址必须一字不差**，末尾是 `/callback`，子域换成第 1 步拿到的那个。

创建后：

- `Client ID` 直接可见，复制备用
- 点 **Generate a new client secret**，复制那个 secret（**只显示一次**）

---

## 第 3 步：给 Worker 设置环境变量

打开 Cloudflare 控制台 → **Workers & Pages** → 点进 `sveltia-cms-auth` →
**Settings** → **Variables and Secrets**，添加三条：

| 名称 | 值 | 类型 |
| --- | --- | --- |
| `GITHUB_CLIENT_ID` | 第 2 步的 Client ID | Text |
| `GITHUB_CLIENT_SECRET` | 第 2 步的 Client Secret | **Secret**（加密） |
| `ALLOWED_DOMAINS` | `word1230.github.io` | Text |

`ALLOWED_DOMAINS` 是防止别人借用你的 Worker 登录，别漏。

保存后回到 Worker 页面点一次 **Deploy**（或 Deployments → 重新部署），让变量生效。

---

## 第 4 步：把 Worker 地址填进博客

编辑 `static/admin/config.yml`，把这一行换掉：

```yaml
  base_url: https://sveltia-cms-auth.<你的子域>.workers.dev
```

然后推送：

```bash
cd ~/Projects/blog-site
./publish "feat: 启用网页端写作"
```

---

## 第 5 步：登录

等 Actions 跑完，打开：

<https://word1230.github.io/admin/>

点 **Sign in with GitHub**，在弹出的窗口里授权。授权一次之后，浏览器会记住。

之后新建文章、编辑正文、上传图片，点 **Save** 就会提交到仓库，
和本地 `./publish` 的效果一样，Actions 自动构建部署。

---

## 说明

- **`_index.md` 不会出现在列表里**，Sveltia 默认就跳过 Hugo 的索引文件。
- **图片**存在仓库 `static/images/`，CMS 里上传后自动插入 `/images/xxx.jpg` 的引用。
- **草稿**：把 `draft` 打勾保存，线上就不会出现（本地 `hugo server -D` 能看到）。
- **密匙安全**：client secret 只存在 Cloudflare Worker 里，仓库和网页端都看不到。
- **撤销授权**：GitHub → Settings → Applications 里可以随时取消；Cloudflare 里删掉 Worker 即可完全停用。
- **升级 CMS**：`static/admin/index.html` 里锁了版本号，改那一行即可。
