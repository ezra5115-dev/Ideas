# Ideas 网站发布规则

这是一个个人想法仓库，部署在 Cloudflare Pages，由 Agent 维护。

- 网站：https://syd-idea.pages.dev
- 仓库：ezra5115-dev/Ideas
- 站点根目录：`ideas-site-template/`
- 主分支：`main`

## 内容目录

所有公开内容放在：

`content/notes/`

每篇是一个 Markdown，文件名是英文 slug，例如：

`studio-as-long-term-workbench.md`

## 分类

```
notes     碎片想法
studio    Studio 产品思考
worlds    世界观档案
reads     阅读摘录（带原文）
prompts   Prompt 实验
logs      项目日志
```

如果用户没指定分类，按内容判断。

## 自动任务（Agentic）

### 每周日 20:00 — 周报

自动执行步骤：
1. 读取 `/api/articles.json`，找出本周新增的笔记（date 在本周范围内）。
2. 读取 `/content/projects.json`，检查每个 project 的 checklist 完成情况。
3. 读取本周新增笔记的正文，提取跟各 project 相关的想法。
4. 生成一篇周报，内容包括：
   - 本周新增笔记列表（标题 + 链接 + 一句话摘要）
   - 每个项目的进度（完成/未完成 checklist）
   - 本周想法中跟项目相关的建议（自动关联）
   - 下周建议推进的 checklist 项
5. 将周报发布到网站（分类 logs，tags: weekly-review）。
6. 同时更新 `/content/projects.json` 中对应项目的 checklist（如有新增建议项）。

### 工作日 09:00 — 项目检查

自动执行步骤：
1. 读取 `/content/projects.json`，找出所有未完成的 checklist 项。
2. 读取本周新增内容，判断是否有想法跟这些未完成项相关。
3. 在 Cohub 对话中返回：
   - 今天最应该推进的一项（优先级最高）
   - 本周已完成的项
   - 一直卡住的项（超过 7 天未更新的）

---

### 4. 每日记录

用户说「记录每日：今天做了什么」时：
1. 读取 `/content/daily.json`
2. 如果今天已有记录，在 content 末尾追加「；」+ 新内容
3. 如果没有，新增一条 `{ "date": "YYYY-MM-DD", "content": "...", "tags": [...] }`
4. 更新 `updated`，提交 GitHub
5. 返回确认

---

## 发布工作流（Agentic）

### 1. 碎片 / 想法

用户：

```
发布到 Ideas：
（一段想法）
```

Agent：
1. 整理一段简报（要点）和一段整理稿（保留原意，分段）。
2. 写入 `content/notes/<slug>.md`。
3. 更新 `content/notes.json`、`api/articles.json`、`llms.txt`、`llms-full.txt`、`rss.xml`、`sitemap.xml`。
4. 提交 GitHub。

### 2. 外部文章（带原文）

用户给一条链接 / 一段抓到的全文 / 截图文字 + 一两句自己的看法：

```
发布到 reads：
原文：https://xxx
我的看法：……
```

Agent：
1. 解读原文，写一段“简报”（5–8 条要点）。
2. 写一段“整理稿”（按原文逻辑结构化，不照搬全文）。
3. 如果用户给了全文，放到 `{source}` 区段。
4. 把用户的看法放到 `{mine}` 区段。
5. frontmatter 必填：
   - `source_url`
   - `source_title`
   - `source_author`（如果有）
6. 分类一般是 `reads`。
7. 同样更新所有索引文件并提交。

### 3. 长期项目（世界观 / Studio）

用户描述一个已有项目：

Agent：
1. 优先更新已有的同主题文章，而不是新建一个孤立文件。
2. 通过 slug 判断主题，例如 `chibi-aftershock`、`studio-as-long-term-workbench`。
3. 在文末新增新章节，用 `## 更新 YYYY-MM-DD` 分隔。

## Markdown frontmatter 规范

```yaml
---
title: "文章标题"
date: "YYYY-MM-DD"
category: "notes"
tags: ["标签1", "标签2"]
summary: "一句话摘要，会进 TLDR 框"
readMin: 4
# 外部文章可选字段
source_url: "https://example.com/article"
source_title: "原文标题"
source_author: "作者"
status: "published"   # 草稿写 draft，首页不展示
---
```

## 正文分区

正文支持 4 个区段，渲染成 Tabs：

```
## {brief}
（简报：5–8 条要点）

## {full}
（整理稿，保留用户原意）

## {source}
（原文或原文链接 / 关键片段）

## {mine}
（我的评论 / 思考）
```

如果只写一段正文不分区，文章页会合并为单页显示。

## 文件命名

- slug 全部小写英文，用 `-` 连接。
- 中文标题 → 英文 slug，例如：`studio-as-long-term-workbench`。
- 标题保持中文。

## 写作风格

- 保留用户原始意思。
- 不要写成营销文。
- 不要堆 AI 味词（“赋能 / 抓手 / 拥抱 / 重塑”等）。
- 短句、有节奏、敢留白。
- 简报用要点，整理稿可以有节奏，不必都用列表。

## 索引文件

每次发布都同步：

- `content/notes.json`
- `api/articles.json`
- `llms.txt`（目录）
- `llms-full.txt`（全文合集）
- `rss.xml`
- `sitemap.xml`

所有索引最新条目排在前面。

## 草稿

如果 frontmatter 是 `status: draft`：

- 不出现在 `notes.json` / `articles.json` / `llms.txt` / `rss.xml`。
- Markdown 文件仍然提交。
- 用户可以通过 `/note.html?slug=xxx` 直接访问。
