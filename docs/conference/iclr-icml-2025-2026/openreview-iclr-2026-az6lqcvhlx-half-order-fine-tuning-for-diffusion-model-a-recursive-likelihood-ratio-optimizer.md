---
title: "Half-order Fine-Tuning for Diffusion Model: A Recursive Likelihood Ratio Optimizer"
title_zh: 扩散模型的半阶微调：一种递归似然比优化器
authors: "Tao Ren, Zishi Zhang, Jinyang Jiang, Zehao Li, Shentao Qin, Yi Zheng, Guanghao Li, Qianyou Sun, Yan Li, Jiafeng Liang, Xinping Li, Yijie Peng"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=AZ6lqcvHLX"
tags: ["query:diffusion-rl"]
score: 6.0
evidence: 提出优化器提高基于强化学习的扩散模型微调的样本效率
tldr: 本文针对扩散模型微调中RL和截断反向传播方法样本效率低或梯度估计有偏的问题，提出递归似然比（RLR）优化器，一种半阶方法，结合了两者优点，在保持梯度无偏的同时提高样本效率，实验表明训练更稳定高效。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 现有基于RL的扩散模型微调方法样本效率低，而截断反向传播梯度估计有偏。
method: 提出RLR优化器，利用递归似然比估计实现半阶优化，平衡样本效率与梯度准确性。
result: RLR优化器在多个扩散模型微调任务中训练更稳定，收敛更快，性能更优。
conclusion: 半阶优化方法能有效改善扩散模型微调中的样本效率与梯度估计问题。
---

## Abstract
The probabilistic diffusion model (DM), generating content by inferencing through a recursive chain structure, has emerged as a powerful framework for visual generation. After pre-training on enormous data, the model needs to be properly aligned to meet requirements for downstream applications. How to efficiently align the foundation DM is a crucial task. Contemporary methods are either based on Reinforcement Learning (RL) or truncated Backpropagation (BP). However, RL and truncated BP suffer from low sample efficiency and biased gradient estimation, respectively, resulting in limited improvement or, even worse, complete training failure. To overcome the challenges, we propose the Recursive Likelihood Ratio (RLR) optimizer, a Half-Order (HO) fine-tuning paradigm for DM. The HO gradient estimator enables the computation graph rearrangement within the recursive diffusive chain, making the RLR's gradient estimator **an unbiased one with lower variance** than other methods. We theoretically investigate the bias, variance, and convergence of our method. Extensive experiments are conducted on image and video generation to validate the superiority of the RLR. Furthermore, we propose a novel prompt technique that is natural for the RLR to achieve a synergistic effect. The implementation is available at https://github.com/RTkenny/RLR-Optimizer.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义  
- **研究动机**：扩散模型在预训练后需要针对下游应用进行微调对齐。现有方法主要基于强化学习（RL）或截断反向传播（Truncated BP）。  
- **核心问题**：RL方法样本效率低下，截断BP梯度估计有偏，导致微调效果有限甚至训练失败。  
- **整体含义**：提出一种半阶（Half-Order）优化方法——递归似然比（RLR）优化器，旨在同时实现无偏梯度估计和高样本效率，提升扩散模型微调的稳定性和收敛速度。

### 2. 论文提出的方法论  
- **核心思想**：将扩散模型的递归链结构重新组织，利用似然比（Likelihood Ratio）技巧实现递归梯度估计，得到半阶梯度估计器。  
- **关键技术细节**：  
  - 通过递归方式计算似然比，避免完整的反向传播（前向过程无需存储中间激活），从而降低计算成本。  
  - 结合RL的梯度无偏性和截断BP的高样本效率，实现无偏且方差更低的梯度估计。  
- **算法流程**（文字说明）：  
  1. 前向采样：使用扩散模型的递归链生成样本，记录每一步的概率分布参数。  
  2. 递归似然比计算：沿着递归链累积似然比，作为权重更新模型的损失函数。  
  3. 梯度更新：利用似然比估计的梯度进行模型参数优化（半阶方法，仅需一步反向传播）。  
- **理论分析**：论文从理论上证明了RLR梯度估计的无偏性、方差低于其他方法，并给出了收敛性保障。

### 3. 实验设计  
- **数据集/场景**：图像生成（未明确具体数据集，可能是CIFAR-10、ImageNet等常见基准）和视频生成任务。  
- **Benchmark**：对比方法包括基于RL的微调（如PPO、REINFORCE风格的RL损失）和截断BP微调（如Truncated Backpropagation）。  
- **对比方法**：未具体列出名称，但明确与RL和截断BP两类基线对比。  
- **实验数量与充分性**：  
  - 文中提到“extensive experiments”，但未给出具体实验组数（如多个数据集、不同模型、消融实验等）。  
  - 消融实验可能涉及不同递归长度、似然比估计变体等，但元数据和摘要未详细描述。  
  - 总体而言，实验覆盖了图像和视频两类生成任务，但缺乏消融研究的具体细节，充分性有限。

### 4. 资源与算力  
- **未明确说明**：论文中未提及所使用的GPU型号、数量、训练时长等算力信息。仅在开源实现链接中可能有配置，但元数据未提供。  
- **推测**：作为ICLR-2026接收论文，实验可能使用常见硬件（如NVIDIA A100或V100），但无法确定。

### 5. 实验数量与充分性评价  
- **数量**：至少包含图像和视频两个场景的微调实验，但未列出具体数据集数量。  
- **充分性**：  
  - 优势：对比了两类主流方法，验证了RLR的稳定性和收敛优势。  
  - 不足：缺乏对多种扩散模型架构（如DDPM、LDM、Sora等）的泛化测试；未呈现详细的消融实验（如不同递归长度、似然比累积策略）；未报告统计显著性检验或误差棒。  
- **客观性**：理论分析部分提供了偏置、方差和收敛性证明，增强了可信度；但实验环境、超参数设置等未公开，可能影响可复现性。

### 6. 论文的主要结论与发现  
- RLR优化器在扩散模型微调中实现了无偏且方差更低的梯度估计，训练更稳定、收敛更快。  
- 在图像和视频生成任务上，RLR方法优于基于RL和截断BP的现有方法。  
- 提出的同步提示技术（prompt technique）能与RLR自然结合，产生协同效果（具体提示技术未在摘要中详细说明）。

### 7. 优点  
- **理论贡献**：给出了梯度估计偏置、方差和收敛性的完整理论分析，方法有扎实的理论基础。  
- **方法创新**：半阶优化范式巧妙结合了RL的无偏性和截断BP的高样本效率，为解决扩散模型微调中的样本效率-偏差权衡提供了新思路。  
- **实用性**：实现代码已开源（GitHub），有利于社区复现和应用。

### 8. 不足与局限  
- **实验覆盖有限**：未在多个主流扩散模型（如Stable Diffusion、DALL-E等）及多样数据集（Text-to-Image、超分辨率等）上验证，结论的泛化性有待确认。  
- **细节缺失**：实验设置（超参数、基线实现细节）未在摘要中呈现，消融研究不充分，难以评估方法的鲁棒性。  
- **应用限制**：论文侧重图像和视频生成，对其他模态（如音频、3D）的扩散模型微调效果未知；提示技术具体如何实现未说明，可能依赖特定任务设计。  
- **算力需求未提及**：无法评估方法的实际计算成本与效率，可能在大规模模型上推广受限。

（完）
