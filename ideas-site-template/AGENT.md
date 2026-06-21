# Ideas 网站发布规则

网站地址：https://syd-idea.pages.dev

## 内容目录

公开笔记放在：

`content/notes/`

每篇笔记是一个 Markdown 文件。

## 新增文章流程

当用户说“发布到 Ideas”时：

1. 根据用户原始内容整理成 Markdown。
2. 在 `content/notes/` 下新增一篇 `.md` 文件。
3. 更新 `content/notes.json`。
4. 同步更新 `api/articles.json`。
5. 更新 `llms.txt`。
6. 提交到 GitHub，等待 Cloudflare Pages 自动部署。

## Markdown 格式

```markdown
---
title: "文章标题"
date: "YYYY-MM-DD"
category: "notes"
tags: ["标签1", "标签2"]
summary: "一句话摘要"
---

# 文章标题

正文内容。
```

## 分类规则

- 碎片想法：notes
- Studio 产品思考：studio
- 世界观设定：worlds
- 阅读摘录：reads
- Prompt 实验：prompts
- 项目日志：logs

## 写作风格

- 保留用户原始意思。
- 不要写成营销文。
- 不要过度 AI 味。
- 可以分段、起标题、补小标题。
- 如果是碎片，保留一定碎片感。
- 如果属于长期项目，优先更新已有主题，而不是强行新建孤立文章。
