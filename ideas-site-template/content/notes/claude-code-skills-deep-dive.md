---
title: "Claude Code Skills 深度解析：Anthropic 内部几百个 Skill 的实战经验"
date: "2026-06-28"
category: "reads"
tags: ["Claude Code","Skills","AI 工程","Anthropic","Agent"]
summary: "Anthropic 官方博客首次系统披露 Skills 机制：Skill 是文件夹不是文件、description 只有 250 字符预算、验证类 Skill 回报最大、坑点清单是含金量最高的部分。7 个问题一次讲透。"
readMin: 8
source_url: "https://mp.weixin.qq.com/s/D0Pu6fm6EbC2EUy6CrPmlw"
source_title: "面试官皱眉：Claude Code 你用了半年，你懂 Skills 吗？"
source_author: "小林coding"
status: "published"
---

## {brief}

1. Skill 不是一份 Markdown 文件，是一个文件夹——除了 SKILL.md，还能放脚本、参考资料、输出模板，Claude 按需渐进式加载。
2. Anthropic 内部几百个 skill 自然聚成 9 类，其中验证类 skill 是实测对输出质量提升最明显的一类，值得专门花一周打磨。
3. Skill 不触发的根本原因：会话启动时只注入 description（且只有 250 字符预算），整个清单只占 context 窗口的 1%，装太多会互相挤压。
4. 一个 skill 里含金量最高的内容是「坑点清单」——Claude 靠读代码永远推断不出来、只有踩过坑的人才知道的事。
5. Skill 可以带记忆（持久化数据到 CLAUDE_PLUGIN_DATA）、喂脚本（预写函数库让 Claude 只管编排）、挂临时 hook（只在 skill 激活期间生效的危险操作拦截）。
6. 团队分发走「沙盒 → 口碑筛选 → marketplace」的自然演化路径，不做中心化审批。
7. 用 PreToolUse hook 做埋点，拉出「受欢迎 skill」和「触发不足 skill」，建设从凭感觉变成看数据。

## {full}

Anthropic 前几天发了一篇博客，把内部几百个活跃 skill 沉淀的经验一次性公开。小林coding 把这篇文章扒了一遍，整理成 7 个关键问题。下面逐条说。

### Q1：Skill 不是 Markdown 文件

这是官方点名的最常见误解。

Skill 的真正定义是一个文件夹。典型的目录结构：

```
deploy-service/
├── SKILL.md               # 唯一必需：何时用 + 操作指引 + 坑点
├── references/            # API 详细参数、排障手册
├── scripts/               # 冒烟测试、一键回滚
└── assets/                # 发布报告模板
```

Claude 不会一股脑读完所有文件，而是干活干到哪一步、需要什么材料，才自己翻什么。这套机制叫**渐进式披露**（Progressive Disclosure）：平时只有 description，调用时才加载 SKILL.md 全文，正文提到的参考文件等真需要时才读。

只写一份 Markdown 的 skill，只用了这个机制十分之一的能力。

### Q2：9 类 Skill 分类法

| 类别 | 做什么 |
|---|---|
| 库和 API 参考 | 教 Claude 正确使用内部库/CLI |
| **产品验证** | 让 Claude 自测代码（无头浏览器跑流程） |
| 数据查询分析 | 连接数据和监控系统 |
| 业务流程自动化 | 聚合工单/PR 生成日报 |
| 代码脚手架 | 按规范生成样板代码 |
| 代码质量与审查 | 子 agent 对抗式审查 |
| CI/CD 与部署 | 盯着 PR 重试 CI、解决冲突 |
| Runbook 排障手册 | 从报警出发做多工具排查 |
| 基础设施运维 | 带护栏的例行维护 |

**验证类 skill 是内部实测回报最大的一类。** 原话甚至说：值得专门让一个工程师花一整周打磨验证 skill。一个会自己验收的 Claude 和一个只会交作业的 Claude，干活质量是两个物种。

### Q3：为什么不触发？

机制比大多数人想的聪明。会话启动时，Claude Code 收集所有 skill 的 name + description 拼成清单注入 context。Claude 平时只看到这张「目录页」，判断匹配后才加载正文。

关键约束：
- 整张清单只占 context 窗口的 **1%**
- 单个 description 最多 **250 个字符**，超出直接截断成省略号
- 装太多 skill 挤爆预算 → 压缩 description → 最后只显示名字

源码在 `src/tools/SkillTool/prompt.ts`，两个常量 `SKILL_BUDGET_CONTEXT_PERCENT = 0.01` 和 `MAX_LISTING_DESC_CHARS = 250` 写死了这个限制。

