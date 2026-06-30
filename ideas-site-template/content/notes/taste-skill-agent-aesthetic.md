---
title: "Taste Skill: 前端接口 Agent 的美学判断层"
date: "2026-06-28"
category: "studio"
tags: ["Agent", "Taste", "美学", "Skill", "产品设计", "AI决策"]
summary: "在 Agent 决策前加一层美学包装——不是让 AI 替你选，而是让 AI 帮你穷举可能，你做最终的 taste 判断。调研了一轮之后发现 'taste is the new bottleneck' 是 2026 年 AI 设计界最热的话题。"
readMin: 5
status: "published"
---

## {brief}

- 灵感：前端接口 Agent 在决策前调用 taste skill，做一层美学包装。
- 2026 年 AI 设计界共识：taste 正在成为「新瓶颈」——执行变得廉价，判断变得昂贵。
- Steve Jobs、Paul Graham、OpenAI 的 Greg Brockman 都在说：AI 替代不了 taste。
- TASTE 数据集（2025）：设计师对 AI 生成的平面设计做了 9 个维度的评分，结果没有任何预训练模型能超过 0.55 的设计师一致率。
- 人类角色迁移：从「执行者」变成「框架制定者 + 美学判官」。
- 可以做的东西：Reference Curation（找参考、配对类比）、Aesthetic Vibe Check（生成后打分）、Style Decision Matrix（多方案用轴向打分法）。

## {full}

# Taste Skill: 前端接口 Agent 的美学包装层

灵感谢自一个很直接的需求：Agent 帮你做了决策，但出来东西丑或者气质不对，你要调好几轮。能不能在 Agent 决策前就加一层「taste check」？

## 调研发现：Taste Is the New Bottleneck

搜了一圈发现，2026 年 AI 设计界最热的话题就是这个。

**Steve Jobs 那句话被反复引用**：「Taste is what separates the great from the good.」

NYT、Inc、New Yorker 都在讨论同一件事：

> 当 AI 把执行成本打到几乎为零，人类的剩余价值从「能做」迁移到「会判断」。
> Taste 不是天赋，是**可训练的决策技能**。

Paul Graham（YC 创始人）说：「Taste will become even more important.」

创业者 Cong Wang 更直接：「In the AI era, personal taste is the moat.」

## 数据验证：AI 还做不到设计师级的判断

2025 年发布的 **TASTE 数据集**测了一件事：让 10 个专业设计师对 AI 生成的平面设计作品按 9 个维度打分（版式、审美、空间、调性等），然后拿 6 个开源视觉模型和 3 个专用评分模型去比。

结果：

> **没有任何一个模型的宏观一致率超过 0.55。**

也就是说，AI 能生成，但**判断不出哪个更好**。人类设计师的一致率远高于任何模型。

## 这和你的 Taste Skill 有什么关系

你提的「前端接口 Agent 在决策前调用 taste skill」本质上是：

```
用户意图 → Agent 决策 → [Taste Skill 美学判断] → 最终输出
```

不是让 AI 替你做审美决策，而是让 AI 做两件它擅长的事：

1. **穷举可能**——生成 3-5 个方案
2. **结构化展示**——用轴向（精致/粗犷、现代/古典、高对比/柔和）让用户选

人类做最后一步：**拍板**。

## 可以参考的 Taste Skill 设计

### 1. Reference Curation（参考策展）

- 输入：用户的模糊描述（「我想要一种冷一点、破一点、有点宿命感，但不要太中二」）
- 输出：3-5 个参考图 + 「你喜欢的可能是这一段」的解释
- 不做的事：直接生成，不做选择题

### 2. Aesthetic Vibe Check（美学打分）

- 输入：AI 生成的方案+A/B/C
- 输出：按维度拆分的评分矩阵（色彩统一性、视觉重量、风格一致性、情感传递）
- 不做的事：直接说「A 最好」

### 3. Style Decision Matrix（风格决策矩阵）

```
精致 ←→ 粗犷
温暖 ←→ 冷峻
秩序 ←→ 混乱
明亮 ←→ 晦暗
有机 ←→ 几何
```

用户在矩阵上滑动，AI 实时生成对应方向的参考。

核心原则：**AI 负责展开选项的宽度，人类负责收缩到最对的那一个。**

## {mine}

这其实跟 Studio 最早讨论的「Vibe vs World Builder」是一个问题在不同维度的表达：

- Vibe = 帮用户发现他的 taste position
- Taste Skill = 帮用户在决策时刻调用这个 position

如果把这个和之前的 Agentic 系统合在一起看，完整链路是：

```
Cohub 输入意图
  ↓
Agent 分析意图
  ↓
Taste Skill 展开美学选项
  ↓
用户拍板（唯一不可替代的环节）
  ↓
Agent 执行 + 发布到网站
```

Taste 不是「让 AI 变聪明」，是「让用户变快」——把判断从 10 轮对话压缩到 1 个决策矩阵。
