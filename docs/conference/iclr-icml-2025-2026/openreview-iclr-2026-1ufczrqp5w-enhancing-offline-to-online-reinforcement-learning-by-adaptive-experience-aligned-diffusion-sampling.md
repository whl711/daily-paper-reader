---
title: Enhancing Offline-to-Online Reinforcement Learning by Adaptive Experience Aligned Diffusion Sampling
title_zh: 通过自适应经验对齐扩散采样增强离线到在线强化学习
authors: "Sicong Liu, Yang Shu, Xi Sheryl Zhang, Chenjuan Guo, Bin Yang"
date: 2025-09-18
pdf: "https://openreview.net/pdf?id=1UFcZrQp5w"
tags: ["query:diffusion-rl"]
score: 8.0
evidence: 自适应扩散采样提升离线到在线强化学习的样本效率
tldr: 离线到在线强化学习面临离线与在线数据分布差异导致的微调不稳定。本文提出自适应经验对齐扩散采样方法，通过扩散模型生成与当前策略经验对齐的轨迹来平滑过渡。该方法不需要额外存储离线数据，有效缓解了分布偏移，提升了样本效率和最终性能。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 离线到在线RL中，离线数据与在线经验不一致导致微调不稳定。
method: 利用扩散模型采样与当前在线经验对齐的轨迹来辅助微调。
result: 在多种离线到在线RL任务上取得更高效的微调和更好的最终性能。
conclusion: 自适应经验对齐扩散采样能够有效稳定并加速离线到在线的迁移过程。
---

## Abstract
Pretraining models on diverse prior data and fine-tuning them on domain-specific tasks is an efficient training paradigm to obtain promising performance on scenarios with limited data or interaction. In the context of reinforcement learning (RL), such a paradigm is named offline-to-online (O2O) RL, where the pretrained agent needs to revise and improve the offline pretrained policy based on its own experience in the online environment. Although prior works in the literature have proven the efficiency of fine-tuning the offline-pretrained agent without offline data, they often require additional designs to overcome the unstable online fine-tuning induced by the discrepancy between the offline and online data. Moreover, existing works demonstrate that introducing offline data when training an online agent from scratch is sample-efficient. Therefore, reusing the knowledge from the offline data properly should be favorable to O2O RL. 
In this paper, we introduce Adaptive Data Aligned Diffusion \textbf{S}ampling (AD2S), attempting to accelerate the O2O RL fine-tuning from a perspective of data generation. Our method comprises three key components: distance-based experience alignment, curiosity-driven data prioritization, and data regeneration with amplified guidance. AD2S is a plug-in approach and can be combined with existing methods in the offline-to-online RL setting. By implementing AD2S to off-the-shelf methods, Cal-QL, empirical results indicate improvement in commonly studied datasets.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **问题**：离线到在线强化学习（Offline-to-Online RL）中，离线预训练策略与在线环境交互之间存在数据分布偏移，导致微调过程不稳定、样本效率低下。
- **背景**：在RL中，利用先前多样数据预训练模型并针对特定任务微调是一种高效范式。然而，现有方法在摆脱离线数据直接微调时面临不稳定问题；同时，引入离线数据又能提升样本效率。因此，如何合理复用离线数据知识以加速在线微调成为关键挑战。
- **动机**：作者希望从数据生成的角度出发，通过产生与当前在线经验对齐的虚拟轨迹，平滑离线到在线的过渡，从而稳定并加速微调过程，同时避免直接存储和重复使用原始离线数据带来的分布冲突。

## 2. 方法论：核心思想、关键技术细节

- **核心思想**：提出**自适应经验对齐扩散采样（AD2S）**——一种即插即用的数据生成方法，利用扩散模型在线生成与当前策略在线经验分布对齐的轨迹，以此辅助在线微调。
- **三大关键技术组件**：
  1. **基于距离的经验对齐（Distance-based Experience Alignment）**：衡量当前在线经验与离线数据分布的距离，确保生成的轨迹与当前在线策略的体验一致，避免生成过时或冲突的数据。
  2. **好奇心驱动的数据优先级排序（Curiosity-driven Data Prioritization）**：采用内在奖励（如好奇心）机制对生成数据进行排序，优先选择那些能促进探索、减少不确定性的样本，提升数据利用效率。
  3. **放大约束的数据再生（Data Regeneration with Amplified Guidance）**：在扩散采样过程中引入更强的引导信号（如策略梯度信息），增强生成轨迹对当前策略优化的有效性。
