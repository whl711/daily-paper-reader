---
title: "TreeGRPO: Tree-Advantage GRPO for Online RL Post-Training of Diffusion Models"
title_zh: TreeGRPO：用于扩散模型在线RL后训练的树优势GRPO
authors: "Zheng Ding, Weirui Ye"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=3rZdp4TmUb"
tags: ["query:diffusion-rl"]
score: 8.0
evidence: 树结构扩散模型后训练提升样本效率
tldr: 针对扩散模型后训练计算成本高的问题，本文提出TreeGRPO。将去噪过程重构为搜索树，从共享噪声分支生成多条轨迹并重用公共前缀。通过奖励反向传播实现细粒度信用分配。实验在文本到图像生成任务上以更少样本达到更优对齐性能。为扩散模型的高效RL微调提供了轻量化方案。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散模型RL后训练计算开销大，现有方法样本利用率低。
method: TreeGRPO将去噪过程树状展开，共享前缀并计算树优势。
result: 在相同训练样本下，对齐性能优于基线，样本效率显著提升。
conclusion: 树结构可高效利用样本进行扩散模型RL后训练。
---

## Abstract
Reinforcement learning (RL) post-training is crucial for aligning generative models with human preferences, but its prohibitive computational cost remains a major barrier to widespread adoption. We introduce **TreeGRPO**, a novel RL framework that dramatically improves training efficiency by recasting the denoising process as a search tree. From shared initial noise samples, TreeGRPO strategically branches to generate multiple candidate trajectories while efficiently reusing their common prefixes. This tree-structured approach delivers three key advantages: (1) *High sample efficiency*, achieving better performance under same training samples (2) *Fine-grained credit assignment* via reward backpropagation that computes step-specific advantages, overcoming the uniform credit assignment limitation of trajectory-based methods, and (3) *Amortized computation* where multi-child branching enables multiple policy updates per forward pass. Extensive experiments on both diffusion and flow-based models demonstrate that TreeGRPO achieves **2.4$\times$** faster training} while establishing a superior Pareto frontier in the efficiency-reward trade-off space. Our method consistently outperforms GRPO baselines across multiple benchmarks and reward models, providing a scalable and effective pathway for RL-based visual generative model alignment. The project website is available at https://treegrpo.github.io.

---

## 论文详细总结（自动生成）

# TreeGRPO：用于扩散模型在线RL后训练的树优势GRPO - 论文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **背景**：强化学习（RL）后训练对于将生成模型（如扩散模型）与人类偏好对齐至关重要，但高昂的计算成本阻碍了其广泛应用。现有的轨迹级方法（如GRPO）存在样本利用率低、信用分配均匀（无法区分不同去噪步骤的贡献）等问题。
- **核心问题**：如何在不显著增加计算开销的前提下，提高扩散模型RL后训练的样本效率和训练速度，实现更好的对齐性能。
- **整体含义**：提出一种基于树结构的RL框架TreeGRPO，通过重构去噪过程为搜索树，利用前缀重用和细粒度信用分配，大幅提升训练效率，为扩散模型的RL微调提供轻量化、可扩展的方案。

## 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：将扩散模型的去噪过程（从随机噪声到图像的逐步生成）重构为一棵搜索树：从共享的初始噪声样本开始，策略性地分支生成多条候选轨迹，并高效重用其公共前缀（即前几步去噪步骤）。
- **关键技术细节**：
  - **树结构采样**：在去噪过程中，从同一个噪声样本出发，生成多个分支，每条分支代表一条完整的去噪轨迹。共享前缀意味着前几步的计算结果可在多分支间复用，减少冗余计算。
  - **细粒度信用分配**：通过奖励反向传播，计算每个步骤（step）的特有优势值（advantage），而非像传统轨迹方法那样对整个轨迹赋予相同信用。这使得模型能够更精确地调整那些对最终奖励贡献大的步骤。
  - **分摊计算（Amortized computation）**：在每轮前向传播中，多子分支（multi-child branching）结构使得一次前向可以支持多次策略更新（即多个分支的梯度更新可并行或顺序进行），进一步加速训练。