所以 description 必须写成「触发条件」而非「给人看的摘要」。"帮助处理数据库相关工作"是人话；"当用户要写数据库迁移、修改表结构、或遇到 migration 报错时使用"才是模型能用的触发条件。

### Q4：坑点清单是含金量最高的内容

官方的黄金法则：**只写 Claude 推断不出来的，删掉它本来就会的。**

什么算坑点？两个真实例子：
- 「这个字段在 API 网关叫 @request_id，在计费服务叫 trace_id，它们是同一个值。」
- 「staging 环境就算 Stripe 回调没真正处理也会返回 200，真实状态要去 payment_events 表查。」

共同特征：Claude 靠读代码永远推断不出来，只有踩过坑的人才知道。坑点清单不是一次写完的——每次 Claude 用这个 skill 又栽新坑，就回头补进去，越用越准。

另一个反方向的坑：别把 Claude 锁死在轨道上。步骤写太死，遇到没覆盖的情况它就僵住。正确姿势是**给足信息，把自由留给它**。

### Q5：三个高阶玩法

**装记忆**：把执行结果存到 `CLAUDE_PLUGIN_DATA` 目录。日报 skill 每次追加记录，下次执行先读历史，自然知道只报增量。这个目录持久存在，升级不丢，卸载才清。

**喂脚本**：skill 里预放好函数库，取数、清洗、对比全部封装。Claude 每个回合只思考怎么组合函数，而不是重新发明轮子。

**挂临时 hook**：skill 激活时才注册、会话结束就失效的 hook。官方两个例子——`careful` skill 阻断 rm -rf 和 DROP TABLE，`freeze` skill 禁止修改指定目录之外的文件。直接在 frontmatter 声明就行。

### Q6：团队分发

两条路：小团队放 `.claude/skills` 随仓库分发；规模上来后做 plugin marketplace，谁用谁安装。

最妙的是审批模式——**不做中心化审批**。写好的 skill 扔进 GitHub 沙盒文件夹，Slack 里吆喝一声。口碑起来了，作者自己提 PR 挪进正式 marketplace。门槛越低，分享越多。依赖管理目前没有原生支持，但只要在 SKILL.md 正文里报另一个 skill 的名字，模型自己会去调。

### Q7：数据埋点

用 PreToolUse hook 监听 skill 调用，记录谁在什么时候用了哪个。数据拉出来，两类问题现形：受欢迎的 skill 重点维护，触发不足的 description 没写对，回头修。成本低到没有借口不做——一个 hook、一段日志，但把建设从凭感觉变成了看数据。

## {source}

> 他们内部最好的那批 skill，几乎都是从「几行字加一个坑点」开始的，然后随着 Claude 撞上一个个新的边界情况，被人一点点喂大。

> 官方给了一个判断标准：最好的 skill 干干净净落在某一类里；那些想一次干太多事、横跨好几类的 skill，反而会把 agent 搞糊涂。

> skill 的上限不是一份好文档，是一个带记忆、带工具、带保险丝的小型工作系统。

> 决定 skill 命运的是 description 的前 250 个字符。description 没写好，正文写出花来也是白搭。

## {mine}

这篇的核心价值在于把「怎么用好 Skills」从一个模糊的感觉变成了可操作、可度量的工程问题。

最震撼我的是两个数字：**1% 的 context 预算**和**250 字符的 description 上限**。这意味着 skill 设计不是「内容写得好就能用」，而是一个严格的资源分配问题——每个 skill 都在和其他 skill 抢那 1% 的注意力。装得越多，每个就越难被看到。这和收藏夹的原理一模一样：收藏越多，真正打开的概率越低。

另外，「坑点清单」这个概念很好。本质上它回答了一个通用问题：怎么给 AI 提供增量信息？答案不是教它已经会的事，而是提供只有特定上下文才有的隐性知识。这和带新人是一样的——最值钱的不是流程手册，而是老员工嘴里的「这个系统有个怪癖」。

验证类 skill 被官方推为最高 ROI，背后逻辑也很清晰：AI 写代码的能力已经够强了，真正的瓶颈变成了「它怎么确认自己写对了」。能自验收的 agent 和不能的，确实是两种生物。

**参考**：[Anthropic 博客原文](https://claude.com/blog/lessons-from-building-claude-code-how-we-use-skills) | [官方示例 skill 仓库](https://github.com/anthropics/skills)
