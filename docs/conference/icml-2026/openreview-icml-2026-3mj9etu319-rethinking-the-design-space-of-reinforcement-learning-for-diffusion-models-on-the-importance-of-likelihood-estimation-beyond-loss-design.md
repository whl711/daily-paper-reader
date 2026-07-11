---
title: "Rethinking the Design Space of Reinforcement Learning for Diffusion Models: On the Importance of Likelihood Estimation Beyond Loss Design"
title_zh: 重新思考扩散模型强化学习的设计空间：似然估计在损失设计之外的重要性
authors: "Jaemoo Choi, Yuchen Zhu, Wei Guo, Petr Molodyk, Bo Yuan, Jinbin Bai, Yi Xin, Molei Tao, Yongxin Chen"
date: 2026-04-30
pdf: "https://openreview.net/pdf/5f18c7399448fd0b9ef9aebe19679073be540dad.pdf"
tags: ["query:diffusion-rl"]
score: 7.0
evidence: 扩散模型强化学习设计空间的系统分析
tldr: 强化学习在扩散模型中的应用面临不可计算似然的挑战。本文通过解耦策略梯度目标、似然估计和算法设计三个因素，系统分析了RL设计空间，揭示了似然估计对整体性能的关键影响，为扩散模型RL方法的设计提供了指导。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散模型RL中似然估计方法缺乏系统研究。
method: 系统分析了策略梯度目标、似然估计和算法设计三个因素的影响。
result: 发现似然估计是影响性能的关键因素。
conclusion: 为扩散模型的RL应用提供了设计指南。
---

## Abstract
Reinforcement learning has been widely applied to diffusion and flow models for visual tasks such as text-to-image generation. However, these tasks remain challenging because diffusion models have intractable likelihoods, which creates a barrier for directly applying popular policy-gradient type methods. Existing approaches primarily focus on crafting new objectives built on already heavily engineered LLM objectives, using ad hoc estimators for likelihood, without a thorough investigation into how such estimation affects overall algorithmic performance.  In this work, we provide a systematic analysis of the RL design space by disentangling three factors: i) policy-gradient objectives, ii) likelihood estimators, and iii) rollout sampling schemes. We show that adopting an evidence lower bound (ELBO) based model likelihood estimator, computed only from the final generated sample, is the dominant factor enabling effective, efficient, and stable RL optimization, outweighing the impact of the specific policy-gradient loss functional. We validate our findings across multiple reward benchmarks using SD 3.5 Medium, and observe consistent trends across all tasks. Our method improves the GenEval score from $0.24$ to $0.95$ in $90$ GPU hours, which is $4.6\times$ more efficient than FlowGRPO and $2\times$ more efficient than the SOTA method DiffusionNFT without reward hacking.

---

## 论文详细总结（自动生成）

# 中文总结

## 1. 核心问题与整体含义（研究动机与背景）

- 强化学习（RL）已被广泛用于扩散模型和流模型的视觉任务（如文生图），但扩散模型的似然函数不可计算，给直接应用策略梯度类方法带来障碍。
- 现有方法主要聚焦于在已有语言模型RL目标基础上设计新损失函数，采用启发式的似然估计，但缺乏对似然估计如何影响整体算法性能的深入探究。
- 本文的核心动机是**系统分析扩散模型RL的设计空间**，揭示除损失函数设计外，似然估计方法才是决定性能的关键因素。

## 2. 提出的方法论

- **核心思想**：将扩散模型RL设计空间解耦为三个独立因素：
  1. **策略梯度目标**（policy-gradient objectives）：如REINFORCE、PPO等不同损失函数形式。
  2. **似然估计器**（likelihood estimators）：如何近似扩散模型的不可计算似然，例如基于证据下界（ELBO）的估计。
  3. **滚动采样方案**（rollout sampling schemes）：从扩散模型中采样生成样本的策略（如单步、多步等）。
- **关键技术细节**：
  - 采用**基于ELBO的模型似然估计器**，仅利用最终生成样本计算，无需中间状态信息。
  - 实验表明，该似然估计器是**主导因素**，其重要性超过策略梯度损失函数的具体设计。
- **算法流程（文字说明）**：  
  1. 从扩散模型采样生成图像（使用特定的rollout采样方案）。  
  2. 计算生成样本的奖励（如GenEval评分）。  
  3. 使用基于ELBO的似然估计器近似对数概率。  
  4. 将奖励和似然估计结合到策略梯度目标中更新模型参数。

## 3. 实验设计

- **使用的模型/场景**：基于 **SD 3.5 Medium** 作为基础扩散模型，在多个奖励基准上进行验证。
- **Benchmark**：主要报告了 **GenEval** 评分（从0.24提升至0.95）。其他reward benchmark未在摘要中明确列出，但提及“across multiple reward benchmarks”。
- **对比方法**：
  - **FlowGRPO**（基于流匹配的GRPO方法）
  - **DiffusionNFT**（当前SOTA方法，无奖励黑客问题）
  - 本文方法相对FlowGRPO效率提升4.6倍，相对DiffusionNFT效率提升2倍。

## 4. 资源与算力

- 文中明确提到总训练时间为 **90 GPU hours**（在SD 3.5 Medium上完成）。
- **未明确说明** GPU的型号、数量以及具体训练配置（如batch size等）。建议读者参考全文补充细节。

## 5. 实验数量与充分性

- 实验覆盖了**多个奖励基准**（未给出具体数目），并验证了**一致的性能趋势**。
- 通过**解耦三个因素**，隐含了消融实验的设计（如比较不同似然估计器、不同策略梯度目标、不同采样方案）。
- **充分性评价**：实验设计系统，但仅以SD 3.5 Medium为基座模型，未在更大规模或不同架构的扩散模型上验证，可能影响泛化结论。总体上实验客观，对比方法先进，但数量上不够全面（例如未报告统计显著性或多次重复实验）。

## 6. 主要结论与发现

- **似然估计是扩散模型RL优化的关键因素**，其影响力远超策略梯度损失函数的具体选择。
- 采用**ELBO-based似然估计器**能够实现**有效、高效、稳定**的RL优化，避免奖励黑客现象。
- 方法在GenEval上从0.24提升至0.95，仅用90 GPU小时，获得显著性能提升和计算效率优势。

## 7. 优点（方法与实验设计亮点）

- **系统化拆解设计空间**：首次明确解耦策略梯度目标、似然估计器和采样方案，为后续研究提供清晰分析框架。
- **发现普适性规律**：指出似然估计器比损失函数设计更重要，纠正了领域内过度关注损失函数设计的倾向。
- **高效且实用**：仅需最终样本计算ELBO，训练效率高，且结果可复现性强（基于标准SD模型）。
- **对比指标全面**：与多种先进方法比较效率和性能，且强调无奖励黑客现象。

## 8. 不足与局限

- **实验覆盖面有限**：仅以单一模型（SD 3.5 Medium）进行测试，未在多种扩散模型（如更大的SDXL、FLUX等）或不同流模型上验证，泛化性存疑。
- **似然估计器依赖ELBO**：ELBO本身是近似值，可能存在偏差，尤其对于高维复杂分布；未探讨其他似然估计方法（如重要性采样、MCMC）的对比。
- **未提供统计差异**：未提及多次重复实验的标准差或置信区间，无法判断结果的稳定性。
- **应用限制**：方法主要针对文生图任务，是否适用于其他模态（如视频、音频）的扩散模型需进一步验证。

（完）
