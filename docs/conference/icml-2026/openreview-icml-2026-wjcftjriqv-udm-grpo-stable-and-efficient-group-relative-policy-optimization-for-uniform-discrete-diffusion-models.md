---
title: "UDM-GRPO: Stable and Efficient Group Relative Policy Optimization for Uniform Discrete Diffusion Models"
title_zh: UDM-GRPO：针对均匀离散扩散模型的稳定高效组相对策略优化
authors: "Jiaqi Wang, Haoge Deng, Ting Pan, Yang Liu, Chengyuan Wang, Fan Zhang, Yonggang Qi, Xinlong Wang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/ed3289b3bbfc4f6c1a69cec7a50111a44c8fe066.pdf"
tags: ["query:diffusion-rl"]
score: 6.0
evidence: 首个将均匀离散扩散与RL通过GRPO集成的框架
tldr: 该论文针对将GRPO应用于均匀离散扩散模型（UDM）时的训练不稳定问题，提出UDM-GRPO框架。关键思想是将最终干净样本视为动作以获得稳定信号，并通过扩散前向过程重建轨迹以对齐预训练分布。引入的减少策略进一步提升了稳定性。实验表明UDM-GRPO在离散生成任务上优于直接应用GRPO。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 将RL与均匀离散扩散模型结合的方法缺失，直接应用GRPO导致训练不稳定。
method: 将最终干净样本作为动作，通过扩散前向过程重建轨迹，并引入减少策略来稳定GRPO训练。
result: 在离散生成基准上，UDM-GRPO相比基线实现了更稳定的训练和更好的生成性能。
conclusion: 该工作为离散扩散模型的RL优化提供了首个有效框架。
---

## Abstract
Uniform Discrete Diffusion Model (UDM) has recently emerged as a promising paradigm for discrete generative modeling; however, its integration with reinforcement learning remains largely unexplored. We observe that naively applying GRPO to UDM leads to training instability and marginal performance gains. To address this, we propose **UDM-GRPO**, the first framework to integrate UDM with RL. Our method is guided by two key insights: (i) treating the final clean sample as the action provides more accurate and stable optimization signals; and (ii) reconstructing trajectories via the diffusion forward process better aligns probability paths with the pretraining distribution. Additionally, we introduce two strategies, Reduced-Step and CFG-Free, to further improve training efficiency. **UDM-GRPO** significantly improves base model performance across multiple T2I tasks. Notably, GenEval accuracy improves from $69\\%$ to $96\\%$ and PickScore increases from $20.46$ to $23.81$, achieving state-of-the-art performance in both continuous and discrete settings. On the OCR benchmark, accuracy rises from $8\\%$ to $57\\%$, further validating the generalization ability of our method. Code is available at https://github.com/Yovecent/UDM-GRPO.

---

## 论文详细总结（自动生成）

# UDM-GRPO: 针对均匀离散扩散模型的稳定高效组相对策略优化

## 1. 核心问题与整体含义（研究动机和背景）
- **研究动机**：均匀离散扩散模型（UDM）在离散生成建模中展现出潜力，但其与强化学习（RL）的集成尚未被充分探索。直接应用现有的组相对策略优化（GRPO）到UDM会导致训练不稳定且性能提升有限。
- **整体含义**：本文旨在填补UDM与RL结合的空白，提出首个有效框架UDM-GRPO，通过重新设计动作定义和轨迹重建策略，实现稳定高效的RL优化，从而显著提升离散生成任务（如文本到图像生成）的性能。

## 2. 方法论：核心思想与关键技术细节
- **核心思想**：
  - 将最终干净样本（而非中间步骤）视为动作，提供更准确稳定的优化信号。
  - 通过扩散前向过程重建轨迹，更好地对齐预训练分布的概率路径。
