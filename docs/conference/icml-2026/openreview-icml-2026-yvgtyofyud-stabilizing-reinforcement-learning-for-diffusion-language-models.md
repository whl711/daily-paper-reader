---
title: Stabilizing Reinforcement Learning for Diffusion Language Models
title_zh: 针对扩散语言模型的强化学习稳定性提升
authors: "Jianyuan Zhong, Kaibo Wang, Ding Ding, Zijin Feng, Haoli Bai, Yang Xiang, Jiacheng Sun, Qiang Xu"
date: 2026-04-30
pdf: "https://openreview.net/pdf/e29e9ce1b706c684af478b30bee1697562b6303f.pdf"
tags: ["query:diffusion-rl"]
score: 4.0
evidence: 解决扩散LLM中GRPO训练的不稳定问题
tldr: 该论文发现扩散大语言模型（dLLM）在GRPO训练中因重要性比率近似噪声导致严重不稳定性，形成自增强的不稳定循环。理论分析和实验证实了该问题，并提出了StableDRL方法，通过降噪技术稳定训练。实验表明StableDRL在多个推理任务上提升了dLLM的微调稳定性和性能。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散LLM在GRPO训练中表现出严重不稳定性，限制了RL对推理能力的提升。
method: 通过理论分析识别出不稳定性循环，提出StableDRL以降低重要性比率估计噪声。
result: 在推理基准上，StableDRL稳定了训练过程并提升了微调后的模型性能。
conclusion: 为扩散语言模型的RL训练稳定性提供了理论和实践解决途径。
---

## Abstract
Diffusion Large Language Models (dLLMs) often exhibit severe instability during Group Relative Policy Optimization (GRPO) training, limiting the effectiveness of reinforcement learning for improving reasoning capabilities. In dLLMs, the importance ratios used by GRPO are derived from finite-sample estimates rather than exact likelihoods, making them inherently noisy. In this paper, we show that GRPO is highly sensitive to this noise, which drives training instability. Through theoretical analysis and empirical evidence, we identify a self-reinforcing instability loop in which noisy importance ratios induce gradient spikes and policy drift, further amplifying future importance ratio estimation variance. To address this issue, we propose StableDRL, a novel reinforcement learning framework for dLLMs. StableDRL stabilizes training via (i) unconditional clipping to suppress outlier-induced gradient spikes, and (ii) self-normalization to constrain gradients within the convex hull of per-sample updates. We further extend StableDRL to block-wise diffusion models via a staircase attention mechanism. StableDRL is the first method that enables stable, full-parameter reinforcement learning for dLLMs. It achieves the state-of-the-art performance, outperforming prior best full-attention baselines by 6% on MATH500 and block-diffusion baselines by 25.6% on AIME.

---

## 论文详细总结（自动生成）

# 针对扩散语言模型的强化学习稳定性提升（StableDRL）——论文总结

## 1. 核心问题与整体含义（研究动机与背景）
- **问题**：扩散大语言模型（dLLMs）在使用组相对策略优化（GRPO）进行强化学习训练时，表现出严重的不稳定性，限制了RL对推理能力的提升效果。
- **根本原因**：dLLMs中重要性比率（importance ratio）来自有限样本估计而非精确似然，导致估计噪声；GRPO对此噪声高度敏感，引发训练不稳定。
- **自增强不稳定循环**：理论分析与实证表明，噪声的重要性比率引发梯度尖峰（gradient spikes）和策略漂移（policy drift），进而放大后续估计方差，形成恶性循环。
- **研究意义**：为扩散语言模型的RL训练稳定性提供理论分析与实用解决方案，推动dLLM在推理任务上的应用。

## 2. 方法论：核心思想、关键技术细节
- **核心思想**：通过降低重要性比率估计噪声的影响，打破自增强不稳定循环，实现稳定的全参数强化学习训练。
- **StableDRL框架**包含两项关键技术：
  1. **无条件裁剪（Unconditional Clipping）**：抑制异常值引起的梯度尖峰，避免单样本主导更新。
  2. **自归一化（Self-Normalization）**：将梯度约束在逐样本更新的凸包内，防止策略漂移。
- **扩展机制**：针对分块扩散模型（block-wise diffusion），引入**阶梯注意力机制（Staircase Attention）**，实现稳定训练。
- **算法流程**：在标准GRPO基础上，对重要性比率施加无条件裁剪，并对梯度进行自归一化处理；分块模型中结合阶梯注意力控制信息流。

## 3. 实验设计
- **数据集与基准**：主要在推理基准上进行评估，明确提及：
  - MATH500（数学推理）
  - AIME（高级数学竞赛）
- **对比方法**：
  - 全注意力基线（prior best full-attention baselines）
  - 分块扩散基线（block-diffusion baselines）
- **评价指标**：未见详细说明，但报告了相对提升百分比。

## 4. 资源与算力
- **文中未明确说明**使用的GPU型号、数量、训练时长等算力信息。
- 仅在元数据中标记为ICML-2026接收论文，但正文未提及任何计算资源细节。

## 5. 实验数量与充分性
- **实验数量**：从摘要中仅能得知两个主要数据集（MATH500, AIME）的对比结果；未提及消融实验数量或变量控制。
- **充分性**：
  - 缺少消融实验（如单独验证无条件裁剪、自归一化各自贡献）的详细描述。
  - 未报告多次重复实验的统计量（均值、方差）。
  - 仅给出单一相对提升数值，未提供绝对性能对比表。
  - 总体实验覆盖有限，客观性和公平性难以全面评估。

## 6. 主要结论与发现
- 理论识别了扩散LLM在GRPO中的自增强不稳定循环，并证明重要性比率噪声是根源。
- StableDRL首次实现了dLLM的全参数稳定强化学习训练。
- 在MATH500上超越先前最佳全注意力基线**6%**，在AIME上超越分块扩散基线**25.6%**，达到SOTA。

## 7. 优点
- **理论分析深入**：清晰揭示了噪声-梯度-方差的循环自增强机制。
- **方法简洁有效**：仅通过裁剪和归一化两项技术即可显著提升稳定性，易于实现。
- **首次实现全参数RL**：解决了dLLM无法稳定进行全参数RL训练的瓶颈。
- **扩展性**：阶梯注意力机制针对分块扩散模型，扩大了适用场景。

## 8. 不足与局限
- **实验报告不充分**：缺乏详细实验设置、超参数、重复次数、统计显著性检验。
- **算力信息缺失**：无法评估方法可复现性与资源成本。
- **数据集单一**：仅测试数学推理（MATH500, AIME），未覆盖其他下游任务（如常识推理、代码生成）。
- **对比基线有限**：未与不同RL算法（如PPO、DPO变体）或不同去噪策略对比。
- **可能存在偏差风险**：未见负结果或失败案例分析；未讨论泛化至更大规模模型的可能性。

（完）
