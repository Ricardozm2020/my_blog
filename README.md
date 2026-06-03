# Richardozm Blog

这是我的个人博客项目，基于 Next.js、React、TypeScript 和 GitHub 仓库内容管理改造而来。

原项目的部署和 GitHub App 使用教程已保留在 [docs/original-project-guide.md](docs/original-project-guide.md)，后续需要回看配置步骤时可以参考。

## 当前状态

- 站点名称：Richardozm
- 默认仓库：`Ricardozm2020/my_blog`
- 内容存储：文章、项目、图片、友链等数据主要保存在仓库内的 JSON 和 Markdown 文件中
- 本地开发端口：`2025`

## 本地开发

```bash
pnpm install
pnpm dev
```

打开 `http://localhost:2025` 预览。

## 需要重点替换的个人信息

下面这些文件会直接影响前台展示或部署配置：

| 文件 | 用途 |
| --- | --- |
| `src/config/site-content.json` | 站点标题、描述、首页用户名、主题色、社交链接、备案信息 |
| `public/images/avatar.png` | 首页头像 |
| `src/app/about/list.json` | 关于页标题、描述和正文 |
| `public/blogs/index.json` 与 `public/blogs/*` | 博客文章索引、文章正文、封面、分类 |
| `src/app/projects/list.json` | 项目页列表 |
| `src/app/share/list.json` | 推荐/收藏链接列表 |
| `src/app/bloggers/list.json` | 博主/友链列表 |
| `src/app/pictures/list.json` 与 `public/images/pictures/*` | 图片页内容 |
| `src/consts.ts` | GitHub App 默认 owner、repo、branch 等配置 |
| `wrangler.toml` | Cloudflare Workers 项目名 |
| `public/manifest.json` | PWA 名称与图标配置 |

## 后续清理建议

这个仓库从开源博客项目改造而来，仍可能残留两类旧内容：

1. 原作者的示例数据：项目集、推荐链接、友链、图片、示例文章。
2. 原项目的教程资料：部署截图、GitHub App 配置说明、使用引导。

教程资料建议继续放在 `docs/` 或作为隐藏文章保留；示例数据如果不属于你的博客内容，建议逐步替换为自己的文章、项目和收藏。
