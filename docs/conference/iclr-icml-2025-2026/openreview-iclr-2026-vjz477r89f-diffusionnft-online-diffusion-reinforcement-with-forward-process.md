---
title: "DiffusionNFT: Online Diffusion Reinforcement with Forward Process"
title_zh: DiffusionNFT：基于前向过程的在线扩散强化学习
authors: "Kaiwen Zheng, Huayu Chen, Haotian Ye, Haoxiang Wang, Qinsheng Zhang, Kai Jiang, Hang Su, Stefano Ermon, Jun Zhu, Ming-Yu Liu"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=VJZ477R89F"
tags: ["query:diffusion-rl"]
score: 9.0
evidence: 通过前向过程对扩散模型进行在线RL
tldr: 针对扩散模型在线强化学习挑战，提出DiffusionNFT方法，利用流匹配在前向过程中优化扩散模型，通过对比正负生成来定义隐式策略改进方向，避免了反向采样的复杂性和不一致问题，为扩散模型的后训练提供新范式。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 现有扩散模型在线RL方法依赖反向采样离散化，存在求解器限制和前向-反向不一致等问题。
method: 提出DiffusionNFT，在前向过程中通过流匹配进行在线强化学习，对比正负生成以定义策略改进方向。
result: 实验表明该方法有效提升扩散模型性能，优于现有离散化方法。
conclusion: DiffusionNFT为扩散模型在线RL提供了一种更简洁高效的途径。
---

## Abstract
Online reinforcement learning (RL) has been central to post-training language models, but its extension to diffusion models remains challenging due to intractable likelihoods. Recent works discretize the reverse sampling process to enable GRPO-style training, yet they inherit fundamental drawbacks, including solver restrictions, forward–reverse inconsistency, and complicated integration with classifier-free guidance (CFG). We introduce Diffusion Negative-aware FineTuning (DiffusionNFT), a new online RL paradigm that optimizes diffusion models directly on the forward process via flow matching. DiffusionNFT contrasts positive and negative generations to define an implicit policy improvement direction, naturally incorporating reinforcement signals into the supervised learning objective. 
This formulation enables training with arbitrary black-box solvers, eliminates the need for likelihood estimation, and requires only clean images rather than sampling trajectories for policy optimization. DiffusionNFT is up to $25\times$ more efficient than FlowGRPO in head-to-head comparisons, while being CFG-free. For instance, DiffusionNFT improves the GenEval score from 0.24 to 0.98 within 1k steps, while FlowGRPO achieves 0.95 with over 5k steps and additional CFG employment. By leveraging multiple reward models, DiffusionNFT significantly boosts the performance of SD3.5-Medium in every benchmark tested.

---

## 论文详细总结（自动生成）

# 论文详细中文总结：DiffusionNFT

## 1. 核心问题与整体含义（研究动机和背景）
- **背景**：在线强化学习（RL）在语言模型的后训练中已取得核心地位，但将其扩展到扩散模型时面临困难，主要原因是扩散模型的似然函数难以处理（intractable likelihoods）。
- **现有方法局限**：近期工作通过离散化反向采样过程来实现GRPO风格训练，但存在根本性缺陷，包括：求解器限制（只能使用特定离散化求解器）、前向-反向不一致（forward–reverse inconsistency）以及与无分类器引导（CFG）的复杂集成问题。
- **研究动机**：提出一种新的在线RL范式，直接在前向过程中优化扩散模型，避免反向采样的离散化问题，同时保持高效和灵活。

## 2. 论文提出的方法论
- **核心思想**：Diffusion Negative-aware FineTuning (DiffusionNFT) 通过在**前向过程**中利用**流匹配（flow matching）**进行在线RL，对比正样本和负样本生成结果来定义隐式策略改进方向，将强化信号自然地融入监督学习目标。
- **关键技术细节**：
  - 无需估计似然函数，只需干净图像（而非采样轨迹）即可进行策略优化。
  - 支持任意的黑盒求解器（arbitrary black-box solvers），不受离散化求解器限制。
  - 无需CFG（无分类器引导），避免了CFG与RL训练的复杂耦合。
