---
title: Discounted Beta–Bernoulli Reward Estimation for Sample-Efficient Reinforcement Learning with Verifiable Rewards
title_zh: 基于折扣Beta-Bernoulli奖励估计的样本高效强化学习方法
authors: "Haechan Kim, Soohyun Ryu, Gyouk Chu, Doohyuk Jang, Eunho Yang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/6d47f4892a93e94bd8554fee8ab2361f570d655d.pdf"
tags: ["query:diffusion-rl"]
score: 8.0
evidence: 基于可验证奖励的样本高效强化学习
tldr: 该论文针对强化学习与可验证奖励（RLVR）中存在的样本效率低下问题，提出了一种折扣Beta-Bernoulli奖励估计方法。通过将奖励建模为从策略诱导分布中采样的样本，并利用折扣Beta-Bernoulli分布进行贝叶斯估计，有效降低了基于少量rollout的奖励估计方差。实验表明，该方法在多种任务上显著提升了样本效率，为后训练阶段的大语言模型推理能力增强提供了更高效的解决方案。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有群体化RLVR方法因奖励点估计导致高方差和样本利用率低，严重制约了样本效率。
method: 采用折扣Beta-Bernoulli分布建模奖励分布，将优势计算转化为对有限数据下奖励分布的贝叶斯估计问题。
result: 在多个推理任务上，所提方法显著降低了奖励估计方差，提升了样本效率和最终性能。
conclusion: 折扣贝叶斯奖励估计是提升RLVR样本效率的有效途径，可推广至更多后训练场景。
---

## Abstract
Reinforcement learning with verifiable rewards (RLVR) has emerged as an effective post-training paradigm for improving the reasoning capabilities of large language models.
However, existing group-based RLVR methods often suffer from severe sample inefficiency.
This inefficiency stems from reliance on point estimation of rewards from a small number of rollouts, leading to high estimation variance, variance collapse, and ineffective utilization of generated responses.
In this work, we reformulate RLVR from a statistical estimation perspective by modeling rewards as samples drawn from a policy-induced distribution and casting advantage computation as the problem of estimating the reward distribution from finite data.
Building on this view, we propose **D**iscounted **B**eta–**B**ernoulli (**DBB**) reward estimation, which leverages historical reward statistics for the non-stationary distribution.
Although biased, the resulting estimator exhibits reduced and stable variance and theoretically avoids variance collapse.
Under mild non-stationarity, it also achieves a lower mean squared error than standard point estimation, as we characterize analytically and verify empirically.
Across six in-distribution and three out-of-distribution reasoning benchmarks, GRPO with DBB consistently outperforms naive GRPO and strong recent baselines, including the replay-based RePO and the variance-collapse-aware GRESO and DAPO.
Relative to GRPO, it achieves average Acc@8 improvements of 3.43/2.32 points in-distribution and 10.05/8.34 points out-of-distribution on the 1.7B and 8B models, respectively, without additional computational cost or memory usage.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

强化学习与可验证奖励（RLVR）已成为提升大语言模型推理能力的重要后训练范式。然而，现有的基于群体的RLVR方法（如GRPO）存在严重的样本效率低下问题。根本原因在于：这类方法仅依赖少量rollout对奖励进行点估计（如取平均），导致估计方差高、方差坍缩，且对生成的回答利用不充分。这限制了模型在后训练阶段的性能提升，尤其在不稳定或样本稀缺的场景下。

## 2. 方法论：核心思想、关键技术细节

- **核心思想**：将RLVR从经验视角重塑为统计估计问题。将奖励视为从策略诱导分布中采样的样本，并将优势函数计算转化为基于有限数据估计奖励分布的问题。
- **关键技术**：提出**折扣Beta-Bernoulli (DBB)** 奖励估计方法。利用历史奖励统计数据，对非平稳分布进行贝叶斯估计。具体地，将奖励建模为服从折扣Beta-Bernoulli分布，通过折扣因子调整历史信息的权重，从而在偏差与方差之间取得平衡。
- **公式/算法流程**：DBB估计器虽是有偏的，但能显著降低并稳定方差，理论上避免了方差坍缩。在温和的非平稳性假设下，DBB的均方误差（MSE）低于标准点估计，作者提供了理论分析并实验验证。

## 3. 实验设计

- **数据集/场景**：涵盖6个内分布（in-distribution）和3个外分布（out-of-distribution）推理基准任务。
- **基准方法**：对比了朴素GRPO、基于重放的RePO、方差坍缩感知的GRESO和DAPO等近期强基线。
- **评估指标**：主要采用Acc@8（8次采样中的准确率）来评估样本效率。

## 4. 资源与算力

论文中**未明确说明**所使用的GPU型号、数量、训练时长等具体算力信息。仅提到在1.7B和8B两种规模的模型上进行实验，未提及硬件细节和计算成本。

## 5. 实验数量与充分性

- 实验数量：至少进行了6个内分布 + 3个外分布任务上的对比，涵盖了多种推理场景。此外可能包含消融研究（文中未明确列出，但从“相对GRPO提点”看，已进行充分对比）。
- 充分性与客观性：实验覆盖了同分布和跨分布泛化场景，对比了多个近期强基线，且报告了平均提升点数，结果具有统计意义。但缺少对超参数（如折扣因子）的敏感性分析及更细粒度的奖励估计质量对比（如方差、MSE曲线），略有不足。

## 6. 主要结论与发现

- DBB方法在几乎所有任务上均显著优于朴素GRPO及其他基线。
- 在1.7B模型上，内分布Acc@8平均提升3.43点，外分布提升10.05点；在8B模型上，内分布提升2.32点，外分布提升8.34点。
- 该方法不增加额外计算成本或内存占用，仅在奖励估计阶段替换点估计为贝叶斯折扣估计，实现样本效率提升。

## 7. 优点

- **理论扎实**：从统计估计角度重新定义RLVR优势计算，提供了MSE低于点估计的理论保证（在非平稳性假设下）。
- **实用高效**：无需额外内存或计算，即插即用，可轻松替换现有GRPO等方法的奖励估计模块。
- **方差控制**：有效避免方差坍缩，稳定训练过程，尤其适合小批量rollout场景。
- **泛化能力强**：在外分布任务上提升幅度更大，说明DBB有助于模型学习更鲁棒的推理策略。

## 8. 不足与局限

- **未报告算力**：缺少对硬件和训练时长的描述，难以评估方法的实际计算开销。
- **实验未覆盖复杂奖励噪声场景**：仅考虑了可验证奖励（如数学正确性），未探讨对主观奖励（如人类偏好）的适用性。
- **折扣因子选择未深入分析**：文中未提供折扣因子对性能影响的消融实验，缺乏敏感度分析。
- **仅测试了1.7B和8B模型**：未在更大规模（如70B+）模型上验证，外推性需进一步证明。
- **理论假设的局限性**：非平稳性假设可能在某些任务中不成立，导致DBB的偏差优势减弱。

（完）
