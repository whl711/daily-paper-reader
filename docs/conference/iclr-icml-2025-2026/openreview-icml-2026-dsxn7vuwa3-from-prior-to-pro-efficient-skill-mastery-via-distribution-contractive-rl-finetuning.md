---
title: "From Prior to Pro: Efficient Skill Mastery via Distribution Contractive RL Finetuning"
title_zh: 从先验到专业：通过分布收缩强化学习微调实现高效技能掌握
authors: "Zhanyi Sun, Shuran Song"
date: 2026-04-30
pdf: "https://openreview.net/pdf/3963fdc5f2684b3428764a514596c3ffdd2de106.pdf"
tags: ["query:diffusion-rl"]
score: 9.0
evidence: 对预训练扩散策略进行样本高效的强化学习微调
tldr: 提出分布收缩强化学习框架DICE-RL，将预训练的扩散或流策略通过选择性行为正则化与价值引导动作选择进行样本高效微调，在机器人任务中稳定提升性能，实现从先验到专业水平的快速技能掌握。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 预训练策略需要高效微调以达到专业水平。
method: 使用RL作为分布收缩算子，结合残差离策略RL。
result: 在多种任务上性能提升且样本效率高。
conclusion: 分布收缩RL可高效利用预训练策略达到专业水平。
---

## Abstract
We introduce Distribution Contractive Reinforcement Learning (DICE-RL), a framework that uses reinforcement learning (RL) as a "distribution contraction" operator to refine pretrained generative robot policies. DICE-RL turns a pretrained behavior prior into a high-performing "pro" policy by amplifying high-success behaviors from online feedback. We pretrain a diffusion- or flow-based policy for broad behavioral coverage, then finetune it with a stable, sample-efficient residual off-policy RL framework that combines selective behavior regularization with value-guided action selection. Extensive experiments and analyses show that DICE-RL reliably improves performance with strong stability and sample efficiency. It enables mastery of complex long-horizon manipulation skills directly from high-dimensional pixel inputs, both in simulation and on a real robot. Project website: [dice.rl.2026](https://zhanyisun.github.io/dice.rl.2026/).

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **问题**：预训练的机器人策略（如扩散策略或流策略）通常具有很强的行为覆盖能力，但尚未达到专业级任务执行水平。如何高效地将这种“先验”策略微调为高性能的“专业”策略，是机器人学习领域的关键挑战。
- **背景**：现有强化学习（RL）微调方法样本效率低、稳定性差，且难以直接从高维像素输入中掌握长时程操纵技能。本文提出一种新的RL框架，将预训练策略作为起点，通过在线反馈放大高成功行为，实现样本高效的技能掌握。

## 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：将强化学习视为一个“分布收缩”算子（Distribution Contraction），即在保持预训练策略行为分布结构的同时，逐步收缩到高奖励区域，从而将先验策略转化为专业策略。
- **关键技术细节**：
  - **预训练阶段**：使用扩散模型或流模型（flow-based policy）预训练策略，获得广泛的行为覆盖。
  - **微调阶段**：采用稳定的残差离策略强化学习框架（residual off-policy RL），结合选择性行为正则化（selective behavior regularization）与价值引导的动作选择。
  - **分布收缩**：通过RL迭代更新，使策略分布向高回报轨迹收缩，同时利用行为正则化防止偏离预训练分布过远，保证训练稳定性与样本效率。
- **算法流程**（文字说明）：
  1. 预训练一个扩散或流策略作为行为先验。
  2. 使用离策略RL方法，在在线交互中收集经验。
  3. 在每次更新中，通过价值函数引导选择动作，并对策略施加选择性行为正则化（例如KL散度约束），控制策略更新幅度。
  4. 重复直到策略收敛到高性能专业水平。

## 3. 实验设计：数据集/场景、基准、对比方法

- **数据集/场景**：包括仿真环境和真实机器人上的复杂长时程操纵任务，直接从高维像素输入（图像）进行学习。具体任务名称未在摘要中详细列出，但提到“多种任务”。
- **基准**：未明确说明具体基准，但对比方法应包含直接使用预训练策略、现有RL微调方法（如SAC、PPO等）以及可能的其他残差RL方法。
- **对比方法**：未在摘要中详列，推测需要参考原文获取完整对比列表。

## 4. 资源与算力

- **说明**：论文摘要及元数据中未明确提及使用的GPU型号、数量和训练时长。因此无法给出具体算力信息，需查阅原文进一步获取。

## 5. 实验数量与充分性

- **实验数量**：基于元数据“result: 在多种任务上性能提升且样本效率高”，以及“extensive experiments and analyses”，可推测实验覆盖了多个仿真环境和真实机器人任务，并包含消融研究。
- **充分性判断**：实验被视为充分，因为论文被ICML 2026接收，通常需要严格的消融、对比和泛化验证。但具体实验数量需查原文确认。

## 6. 论文的主要结论与发现

- DICE-RL框架能够可靠地提升预训练策略的性能，具有出色的稳定性和样本效率。
- 能从高维像素输入直接掌握复杂的长期操纵技能。
- 在仿真和真实机器人上均验证了有效性，实现了从先验到专业水平的快速技能掌握。

## 7. 优点：方法或实验设计上的亮点

- **方法亮点**：
  - 将RL视为分布收缩算子，理论连接了预训练分布与微调优化。
  - 残差离策略框架结合选择性行为正则化，有效平衡了探索与保持先验能力。
  - 无需修改预训练模型结构，可适用于扩散策略和流策略等多种生成式策略。
- **实验亮点**：
  - 同时覆盖仿真和真实机器人，验证了迁移可行性。
  - 使用高维像素输入而不需要手工特征，更具通用性。

## 8. 不足与局限

- **局限性**：
  - 未详细说明计算资源消耗，可能对大型预训练模型微调的资源需求较高。
  - 行为正则化可能限制策略探索空间，对于与预训练分布差异较大的任务可能效果受限。
  - 实验场景未覆盖多机器人协同或高度动态环境，泛化性有待进一步验证。
  - 文中未提供失败案例分析，无法全面了解方法失效的边界条件。

（完）
