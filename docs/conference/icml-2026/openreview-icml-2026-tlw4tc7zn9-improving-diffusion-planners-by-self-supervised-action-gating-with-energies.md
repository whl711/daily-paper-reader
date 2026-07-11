---
title: Improving Diffusion Planners by Self-Supervised Action Gating with Energies
title_zh: 通过自监督动作门控与能量改进扩散规划器
authors: "Yuan Lu, Dongqi Han, Yansen Wang, Dongsheng Li"
date: 2026-04-30
pdf: "https://openreview.net/pdf/fda8852a7b6798c4ab5dea9a56014bd334160bd1.pdf"
tags: ["query:diffusion-rl"]
score: 7.0
evidence: 通过自监督动作门控改进离线RL中的扩散规划器
tldr: 扩散规划器在离线RL中可能选择与环境动态不一致的轨迹，导致执行脆弱。SAGE方法训练联合嵌入预测架构编码器和动作条件预测器，在推理时根据潜在预测误差计算能量，结合价值估计进行重排序，惩罚动态不一致的规划，从而提升扩散规划器的鲁棒性和样本效率。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散规划器可能选择局部不一致的轨迹，导致性能脆弱。
method: 提出SAGE，利用自监督潜在一致性信号进行推理时重排序。
result: 在离线RL基准上提升了规划质量和执行鲁棒性。
conclusion: SAGE为扩散规划器提供了一种轻量级的加固方法。
---

## Abstract
Diffusion planners are a strong approach for offline reinforcement learning, but they can fail when value-guided selection favours trajectories that score well yet are locally inconsistent with the environment dynamics, resulting in brittle execution. We propose Self-supervised Action Gating with Energies (SAGE), an inference-time re-ranking method that penalises dynamically inconsistent plans using a latent consistency signal. SAGE trains a Joint-Embedding Predictive Architecture (JEPA) encoder on offline state sequences and an action-conditioned latent predictor for short horizon transitions. At test time, SAGE assigns each sampled candidate an energy given by its latent prediction error and combines this feasibility score with value estimates to select actions. SAGE can integrates into existing diffusion planning pipelines that can sample trajectories and select actions via value scoring; it requires no environment rollouts and no policy re-training. Across locomotion, navigation, and manipulation benchmarks, SAGE improves the performance and robustness of diffusion planners.

---

## 论文详细总结（自动生成）

# 论文总结：通过自监督动作门控与能量改进扩散规划器

## 1. 核心问题与整体含义
- **研究动机**：离线强化学习中，扩散规划器（Diffusion Planners）通过价值指导选择高评分轨迹，但可能选出局部与环境动态不一致的计划，导致执行脆弱。
- **整体含义**：提出一种轻量级推理时重排序方法，增强扩散规划器的鲁棒性和样本效率，无需环境交互或策略重训练。

## 2. 方法论
- **核心思想**：利用自监督学习的潜在一致性信号，惩罚动态不一致的轨迹。
- **关键技术细节**：
  - 训练一个联合嵌入预测架构（JEPA）编码器，在离线状态序列上学习潜在表示。
  - 训练一个动作条件潜在预测器，用于短视界状态转换的预测。
  - 推理时，为每个候选轨迹计算一个“能量”，即潜在预测误差，作为可行性分数。
  - 将能量与价值估计结合（如加权或重排序），选择既能获得高回报又与动态一致的行动。
  - 该方法可融入现有扩散规划流程（采样轨迹→价值评分→选动作），无需额外环境 rollout 或重新训练策略。

## 3. 实验设计
- **数据集/场景**：运动（locomotion）、导航（navigation）、操作（manipulation）三类基准任务。
- **基准**：未明确列举具体环境名称（如 D4RL、Meta-World 等），但涵盖三类典型离线 RL 场景。
- **对比方法**：未明确列出，仅说明“提高了扩散规划器的性能和鲁棒性”，推测与原始扩散规划器及其他离线 RL 方法（如 IQL、CQL）对比。

## 4. 资源与算力
- 文中未提及 GPU 型号、数量或训练时长等具体算力信息。仅说明 SAGE 是轻量级推理时方法，无需重训练策略，可能计算开销较低。

## 5. 实验数量与充分性
- 文中未给出具体实验次数、数据集数量、消融实验等详细描述。仅通过 Abstract 提及“在运动、导航、操作基准上提升性能”，但未报告统计显著性或完整实验表格。
- **充分性评价**：信息不充分，无法判断实验的覆盖面和重复性。缺乏与基线方法的详细对比表格和方差分析。

## 6. 主要结论与发现
- SAGE 能有效惩罚动态不一致的规划，提升扩散规划器的执行鲁棒性和最终性能。
- 方法轻量，可即插即用，无需额外交互或训练，具有实用价值。

## 7. 优点
- **创新性**：首次将自监督潜在一致性信号用于扩散规划器的推理时重排序，思路简洁新颖。
- **实用性**：无需环境交互或策略重训练，可集成到现有扩散规划管线中。
- **领域通用性**：在运动、导航、操作三类不同任务中均有效，表明方法泛化能力较强。

## 8. 不足与局限
- **实验细节缺失**：未提供环境、数据集、超参数、基线方法等具体信息，难以复现和评估可信度。
- **理论分析不足**：未给出能量与动态一致性的理论保证，或对预测误差阈值的敏感性分析。
- **可能局限**：依赖离线数据训练的 JEPA 编码器和预测器，若离线数据分布偏移严重，预测误差信号可能失效；此外，推理时计算每条轨迹的潜在预测误差可能带来额外时间开销。
- **应用限制**：仅适用于能够采样多条候选轨迹的扩散规划器，对其他类型的规划器不适用。

（完）
