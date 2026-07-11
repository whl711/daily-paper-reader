---
title: "DF-ExpEnse: Diffusion Filtered Exploration for Sample Efficient Finetuning"
title_zh: DF-ExpEnse：用于样本高效微调的扩散滤波探索
authors: "Calvin Luo, Chen Sun, Shuran Song"
date: 2026-04-30
pdf: "https://openreview.net/pdf/68598525b0db388244d6d1b285fdcdb65973f1d1.pdf"
tags: ["query:diffusion-rl"]
score: 9.0
evidence: 扩散滤波探索用于样本高效微调
tldr: 机器人决策中初始预训练生成策略的在线微调面临样本效率挑战。DF-ExpEnse利用生成控制策略的多模态建模能力构建可评估候选集，再通过集成评论家选择平衡质量和探索兴趣的动作。在集群场景中进一步支持跨智能体通信协作探索，显著提升了微调样本效率。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 预训练策略在线微调时样本效率亟待提升。
method: 提出DF-ExpEnse，利用扩散策略生成候选动作集，集成评论家选择最优探索动作。
result: 在机器人操作任务中微调样本效率大幅提升。
conclusion: DF-ExpEnse为基于扩散的智能微调提供了高效的探索机制。
---

## Abstract
A natural recipe for intelligent robotic decision-making is initializing from pretrained generative control policies, which have summarized offline experience, and adapting them to self-collected online experience.  We present DF-ExpEnse, an exploration technique that improves the quality of online experience collection, thus increasing finetuning sample-efficiency.  DF-ExpEnse leverages the multimodal modeling capabilities of the generative control policy to create an expressive and tractably evaluatable candidate set.  It then utilizes an ensemble of critics to identify the action that best balances quality with high exploration interest.   In fleet settings, DF-ExpEnse further enables cross-agent communication to facilitate collaborative exploration as a group.  DF-ExpEnse can be seamlessly integrated with existing strategies that finetune pretrained generative control policies via reinforcement learning.  We experimentally validate consistent sample-efficiency benefits through DF-ExpEnse across a variety of manipulation and locomotion tasks, compared to default finetuning and alternative action selection schemes.  Project can be found at [df-expense.github.io](https://df-expense.github.io).

---

## 论文详细总结（自动生成）

# 论文中文总结：DF-ExpEnse: 用于样本高效微调的扩散滤波探索

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **核心问题**：在智能机器人决策中，一个自然的方法是先利用离线经验预训练生成式控制策略，再通过在线微调使其适应自收集的新经验。然而，在线微调过程中样本效率低下，如何高效地探索并收集高质量的在线经验是主要挑战。
- **整体含义**：提出一种名为 DF-ExpEnse 的探索技术，旨在提升在线经验收集的质量，从而显著提高微调阶段的样本效率，使预训练策略能更快适应新任务或新环境。

## 2. 论文提出的方法论
- **核心思想**：利用生成式控制策略（扩散策略）本身的多模态建模能力，构建一个表达能力强且易于评估的候选动作集；然后通过集成评论家（ensemble of critics）从候选集中选出既保持动作质量又能最大化探索兴趣的动作。
- **关键技术细节**：
  - **候选集构建**：扩散策略一次性生成多个候选动作（或通过采样生成），形成可并行评估的集合。
  - **集成评论家选择**：使用多个评论家网络对候选动作进行评分，综合考虑动作的价值（质量）和不确定性（探索兴趣），选择平衡两者的最优动作。
  - **集群协作探索**：在多智能体（fleet）场景下，支持跨智能体通信，实现群体协作探索，进一步加速经验收集。
- **集成方式**：DF-ExpEnse 可以无缝集成到现有基于强化学习微调预训练生成控制策略的框架中，无需修改底层算法。

## 3. 实验设计
- **任务场景**：在多种机器人操作（manipulation）和移动（locomotion）任务上进行验证。
- **Benchmark**：未明确指定具体的标准基准数据集，但实验对比了**默认微调**（即直接使用原始策略采样）和**替代动作选择方案**（可能是随机采样、仅基于价值等）。
- **对比方法**：默认微调、其他动作选择策略（如仅基于单一评论家、随机选择等）。未列出具体基线名称。

## 4. 资源与算力
- **文中未明确提及**：论文摘要及元数据中未说明使用的 GPU 型号、数量或训练时长等算力信息。无法判断计算开销。

## 5. 实验数量与充分性
- **实验数量**：摘要仅指出在“多种操作和移动任务”上进行了验证，但未给出具体任务数量、重复次数或消融实验细节。元数据中提到“在机器人操作任务中微调样本效率大幅提升”，但缺乏定量结果表格或曲线。
- **充分性评价**：实验覆盖了不同类别的任务，但缺乏消融研究（如候选集大小、集成数量、通信策略的影响）和与更多先进基线（如其他探索方法）的对比。因此实验的充分性和客观性有待论文全文补充。目前仅从摘要看，证据较弱。

## 6. 论文的主要结论与发现
- DF-ExpEnse 在多个机器人操作和移动任务上一致地提升了微调样本效率。
- 通过生成式扩散策略构建候选集并利用集成评论家进行探索选择，能有效平衡动作质量与探索兴趣。
- 在集群场景下，跨智能体协作探索进一步增强了探索效果。

## 7. 优点
- **方法创新**：巧妙地利用扩散策略的多模态生成能力构建候选集，并通过集成评论家实现高效的探索选择，无需额外模型或复杂采样。
- **通用性**：可无缝集成到任何基于强化学习微调预训练生成控制策略的框架中，适用范围广。
- **协同扩展**：在集群环境下支持通信协作，具备扩展到大范围机器人编队的潜力。
- **实验验证**：在操作和移动两类典型任务中验证了效果，表明方法具有一定通用性。

## 8. 不足与局限
- **实验细节缺失**：未提供具体任务列表、对比基线、收敛曲线、标准差等定量结果，使得结论的可信度难以评估。
- **消融分析不足**：未对候选集大小、评论家集成数量、通信方案等关键组件进行消融实验，无法确认各模块的实际贡献。
- **算力与时效性**：未报告计算资源消耗，实际应用中可能因生成大量候选动作而引入额外计算开销。
- **应用限制**：方法依赖预训练扩散策略的多模态能力，若预训练策略本身多样性不足，候选集质量可能受限；集群通信需额外基础设施支持。
- **偏差风险**：实验可能只在特定环境或任务上有效，泛化性未充分证明。

（完）
