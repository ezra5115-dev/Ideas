---
title: "周报 2026-07-27 ~ 2026-08-02：风格库吃掉一半产能，AVG 管线跑起来了"
date: "2026-08-02"
category: "logs"
tags: ["weekly-review","Studio","风格库","AVG","V5","归档"]
summary: "本周站点新增 2 篇笔记；Cohub 侧 87 个会话、23 个 Space 有更新，其中 45 个会话集中在风格库 V3→V4→V5。AVG-Build 管线首次跑出成品候选。归档断档比上周好转，但 Daily 仍停在 6/28，周报中间漏了两周。"
readMin: 6
status: "published"
---

## {brief}

- 站点本周新增 **2** 篇：7/29《极乐迪斯科》美术设计翻译、7/30 概念设计师原创 IP 与社媒起号。全站 16 篇。
- Cohub 活动 **87** 个会话、**23** 个 Space。其中 **45** 个会话（超过一半）在做同一件事：风格库从 V3 到 V5。
- 风格库主线：V4 命名定稿、发布校验、预览资产审计、跨 Space 命名对齐，然后直接进 V5 架构评审和 V4/V5 差异比对。
- 新增一条线：**AVG-Build 管线**（cand_095 / cand_096 共 10 会话），立绘重跑、抠图、build + publish、manifest 更新，已经是流水线形态而不是单次尝试。
- 世界侧 10 会话铺开但都很浅：三体、迷宫饭、DND、rainning world，多为开局。
- 项目面：两个 Project 的 checklist 本周**无完成项变化**，`projects.json` 仍停在 7/12。
- 两个旧问题没解决：Daily 最新条目仍是 **6/28**（晚间扫描 cron 没在写）；周报漏了 7/13~7/26 两周。
- 下周建议：1）把 V4→V5 的命名和校验规则写成 1 篇 notes，这是本周最值钱的可复用资产；2）用 AVG-Build 管线产出 GTM 那个卡了很久的 playable 纵向切片；3）修 Daily cron。

## {full}

### 一、本周新增笔记

周期 **2026-07-27 ~ 2026-08-02** 内新增 **2** 篇，全站累计 16 篇。

