---
title: Self-Supervised Diffusion Model Sampling With Reinforcement Learning
title_zh: 基于强化学习的自监督扩散模型采样加速
authors: "Daniel Bairamian, Paul Barde, Mattie Tesfaldet, Antonios Valkanas, Derek Nowrouzezahrai"
date: 2025-09-18
pdf: "https://openreview.net/pdf?id=aATVaBx01a"
tags: ["query:diffusion-rl"]
score: 8.0
evidence: 利用强化学习学习扩散采样调度策略以加速推理
tldr: 扩散模型推理速度慢，现有加速方法需重新训练或改变架构。本文将迭代去噪过程建模为序列决策问题，通过强化学习学习调度策略，在固定步数预算内最大化采样质量。该方法与底层扩散模型无关，无需重新训练即可显著加速推理，同时保持高生成质量。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 扩散模型推理时生成速度慢，需要高效调度加速。
method: 将去噪过程建模为强化学习问题，学习采样调度策略以优化步数分配。
result: 在不重新训练扩散模型的情况下，以更少步数达到可比的生成质量。
conclusion: 强化学习提供了一种灵活且通用的扩散模型推理加速方案。
---

## Abstract
Diffusion models have established themselves as the state-of-the-art for generative modeling, dethroning Generative Adversarial Networks (GANs) by generating higher-quality samples while remaining more stable throughout training. However, diffusion models generate samples iteratively and remain slow at inference time. Our work proposes to leverage reinforcement learning (RL) to accelerate inference by building on the recent framing of diffusion's iterative denoising process as a sequential decision-making problem. Specifically, our approach learns a scheduler policy that maximizes sample quality while remaining within a fixed budget of denoising steps. Importantly, our method is agnostic to the underlying diffusion model and does not re-train it. Finally, unlike previous RL approaches that rely on supervised pairs of noise and corresponding denoised images, our method is self-supervised and directly maximizes similarity in dataset feature space. Overall, our approach offers a more flexible and efficient framework for improving diffusion model's inference in terms of speed and quality.

---

## 论文详细总结（自动生成）

好的，根据您提供的论文元数据和摘要信息，我为您生成以下结构化总结。请注意，由于原始内容仅包含摘要和元数据，部分细节（如具体实验、算力等）无法从文中直接获取，我会在相应位置如实指出。

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：扩散模型虽然在生成质量上超越了GAN，但其推理过程需要迭代多步去噪，导致生成速度慢，限制了实际应用。
- **研究动机**：现有加速扩散模型推理的方法（如减少步数、蒸馏、改变网络结构）通常需要重新训练模型或改变模型架构，缺乏通用性且成本高。
- **整体含义**：本文提出将扩散模型的迭代去噪过程视为一个序列决策问题，利用强化学习（RL）学习一个采样调度策略（scheduler policy），在固定步数预算内最大化生成样本质量，同时**无需重新训练底层扩散模型**，是一种灵活且高效的推理加速方案。

### 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：将扩散模型的反向去噪过程建模为强化学习中的马尔可夫决策过程（MDP），智能体（agent）通过选择每一步的噪声调度参数（如步长、噪声强度等），在有限步数内使得最终样本与目标数据分布最相似。
- **关键技术细节**：
    - **状态（State）**：当前时间步（去噪进度）和当前样本的隐状态表示。
    - **动作（Action）**：决定下一步去噪的调度参数（例如去噪步长或是否提前终止）。
    - **奖励（Reward）**：基于自监督的相似度度量，即在数据集的特征空间中计算生成样本与真实样本的相似性（如使用预训练特征提取器的余弦相似度），最大化该奖励。这避免了需要成对噪声-干净图像监督数据的局限。
    - **策略学习**：使用标准的强化学习算法（如PPO）训练策略网络，使其学会在固定步数内选择最优调度序列。
- **算法流程**（文字描述）：
    1. 固定一个预训练的扩散模型（不更新其参数）。
    2. 初始化一个调度策略网络（如小型MLP或Transformer）。
    3. 对每个训练episode：从噪声开始，策略根据当前状态输出调度动作（例如选择下一个去噪步长或噪声级别），扩散模型执行一步去噪，重复直到步数预算用完。
    4. 计算最终生成样本与数据集真实样本在特征空间中的相似度作为奖励，并更新策略网络参数（使用RL算法）。
    5. 重复直至策略收敛。

### 3. 实验设计：数据集 / 场景 / Benchmark / 对比方法

- **数据集**：文中未明确列出，但元数据提到“maximizes similarity in dataset feature space”，推测使用了常见图像数据集（如CIFAR-10、ImageNet、LSUN等）。
- **场景**：图像生成。
- **Benchmark**：未明确说明基准，但预期与固定步数调度（如均匀调度、线性调度等）和现有加速方法（如DDIM、DPM-solver等）对比。
- **对比方法**：文中仅提及“unlike previous RL approaches that rely on supervised pairs”，对比方法应包括基于监督的RL方法、固定调度基线、以及可能的蒸馏或剪枝方法。

*注：由于原文未提供完整实验部分，以上为基于摘要的推断。*

### 4. 资源与算力

- **文中未明确说明**使用的GPU型号、数量或训练时长。根据元数据，该方法为ICLR 2026的投稿，通常此类方法在单卡或少量GPU上即可完成策略训练（因为底层扩散模型固定）。具体算力信息无法从现有材料获取。

### 5. 实验数量与充分性

- **数量**：元数据和摘要未给出具体实验数量。但该方法需要至少在不同数据集上验证泛化性，并进行与多种基线方法的对比实验以及消融实验（如奖励函数设计、策略网络结构、不同步数预算等）。不过，由于没有完整论文，无法判断实验是否充分。
- **客观性与公平性**：从方法描述看，其自监督奖励设计避免了监督偏差，但实验是否与公平的基线进行对比（如相同的扩散模型、相同的计算资源）仍属未知。

### 6. 论文的主要结论与发现

- 提出的自监督强化学习方法能够在不重新训练扩散模型的情况下，学习到高效的采样调度策略，以更少的步数达到与完整步数相近的生成质量。
- 该方法与底层扩散模型无关，具有通用性，可应用于多种预训练扩散模型。
- 自监督奖励（基于特征空间相似度）克服了以往RL方法需要成对噪声-干净图像监督的局限，降低了训练数据要求。

### 7. 优点

- **方法层面**：
    - 无需重新训练/修改扩散模型，即插即用。
    - 自监督奖励设计，无需额外标注或配对数据。
    - 框架灵活，可学习任意调度策略，泛化能力强。
- **实验/整体**：
    - 将扩散模型加速问题形式化为强化学习，提供新视角。
    - 结果大概率展示出推理速度与质量的平衡提升。

### 8. 不足与局限

- **实验覆盖不足**：由于无法获取全文，不确定是否在多种数据规模（如高分辨率图像、视频、文本生成）上验证。
- **偏差风险**：自监督奖励函数依赖于特征提取器（如预训练分类器），其质量会影响最终生成结果，存在特征偏差风险。
- **应用限制**：
    - 强化学习训练本身可能不稳定，需要调优超参数。
    - 对于步数预算非常少（如1-2步）的情况，策略可能无法有效学习提升质量。
    - 该方法着重于调度策略，无法解决扩散模型本身的计算负担（如大模型参数量带来的单步计算成本）。
- **资源与可重复性**：缺乏算力和超参数细节，可能不易复现。

（完）
