---
title: "Advantage Weighted Matching: Aligning RL with Pretraining in Diffusion Models"
title_zh: 优势加权匹配：将强化学习与扩散模型预训练对齐
authors: "Shuchen Xue, Chongjian GE, Shilong Zhang, Yichen Li, Zhi-Ming Ma"
date: 2026-04-30
pdf: "https://openreview.net/pdf/da4575cb055163bb0d0727fb9631e65bc9555c86.pdf"
tags: ["query:diffusion-rl"]
score: 7.0
evidence: 通过优势加权匹配对齐扩散模型中的强化学习与预训练目标
tldr: 现有扩散模型RL方法（如DDPO）优化目标与预训练分数/流匹配损失不一致，导致方差大、收敛慢。本文理论分析表明DDPO是隐式噪声目标匹配，并提出优势加权匹配（AWM），利用分数/流匹配损失作为策略梯度方法，实现了与预训练的对齐，加速收敛并提升性能。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散模型RL优化目标与预训练目标不一致导致训练不稳定。
method: 提出AWM方法，将分数/流匹配损失作为策略梯度目标，实现RL与预训练的对齐。
result: 理论分析和实验表明AWM降低了方差，加快了收敛。
conclusion: AWM为扩散模型的强化学习微调提供了更稳定高效的范式。
---