- **关键技术细节**：
  - **动作定义**：将UDM生成过程中的最终干净样本作为RL中的动作，避免中间步骤噪声带来的不稳定。
  - **轨迹重建**：利用扩散模型的前向过程（加噪过程）从最终样本反向构造出完整轨迹，使RL轨迹的分布与预训练UDM的分布一致。
  - **减少步骤策略（Reduced-Step）**：缩短RL优化的轨迹长度，减少计算开销和方差。
  - **无CFG策略（CFG-Free）**：取消分类器自由引导（CFG），简化训练并提升稳定性。
- **算法流程**（文字说明）：
  1. 预训练UDM模型。
  2. 从UDM采样生成最终干净样本作为候选动作。
  3. 使用扩散前向过程重建从初始噪声到最终样本的完整轨迹。
  4. 基于GRPO对轨迹上的策略进行优化，奖励信号由外部评估器（如GenEval、PickScore）提供。
  5. 应用Reduced-Step和CFG-Free策略加速训练并保持稳定。

## 3. 实验设计
- **数据集/场景**：
  - Text-to-Image（T2I）任务：使用GenEval基准（评估生成图像与文本对齐的准确性）和PickScore（衡量图像偏好）。
  - OCR基准：评估生成图像中文本识别的准确性。
- **Benchmark**：对比方法包括基础UDM模型（无RL优化）、直接应用GRPO的方法，以及连续/离散设置下的SOTA模型。
- **对比方法**：本文未明确列出所有对比方法，但从性能数据可知，UDM-GRPO超越了基础UDM和连续/离散SOTA。

## 4. 资源与算力
- **文中未明确说明**：论文摘要未提及使用的GPU型号、数量、训练时长等算力信息。可能需要在完整论文中进行补充说明。

## 5. 实验数量与充分性
- **实验数量**：主要报告了三个基准的结果（GenEval、PickScore、OCR），每组实验均给出了性能提升数据。
- **充分性评估**：
  - 覆盖了主流T2I评估指标和可迁移的OCR任务，验证了泛化能力。
  - 但未提供消融实验的具体数量（如Reduced-Step和CFG-Free的单独效果），也未说明是否进行了多轮重复实验或统计显著性检验。
  - 实验整体具有一定代表性，但缺乏更细粒度的分析（如不同奖励函数、不同UDM骨干网络的影响）。

## 6. 主要结论与发现
- UDM-GRPO显著提升了基础UDM的性能：GenEval准确率从69%提升至96%，PickScore从20.46提升至23.81，在连续和离散设置下均达到SOTA。
- OCR基准准确率从8%提升至57%，验证了方法在细粒度生成任务上的泛化能力。
- 直接应用GRPO会导致训练不稳定且收益微薄，而本文的关键设计（最终样本作为动作、轨迹重建、减少步骤、无CFG）有效解决了这一问题。

## 7. 优点
- **方法创新**：首次将GRPO成功应用于均匀离散扩散模型，提出了针对离散生成特性的RL适配策略。
- **性能卓越**：在多个基准上取得大幅提升，达到SOTA。
- **通用性**：不仅适用于T2I任务，在OCR等离散生成任务上也有效，表明方法具有跨任务潜力。
- **稳定性**：通过动作定义和轨迹重建有效抑制了训练不稳定问题。

## 8. 不足与局限
- **实验覆盖有限**：仅报道了T2I和OCR两类任务，未在更多离散生成领域（如分子生成、文本生成）验证。
- **没有消融细节**：缺少对每个关键组件（如Reduced-Step、CFG-Free）的独立贡献分析，无法确认各模块的相对重要性。
- **算力成本未公开**：未提供训练所需资源，难以评估实际部署门槛。
- **偏差风险**：性能提升可能依赖于特定奖励函数（GenEval、PickScore），若奖励函数有偏差，可能放大模型偏见。
- **应用限制**：方法专门针对均匀离散扩散模型设计，对其他类型的离散扩散模型（如Masked Diffusion、Autoregressive Diffusion）的适用性未知。

（完）