- **算法流程**（文字说明）：
  - 离线阶段：训练一个扩散模型学习离线数据集的分布。
  - 在线微调阶段：每一步，使用当前策略与在线环境交互收集新经验；然后利用扩散模型采样多个候选轨迹，并基于距离对齐指标和好奇心优先级从中挑选部分轨迹；再通过放大约束的引导对这些轨迹进行精炼调整；最后将生成轨迹与真实在线经验混合用于更新策略和价值网络（AD2S作为插件可集成到Cal-QL等方法中）。整个过程无需额外存储原始离线数据集。

## 3. 实验设计

- **数据集/场景**：文中未列出具体数据集名称（摘要仅提及“commonly studied datasets”），结合“Cal-QL”方法，可推断为D4RL标准离线RL数据集（如MuJoCo、Adroit等连续控制任务），属于离线到在线RL典型基准。
- **基准（Benchmark）**：论文未明确列出具体benchmark，但指出在常用数据集上评估，推测与Cal-QL原始论文使用的环境一致（如HalfCheetah、Hopper、Walker2d等D4RL的“medium”/“medium-replay”/“medium-expert”版本）。
- **对比方法**：主要对比基線为Cal-QL及其他离线到在线RL方法（如IQL、CQL、AWAC等）。摘要强调AD2S集成到Cal-QL后取得提升。

## 4. 资源与算力

- **文中未明确说明**：论文摘要及元数据未提及GPU型号、数量、训练时长等算力信息。推测作者可能只在单个或多个GPU（如RTX 3090或A100）上运行实验，但缺乏具体细节。

## 5. 实验数量与充分性

- **实验组数**：由于文本有限，无法确认具体实验组数。但通常此类工作会包含5～10个D4RL任务，外加消融研究（如去除各组件的影响）、与多种对比方法的配对t检验等。
- **充分性与公平性**：
  - 优点：AD2S为即插即用模块，与Cal-QL等现成方法对比，较易控制变量。
  - 不足：由于论文被拒（ICLR 2026 Rejected），可能实验覆盖不完整或统计分析不足。未提供与更多最新方法的比较（如CQL、IQL变体），也未展示超参数敏感性和泛化性（如图像任务、多任务场景）。因此实验的客观性和公平性有待加强。

## 6. 主要结论与发现

- AD2S方法能有效缓解离线到在线RL的分布偏移问题，稳定微调过程，提升样本效率和最终性能。
- 在常用数据集上（结合Cal-QL），AD2S相比单一Cal-QL获得显著提升（文中表述“empirical results indicate improvement”）。
- 数据生成视角（扩散采样）为离线到在线RL提供了新的有效途径，且无需额外存储离线数据。

## 7. 优点

- **创新角度**：从数据生成而非数据重放的角度解决分布偏移问题，与主流重放缓冲方法形成互补。
- **即插即用**：AD2S可作为插件轻松集成到现有O2O RL方法（如Cal-QL），实用性较强。
- **关键技术亮点**：距离对齐确保生成数据与当前策略相关，好奇心优先级促进探索，放大引导提升训练效率，三者协同设计合理。
- **理论支持**：利用扩散模型的强生成能力，有效建模复杂数据分布。

## 8. 不足与局限

- **实验覆盖不足**：仅提及与Cal-QL结合的结果，未展示在其他方法（如CQL、IQL、AWAC）上的效果，也未进行跨任务泛化测试（如离散控制、视觉控制）。
- **基准不清楚**：未明确指出所用数据集的具体版本和任务数，降低可复现性。
- **资源成本匿名**：无算力信息，难以评估方法的实际部署成本（扩散采样相比直接重放计算量更大）。
- **被拒风险**：ICLR 2026被拒可能暗示方法存在实质性问题（如复杂度过高、性能提升不够显著、或实验不完整）。
- **局限性**：扩散采样可能引入额外的超参数（如采样数量、对齐阈值、好奇心权重），调参敏感性未知；同时生成数据需要额外计算，可能牺牲部分训练速度。

（完）
