# Ideas

一个极简的个人想法仓库静态网站。适合部署到 Cloudflare Pages。

## 内容怎么更新

1. 在 `content/notes/` 里新增一篇 Markdown，例如 `studio-memory.md`
2. 在 `content/notes.json` 里增加这篇文章的条目
3. 同步更新 `api/articles.json` 和 `llms.txt`（可由 Agent 完成）
4. GitHub 更新后，Cloudflare Pages 会自动发布

## Cloudflare Pages 设置

- Build command: 留空
- Build output directory: `/`