- **算法流程（文字说明）**：
  1. 从噪声分布中采样一个初始噪声（根节点）。
  2. 从根节点开始去噪，每步可选择分裂为多个子分支，生成多个候选轨迹。公共前缀部分只计算一次，分支后各自独立完成去噪。
  3. 对每条完整轨迹计算奖励（如CLIP分数、美观度评分等）。
  4. 将奖励从叶子节点向上回传，计算出每个去噪步骤的树优势（Tree Advantage），反映该步骤对最终奖励的边际贡献。
  5. 使用类似GRPO的优化目标，结合树优势更新策略参数（扩散模型）。
  6. 重复以上过程直到收敛。

## 3. 实验设计：使用了哪些数据集/场景，benchmark，对比方法

- **数据集/场景**：文本到图像生成任务（text-to-image generation），未在摘要中具体说明数据集名称，可能使用常见基准如COCO、PartiPrompts等。
- **Benchmark**：包括多个基准测试（multiple benchmarks）和多种奖励模型（reward models），涵盖扩散模型（Diffusion Models）和基于流的模型（Flow-based Models）。
- **对比方法**：主要与GRPO（Group Relative Policy Optimization）基线进行对比，可能还包括其他RL后训练方法（如DDPO、DPOK等），但摘要仅明确提到GRPO。

## 4. 资源与算力

- 摘要中未明确说明GPU型号、数量、训练时长等详细算力信息。仅提到训练速度提升2.4倍，但未给出绝对数值。
- 需要指出：论文未提供具体的算力配置细节，可能因篇幅限制未在摘要展开。

## 5. 实验数量与充分性

- 实验数量：摘要仅概要提及在扩散和基于流模型上进行了大量实验，并对比了多个基准和奖励模型。未列出具体实验组数（如不同数据集、消融实验、参数敏感性等）。
- 充分性判断：从已有信息看，实验覆盖了不同类型的生成模型和多种奖励函数，对比了主流基线，具有一定充分性。但由于缺乏详细消融实验（如树分支数、优势计算变体等）的说明，无法完全评估实验的全面性和公平性。消融实验可能存在于全文。

## 6. 论文的主要结论与发现

- TreeGRPO 在相同的训练样本下，对齐性能（reward）优于GRPO基线。
- 训练速度提升2.4倍（在效率-奖励帕累托前沿上占据优势）。
- 通过树结构实现了高样本效率、细粒度信用分配和分摊计算，三个优势共同带来性能提升。
- 方法可同时适用于扩散模型和基于流的模型，具有一定的通用性。

## 7. 优点：方法或实验设计上的亮点

- **创新性**：将去噪过程构建为搜索树，利用前缀共享降低计算冗余，思想新颖且符合扩散模型的逐步生成特性。
- **信用分配**：突破传统轨迹级均匀分配限制，通过树优势实现步骤级精细调整，理论上更合理。
- **效率提升**：多分支结构使得一次前向可以支持多次策略更新，显著加速训练。
- **实验覆盖**：同时验证了扩散模型和流模型，增强了方法的泛化性。

## 8. 不足与局限

- **实验细节缺失**：摘要中未提供消融实验、分支数量对性能的影响、优势计算的具体实现等，限制了可复现性和深入理解。
- **算力信息不透明**：未给出具体GPU配置和训练时长，难以评估实际计算开销。
- **应用范围限制**：仅针对文本到图像生成，未探索其他模态（如视频、3D、分子）或不同任务（如文本到3D、扩散策略等）。树结构可能不适用于所有扩散模型变体（如连续时间扩散）。
- **对比基线有限**：仅提及与GRPO对比，未与其他先进的后训练方法（如DDPO、DPOK、DPO-based方法）进行系统性比较，可能缺乏公平性。
- **潜在偏差风险**：奖励模型本身可能存在偏见，树优势计算对奖励模型质量敏感，文中未讨论此偏差。

（完）