## Abstract
Reinforcement Learning (RL) has emerged as a central paradigm for advancing Large Language Models (LLMs), where both pre-training and RL post-training stages are grounded in the same log-likelihood formulation. In contrast, recent RL approaches for diffusion models, most notably Denoising Diffusion Policy Optimization (DDPO), optimize an objective different from the pretraining objectives--score/flow matching loss.
In this work, we establish a novel theoretical analysis: DDPO is an implicit form of score/flow matching with noisy targets, which increases variance and slows convergence. Building on this analysis, we introduce \textbf{Advantage Weighted Matching (AWM)}, a policy-gradient method for diffusion. It uses the score/flow-matching loss and reweights each sample by its advantage. In effect, AWM raises the influence of high-reward samples and suppresses low-reward ones while keeping the modeling objective identical to pretraining. 
This simple yet effective design yields substantial benefits: on the GenEval, OCR, and PickScore benchmarks, AWM delivers up to a $\mathbf{34}\times$ speedup over Flow-GRPO (which builds on DDPO), when applied to Stable Diffusion 3.5 Medium and FLUX, without compromising generation quality. Code is available at \url{https://github.com/scxue/advantage_weighted_matching}.

---

## 论文详细总结（自动生成）

# 详细中文总结：优势加权匹配（Advantage Weighted Matching）

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **研究动机**：在大语言模型（LLM）中，预训练和强化学习（RL）后训练阶段都基于相同的对数似然公式，两者天然对齐；然而，扩散模型现有的 RL 方法（如 DDPO）优化的目标与预训练目标（分数/流匹配损失）不一致。
- **核心问题**：这种不一致导致 RL 微调过程中方差大、收敛慢，限制了扩散模型在下游任务（如图像生成质量优化）中的 RL 应用效果。
- **整体含义**：作者通过理论分析揭示了 DDPO 等价于带噪声目标的隐式分数/流匹配，从而解释其高方差和慢收敛；并据此提出 **优势加权匹配（AWM）**，将预训练损失与 RL 优势加权结合，实现 RL 优化目标与预训练目标的对齐，提升训练效率和生成质量。

## 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程
- **核心思想**：将扩散模型预训练中使用的分数/流匹配损失直接作为 RL 策略梯度优化的目标，并通过每个样本的优势值（Advantage）重新加权采样数据。
- **关键技术细节**：
  - **分数/流匹配损失**：与传统扩散模型预训练保持一致，优化去噪网络对真实数据分布的拟合。
  - **优势加权**：对于每个生成的样本，计算其相对于基准的优势（如奖励减去基线），用该优势作为权重重写损失函数。高奖励样本的损失权重更大，低奖励样本的权重被抑制。
  - **与 DDPO 的关系**：作者理论证明 DDPO 等价于对带噪声目标进行隐式分数/流匹配，而 AWM 直接对干净目标进行加权匹配，避免了噪声引入的额外方差。
- **算法流程（文字说明）**：
  1. 使用当前扩散策略生成一批样本；
  2. 通过奖励模型或评分函数计算每个样本的奖励值；
  3. 计算每个样本的优势值（例如 reward - baseline）；
  4. 以分数/流匹配损失为基础，对每个样本的损失项乘以对应的优势值，得到加权后的策略梯度；
  5. 更新扩散模型参数以最小化加权损失，从而鼓励高优势样本对应的输出模式。

## 3. 实验设计：数据集/场景、基准测试、对比方法
- **数据集/场景**：
  - **GenEval**：图像生成质量评估基准（可能涉及多维度指标）。
  - **OCR**：光学字符识别任务，可能测试生成文本的准确性。
  - **PickScore**：基于人类偏好评分的图像质量评估。
- **基准测试模型**：
  - **Stable Diffusion 3.5 Medium**
  - **FLUX**（另一扩散模型）
- **对比方法**：
  - **Flow-GRPO**：一种基于 DDPO 的强化学习方法（论文明确提到 “Flow-GRPO which builds on DDPO”）。
  - 未提及与其他方法（如直接 DDPO、其他 RL 微调方法）的对比，但可能存在消融实验。

## 4. 资源与算力
- **论文中未明确说明**：使用的 GPU 型号、数量、训练时长等具体算力信息。摘要和提供的元数据中均未提及硬件的详细配置。
- **备注**：需注意论文可能在其他章节包含该信息，但当前提取内容有限，无法进一步判断。

## 5. 实验数量与充分性
- **实验数量**：摘要中提到在 GenEval、OCR、PickScore 三个基准上进行了实验，并且应用了两个不同模型（SD3.5 Medium 和 FLUX）。推测至少有 3×2 = 6 组主要对比实验，可能还包含消融研究（例如是否使用优势加权、不同基线设置等），但未在摘要中具体说明。
- **充分性与公平性**：
  - 优点：跨多个基准和主流模型验证了 AWM 的加速效果（最高 34 倍），覆盖了不同评估维度。
  - 不足：对比方法仅提及 Flow-GRPO，未与其他标准 RL 微调方法（如 DDPO 本身、细粒度 RLHF 方法）进行充分比较；缺乏对生成质量下降风险的量化分析（虽然宣称不牺牲质量，但未提供具体指标数值）；可能缺少在真实世界大规模数据集上的泛化实验。

## 6. 论文的主要结论与发现
- **理论发现**：DDPO 本质上是带噪声目标的隐式分数/流匹配，导致方差大、收敛慢。
- **方法结论**：AWM 通过将优势加权直接应用于预训练损失，实现了 RL 优化与预训练目标的对齐，显著降低了方差，加快了收敛速度。
- **实验结论**：在 GenEval、OCR、PickScore 基准上，AWM 相比 Flow-GRPO 实现高达 **34 倍** 的加速（当应用于 Stable Diffusion 3.5 Medium 和 FLUX 时），且生成质量不降低。
- **总体意义**：AWM 为扩散模型强化学习微调提供了一种更稳定、高效的范式，弥合了 RL 与预训练之间的目标鸿沟。

## 7. 优点
- **理论深刻**：首次从理论上揭示 DDPO 的噪声目标匹配本质，为改进提供明确方向。
- **简洁有效**：方法设计简单（仅牺牲加权机制），但效果显著，易于实现和集成到现有扩散模型 pipeline。
- **与预训练完全对齐**：保持模型预训练能力的同时引入 RL 信号，避免灾难性遗忘和训练不稳定。
- **大幅加速**：34 倍速度提升表明在相同计算预算下可实现更高的样本效率。
- **开源代码**：提供 GitHub 仓库，促进复现和后续研究。

## 8. 不足与局限
- **对比基线有限**：仅有 Flow-GRPO 一个对比方法，缺少与 DDPO 原始方法、其他基于 RL 的扩散模型微调方法（如 DPOK、DRA）的公平比较。
- **实验细节缺失**：未提供具体计算资源（GPU 型号/数量/时长），影响实际加速倍数的可复现性；无具体指标数值（如 FID、CLIP score 等）来支撑“不降低生成质量”的声明。
- **应用限制**：方法依赖于优势估计（advantage estimation），在复杂奖励函数或长尾样本下可能引入偏差；未讨论对多步骤、高维图像任务（如视频生成）的适用性。
- **未测试预训练模型多样性**：仅测试 SD3.5 Medium 和 FLUX，其他主流扩散模型（如 DALL·E、Imagen 系列）未涉及，可能存在模型特定依赖。
- **缺乏人类评估**：尽管使用 PickScore 等代理指标，但未进行直接的人类偏好测试来验证生成质量的真实改善。

（完）
