---
title: "Diffusion Controller: Framework, Algorithms and Parameterization"
title_zh: 扩散控制器：框架、算法与参数化
authors: "Tong Yang, Moonkyung Ryu, Chih-Wei Hsu, Guy Tennenholtz, Yuejie Chi, Craig Boutilier, Bo Dai"
date: 2026-04-30
pdf: "https://openreview.net/pdf/04e003861511e6de9556c0f76e1ef39c9075a3a4.pdf"
tags: ["query:diffusion-rl"]
score: 7.0
evidence: 从控制论角度统一可控扩散生成，推导出用于扩散微调的RL方法
tldr: 该论文提出扩散控制器（DiffCon），将反向扩散采样视为（广义）线性可解MDP中的仅状态随机控制，通过重加权预训练反向转移核实现对终端目标与f-散度成本的平衡。从最优条件推导出实用的RL微调方法，包括基于f-散度的策略梯度更新和PPO风格规则。理论分析和实验验证了该框架的有效性。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 可控扩散生成的各种启发式方法缺乏统一理解，需要系统化的框架。
method: 将扩散采样建模为LS-MDP控制问题，推导出使用f-散度正则化策略梯度的RL方法。
result: 在图像生成等任务上，扩散控制器派生的RL方法改善了可控性，与现有启发式方法相比性能更优。
conclusion: 控制论视角为扩散模型的RL微调提供了统一且理论驱动的框架。
---

## Abstract
Controllable diffusion generation often relies on various heuristics that are seemingly disconnected without a unified understanding. We bridge this gap with Diffusion Controller (DiffCon), a unified control-theoretic view that casts reverse diffusion sampling as state-only stochastic control within (generalized) linearly-solvable Markov Decision Processes (LS-MDPs). Under this framework, control acts by reweighting the pretrained reverse-time transition kernels, balancing terminal objectives against an $f$-divergence cost. From the resulting optimality conditions, we derive practical reinforcement learning methods for diffusion fine-tuning: (i) $f$-divergence-regularized policy-gradient updates, including a PPO-style rule, and (ii) a regularizer-determined reward-weighted regression objective with a minimizer-preservation guarantee under the Kullback–Leibler (KL) divergence. The LS-MDP framework further implies a principled model form: the optimal score decomposes into a fixed pretrained baseline plus a lightweight control correction, motivating a side-network parameterization conditioned on exposed intermediate denoising outputs, enabling effective *gray-box adaptation* with a frozen backbone. Experiments on Stable Diffusion v1.4 across supervised and reward-driven finetuning show consistent gains in preference-alignment win rates and improved quality–efficiency trade-offs versus gray-box baselines and even the parameter-efficient white-box adapter LoRA.

---

## 论文详细总结（自动生成）

# 扩散控制器：框架、算法与参数化 —— 详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

可控扩散生成是当前生成模型领域的热点，但目前存在大量启发式方法（如分类器引导、无分类器引导、基于强化学习的微调等），这些方法看似彼此独立，缺乏统一的理论理解。论文旨在填补这一空白，从控制论视角出发，将反向扩散采样建模为（广义）线性可解马尔可夫决策过程（LS-MDP）中的仅状态随机控制问题，从而为扩散模型的强化学习（RL）微调提供统一且理论驱动的框架。

## 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：将反向扩散过程视为LS-MDP中的控制过程，控制动作通过重加权预训练的反向转移核来实现，在终端目标（如生成收益）与 $f$-散度成本之间进行平衡。
- **关键技术细节**：
  - 从LS-MDP的最优条件出发，推导出实用的RL微调方法：
    1. **$f$-散度正则化的策略梯度更新**：包括一种PPO风格的更新规则，利用重要性采样和裁剪机制稳定训练。
    2. **正则化确定的奖励加权回归（Reward-Weighted Regression）目标**：在KL散度下具有极小值保持保证，即优化后的策略在KL正则化下仍保持预训练分布的特性。
  - LS-MDP框架暗示了一种原则性的模型形式：最优分数函数分解为固定的预训练基线加上一个轻量的控制校正项。这激发了**侧网络参数化**（side-network parameterization），即仅对暴露的中间去噪输出进行条件调整，实现对冻结主干网络的**灰盒适应**（Gray-Box Adaptation）。
- **算法流程（文字说明）**：
  - 输入：预训练扩散模型（如Stable Diffusion）、任务奖励函数（或监督目标）。
  - 在反向扩散步骤中，将每一步的去噪操作视为控制动作，使用一个轻量侧网络（side-network）输出控制校正项，与预训练分数相加得到修正后的分数。
  - 训练侧网络：采用基于 $f$-散度正则化的策略梯度（或PPO变体）或奖励加权回归目标，采样生成轨迹并计算奖励，更新侧网络参数。
  - 在推理时，用冻结的主干加上训练好的侧网络进行采样，实现可控生成。