| 日期 | 分类 | 标题 |
|---|---|---|
| 2026-07-30 | studio | [概念设计师的原创 IP 之路：从 Ashley Wood 到 AI 时代社媒起号实战指南](https://syd-idea.pages.dev/note.html?slug=artist-ip-ai-era-social-launch) |
| 2026-07-29 | reads | [《极乐迪斯科》美术设计：截图原文与逐段翻译](https://syd-idea.pages.dev/note.html?slug=disco-elysium-art-direction-interviews) |

两篇都能在 Cohub 会话里找到出处：Disco Elysium 那篇来自 `SYD_Agamemnon` 的按文件名顺序翻译任务，IP 那篇来自同一 Space 里 Arcade California 的 Instagram 观察和海外社媒运营的实际提问。上周说的「做了但没写进 Lazarus Pit」，这周补上了一部分。

需要记一笔的漏账：上一篇周报是 **7/12**，之后 7/13~7/19、7/20~7/26 两个周期没有周报。中间产出的《Codex 中文创作者 10 个顶级 Skills》（7/19）和两篇状态管理 101（7/13）从未被任何周报收录过。周日 20:00 那条 cron 不像每周都在跑。

### 二、本周实际在做什么

**1. 风格库 V3 → V4 → V5（45 / 87 会话，绝对主线）**

- `Studio Style Library V4`（17）：预览资产审计、V4 发布校验、自包含预览校验；按源 Space 依据统一 V4 风格命名；Sakura Crossing 风格复刻走 Image2 + Gemini 交接，反复跑了 4 轮；V5 评审同样 4 轮。
- `Studio Styles Library V3`（7）：V4 命名与预设微调，对全 Space 风格做查缺补漏，并向 V4 交接。
- `Visual Style Library V5 Rebuild (candidate)`（5）：对着 Studio 里的 V4 逐条比差异，确认 V5 到底改了什么。
- `SYD 技能树探索尝试`（10）：主控 + Agent-B 提示词编辑 + Agent-C 美术风格库的分工试验，中途有「风格库 废弃」又「风格库继续」的反复。
- 零散：赛璐璐风格调用时的实际用词核查、攻壳 2026 风格四个方向（Krea2）、咒术回战既有图的 Prompt 反推与共用项归拢、640×640 统一分辨率。

这周不是在扩风格数量，是在给风格库做**版本治理**：命名、校验、预览、跨 Space 一致性。V4 收口的同时 V5 已经在评审，说明架构还没稳定下来。

**2. AVG-Build 管线（10 会话，本周新线）**

`AVG-Build-cand_095` / `cand_096` / `AVG Pipeline Control`：

- 明确 Build Agent 角色，输入文档落到固定路径，只重做角色立绘、不动场景与 BGM 的定向返工。
- 一次失败诊断后改用 gpt-image-2 重跑；另一版换 krea2 最终版（伦勃朗光、姿势变化、皮肤平滑）。
- 立绘抠图去白底转透明，再整合回游戏。
- 9 张立绘换完后 build + publish + 更新 manifest。
- Pipeline Control 里把生图模型（gemini-3.1-flash-image / krea2）和构建模型（deepseek-v4-flash / pro）当参数组合来试。

这条线的形态值得注意：有主控、有专属生产 World、有返工指令、有发布收尾。它已经是个能重复调用的管线。

**3. Studio 问题反馈（6 会话）**

`SYD STudio 问题反馈与思考`：先搞清 Cohub 与 Studio 的关系，读其他 Session 汇总问题，要求「得出结论前多想」；另有 ArtStyle V5 架构评审交接、三体 Space 检查。这是本周唯一在做元层面复盘的地方。

**4. 世界与其他（15 会话，普遍偏浅）**

- `Three-Body: Silent Universe`（3）：角色封面和地点封面没走 V4 Visual Style 的判断，以及用新风格重跑角色做替换对比。
- `量产进入世界`（2）：计划每天 10~30 张图 + seedance 出视频，scroll-world-video skill 试跑。
- 新世界开局：迷宫饭、DND、rainning world，各 1 会话。
- `SYD-Ulysses`（5）：东野圭吾纪念馆的沉浸式 / AVG 方向讨论、巨构方向讨论、抓图打包。

从时间分布看，7/27（26 会话）、7/30（24）、7/29（17）、7/31（15）是密集日，8/01~8/02 基本停了。

### 三、项目进展

两个 Project 的 checklist 本周**没有状态变化**，`projects.json` 的 `updated` 仍是 2026-07-12。

#### World Vibe GTM（active）

| 项 | 状态 |
|---|---|
| 第一 / 第二优先用户 | done |
| Demo seed：主角失踪 / 关系紧张的二人合作 | done |
| 决定首页主入口：Studio First vs Cohub First | **未完成** |
| 第一个 playable interactive story demo | **未完成** |
| Living World Page 最小信息结构 | **未完成** |
| 官网首屏文案和 CTA | **未完成** |
| Agent 发布和长期记忆工作流 | **未完成** |
| 可点开的 interactive story 纵向切片 | **未完成** |
| 从 Studio 沉淀 1 个可复用风格包 / 护栏规则到 GTM demo | **未完成** |

变化在于：本周 AVG-Build 管线已经能 build + publish 一个有立绘的可玩件，风格库 V4 也接近可以当风格包交付。「纵向切片」和「可复用风格包」这两项现在缺的不是能力，是把它们指向 GTM 的那一步。

#### SYD Lazarus Pit 网站系统（active）

| 项 | 状态 |
|---|---|
| 部署 / 首页 / 文章页 / llms·RSS·sitemap / Projects 页 | done |
| 支持每周自动整理项目进度 | done |
| 从 Cohub 输入 project 更新 checklist | **未完成** |
| Daily 断档排查：确认晚间 Space 扫描 cron 仍在写 daily.json | **未完成** |
| 把本周 Studio 成果回流至少 1 篇 notes | **未完成** |

最后一项要说清：本周确实新增了 2 篇，但都不是 Studio 成果回流。真正吃掉一半产能的 V4/V5 命名与校验方法，一个字都没写进站点。所以这项保持未完成。

Daily 断档已经确认不是偶发：最新条目 6/28，至今 5 周没写。

### 四、下周建议

1. **把风格库方法论写成 notes**。V4 命名规则、发布校验清单、预览资产审计标准——这是本周投入最大、复用价值最高的东西，只存在会话里等于没有。写完就能同时勾掉网站的「Studio 成果回流」和 GTM 的「可复用风格包」。
2. **用 AVG-Build 管线打 GTM 纵向切片**。管线已经跑通到 publish，Demo seed（演出前主角失踪）早就定了。把两者接上，那个卡了一个多月的 playable demo 就有解。
3. **修 Daily cron**。5 周断档，先确认任务还在不在、失败在哪一步。
4. **补周报漏账**。7/13~7/26 两周要么补一篇合并周报，要么确认周日那条 cron 为什么没触发。
5. **给 V5 定收口条件**。V4 刚发布校验完，V5 已在评审。先写下 V5 要解决 V4 的哪几个具体问题，避免版本无限往前滚。
