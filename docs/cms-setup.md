# 网页端写作（Sveltia CMS）配置

配置好之后，打开 <https://word1230.github.io/admin/> 就能在浏览器里写文章，
支持手机。保存后直接提交到 GitHub，触发自动部署。

> 原理：浏览器不能持有 GitHub 的 client secret，所以需要一个很小的 OAuth 中转服务。
> 这里用 Cloudflare Workers 免费跑官方模板 `sveltia-cms-auth`，只在登录时用一次。

---

## 当前状态

| 项目 | 值 |
| --- | --- |
| OAuth Worker | `https://sveltia-cms-auth.word232386.workers.dev` |
| Worker 源码 | `~/Projects/sveltia-cms-auth`（本地 git clone） |
| Cloudflare 账号 | word232386@gmail.com |
| 博客配置 | `static/admin/config.yml` 的 `base_url` 已填好 |

**还需要做**：创建 GitHub OAuth App，然后把三个变量写进 Worker。

---

## 第 1 步：创建 GitHub OAuth App

打开 <https://github.com/settings/applications/new>，填：

| 字段 | 值 |
| --- | --- |
| Application name | 博客编辑器（随便写） |
| Homepage URL | `https://word1230.github.io/` |
| Authorization callback URL | `https://sveltia-cms-auth.word232386.workers.dev/callback` |

**callback 地址必须一字不差**，末尾是 `/callback`。

创建后：

- `Client ID` 直接可见，复制备用
- 点 **Generate a new client secret**，复制那个 secret（**只显示一次**）

---

## 第 2 步：把凭据写进 Worker

在终端里执行（不要通过聊天窗口粘贴 secret）：

```bash
cd ~/Projects/sveltia-cms-auth

npx wrangler secret put GITHUB_CLIENT_ID
npx wrangler secret put GITHUB_CLIENT_SECRET
npx wrangler secret put ALLOWED_DOMAINS
```

每条命令会提示 `Enter a secret value:`，粘贴对应内容后回车：

| 变量 | 填什么 |
| --- | --- |
| `GITHUB_CLIENT_ID` | 第 1 步的 Client ID |
| `GITHUB_CLIENT_SECRET` | 第 1 步的 Client Secret |
| `ALLOWED_DOMAINS` | `word1230.github.io` |

`ALLOWED_DOMAINS` 用来防止别人借用你的 Worker 登录，别漏。

每次 `secret put` 都会自动重新部署，三条做完就生效。

> 也可以在 Cloudflare 控制台操作：**Workers & Pages → sveltia-cms-auth → Settings →
> Variables and Secrets**，效果一样。

---

## 第 3 步：登录

打开 <https://word1230.github.io/admin/>，点 **Sign in with GitHub**，
在弹出的窗口里授权。授权一次之后浏览器会记住。

之后新建文章、编辑正文、上传图片，点 **Save** 就会提交到仓库，
和本地 `./publish` 的效果一样，Actions 自动构建部署。

---

## 关于部署方式

**不要用 Cloudflare 的「Deploy to Cloudflare」按钮**。那个流程会跑
`pnpm install --frozen-lockfile`，而上游仓库的 `pnpm-lock.yaml` 里记录了
`undici` 的 `overrides`，`package.json` 里却没有对应字段，两者不一致会直接报
`ERR_PNPM_LOCKFILE_CONFIG_MISMATCH`。

这个 Worker 没有任何运行时依赖，所以正确做法是本地 `wrangler deploy`：

```bash
cd ~/Projects/sveltia-cms-auth
npx wrangler deploy
```

以后要更新到上游最新版：

```bash
cd ~/Projects/sveltia-cms-auth
git pull
npx wrangler deploy
```

---

## 说明

- **界面定制**：编辑器外观在 `static/admin/admin.css`，与新粗野主义博客主题
  （`assets/css/extended/neubrutalism.css`）同一套视觉语言：黑描边、硬阴影、
  0 圆角、明黄强调色。Sveltia 的类名不是稳定 API，升级版本后需核对样式是否仍生效。
- **`_index.md` 不会出现在列表里**，Sveltia 默认就跳过 Hugo 的索引文件。
- **图片**存在仓库 `static/images/`，CMS 里上传后自动插入 `/images/xxx.jpg` 的引用。
- **草稿**：把 `draft` 打勾保存，线上就不会出现（本地 `hugo server -D` 能看到）。
- **密匙安全**：client secret 只存在 Cloudflare Worker 里，仓库和网页端都看不到。
- **撤销授权**：GitHub → Settings → Applications 里可以随时取消；删掉 Worker 即可完全停用。
- **升级 CMS**：`static/admin/index.html` 里锁了版本号，改那一行即可。