## 3. 实验设计：使用了哪些数据集 / 场景，它的 benchmark 是什么，对比了哪些方法

- **实验场景**：基于 **Stable Diffusion v1.4** 进行两类微调：
  - **监督式微调**（supervised fine-tuning）：利用带标签数据优化生成结果与目标的一致性。
  - **奖励驱动微调**（reward-driven fine-tuning）：使用由人类偏好或质量指标定义的奖励函数进行RL微调。
- **Benchmark与对比方法**：
  - 对比标准灰盒基线（gray-box baselines，即仅微调解码器或部分层的方法）；
  - 对比参数高效的白盒适配器 **LoRA**（低秩适应）；
  - 可能包括无微调的预训练基线、纯策略梯度基线等（具体依据摘要未列出全部）。
- **评估指标**：偏好对齐胜率（win rates against preference alignment）、质量-效率权衡（quality–efficiency trade-offs）。

## 4. 资源与算力：如果文中有提到，请总结使用了多少算力（GPU 型号、数量、训练时长等）。若未明确说明，也请指出这一点。

论文摘要中**未明确说明**所使用的GPU型号、数量或训练时长等算力资源。仅提到基于Stable Diffusion v1.4进行实验，该模型本身需要一定算力，但具体训练配置未披露。因此无法从摘要中推断算力细节。

## 5. 实验数量与充分性：大概做了多少组实验（如不同数据集、消融实验等），这些实验是否充分、是否客观、公平。

- 实验覆盖了两类场景（监督和奖励驱动），在每个场景中使用了预训练基线、灰盒基线、LoRA等对比方法。
- 摘要仅提及“一致性的偏好对齐胜率提升”以及“改进的质量–效率权衡”，但未列出具体数值、数据集名称或消融实验细节。
- 从有限信息看，实验设计涵盖了主要对比方法，但缺少多数据集、多任务验证，也未报告实验的重复次数或统计显著性。因此，**实验充分性有待进一步评估**，可能存在仅验证单模型、单任务的偏差风险。需要阅读全文才能判断公平性（如超参数调优是否一致、环境是否相同等）。

## 6. 论文的主要结论与发现

- 控制论视角（Diffusion Controller）为扩散模型的RL微调提供了统一且理论驱动的框架，有效统一了多种启发式方法。
- 从LS-MDP最优条件派生的RL方法（$f$-散度正则化策略梯度、PPO风格、奖励加权回归）在实践中取得了优于灰盒基线及参数高效白盒适配器LoRA的性能。
- 侧网络参数化（灰盒适应）在冻结主干的情况下实现了有效的控制，平衡了生成质量和计算效率。
- 偏好对齐胜率显著提升，表明该方法在生成符合人类偏好的内容方面具有优势。

## 7. 优点：方法或实验设计上有哪些亮点

- **理论统一**：将看似分散的可控扩散方法纳入LS-MDP控制框架，提供了数学严谨的最优性条件，推导出实用的RL算法。
- **模型设计新颖**：基于分数分解提出侧网络参数化，实现灰盒适应，既保留了预训练模型的知识，又添加了轻量的控制能力。
- **算法多样性**：同时给出了基于策略梯度（含PPO变体）和基于奖励加权回归两种方法，适应不同场景。
- **实验对比全面**：对比了灰盒基线、参数高效LoRA等方法，覆盖监督和奖励驱动两种微调范式。

## 8. 不足与局限：包括实验覆盖、偏差风险、应用限制等

- **实验覆盖不完整**：仅基于Stable Diffusion v1.4，未在更多扩散模型（如扩散语言模型、视频生成模型）上验证，也未在多种奖励函数或评价指标上详细展开。
- **数据与任务细节缺失**：未提及训练数据集、任务具体定义（如用于偏好对齐的人类数据来源）、评估基准名称，无法复现或横向比较。
- **计算资源未说明**：缺乏算力信息，难以评估方法的实际训练成本。
- **潜在偏差风险**：可能只在特定奖励函数下表现优异，而对其他奖励函数或对抗性攻击的鲁棒性未知。
- **应用限制**：灰盒适应虽然高效，但侧网络的设计依赖于预训练模型的中间输出；若模型结构差异大，可能需要调整。此外，$f$-散度正则化可能引入对散度选择的敏感度。

（完）