- **算法流程（文字说明）**：
  1. 从当前扩散模型生成一组样本（包括正样本和负样本，根据奖励信号区分）。
  2. 通过流匹配在前向过程中优化模型：对比正负生成，使模型更倾向于产生高奖励的输出。
  3. 隐式策略改进：强化信号通过对比学习直接调整扩散模型的前向路径参数。
- **公式/数学表述**：摘要未给出具体公式，但明确提到“contrasts positive and negative generations to define an implicit policy improvement direction”。

## 3. 实验设计
- **数据集/场景**：未详细说明具体数据集，但提到了**GenEval**评分基准（生成评估指标），以及**SD3.5-Medium**模型（Stable Diffusion 3.5 Medium）。
- **Benchmark**：主要比较了**GenEval score**（从0.24提升到0.98），以及多个奖励模型联合训练时在所有基准上的性能提升。
- **对比方法**：主要与**FlowGRPO**（一种基于流匹配的GRPO方法）进行头对头比较。
- **具体对照结果**：
  - DiffusionNFT在1k步内将GenEval分从0.24提升至0.98。
  - FlowGRPO需要超过5k步且额外使用CFG才能达到0.95。
  - 效率上DiffusionNFT比FlowGRPO快最多25倍。

## 4. 资源与算力
- **未明确说明**：论文摘要和元数据中未提及使用的GPU型号、数量、训练时长等具体算力信息。仅在效率对比中提到“up to 25× more efficient”，但未披露绝对计算量。

## 5. 实验数量与充分性
- **实验数量**：摘要中仅报告了GenEval一个指标的对比结果，以及通过多个奖励模型联合提升SD3.5-Medium性能的结论。未提及消融实验、不同数据集/场景下的系统实验。
- **充分性与公平性**：
  - 信息有限：仅凭摘要无法判断实验的覆盖面和严谨性。缺少多领域、多任务、多种扩散模型架构的对比。
  - 对比方法仅FlowGRPO一个，缺乏与其他在线RL方法（如直接偏好优化、奖励微调等）的比较。
  - 元数据中论文得分为9.0（ICLR 2026），可能说明同行评审认可其实验设计和结果，但细节仍需查看全文。

## 6. 论文的主要结论与发现
- DiffusionNFT作为一种新的在线RL范式，直接在前向过程优化扩散模型，**有效提升了生成质量**（GenEval score从0.24到0.98）。
- **效率显著优于**现有离散化方法（如FlowGRPO），在相同效果下速度快25倍，且无需CFG。
- **灵活性高**：支持任意黑盒求解器，不需要似然估计，只需干净图像作为优化目标。
- **多奖励模型可扩展**：联合多个奖励模型可显著提升SD3.5-Medium在所有测试基准上的性能。

## 7. 优点
- **理论简洁性**：避免了反向采样的离散化和CFG复杂集成，直接在流匹配前向过程中实现RL。
- **实用性强**：支持任意求解器，无需修改采样过程，易于集成到现有扩散模型训练流程。
- **效率高**：对比FlowGRPO，训练步数减少5倍以上，且无需额外CFG计算。
- **无需似然估计**：解决了扩散模型因似然难解而难以应用RL的核心障碍。
- **摆脱CFG依赖**：简化了训练流程，减少了超参数调优负担。

## 8. 不足与局限
- **实验覆盖不明确**：只展示了GenEval一个具体指标和SD3.5-Medium一个模型，未报告在更多标准数据集（如MS-COCO、ImageNet、文本到图像质量指标）或不同规模模型（如SDXL、Stable Diffusion 2/3）上的结果。
- **对比方法单一**：仅与FlowGRPO对比，未与DPO、RLHF等其他扩散模型后训练方法比较。
- **资源算力未公开**：难以评估实际训练成本和可复现性。
- **负样本生成策略**：摘要未说明如何定义正负样本（如阈值、排名方式），可能存在偏差风险。
- **泛化性存疑**：论文聚焦图像生成，未涉及其他扩散模型应用（如视频生成、音频、分子设计等）。
- **可能存在的局限性**：流匹配在前向过程中对比学习是否会导致模式坍塌或奖励过度优化（reward hacking）尚未探讨。

（完）
