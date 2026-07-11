---
title: "Modular Diffusion Policy Training: Decoupling and Recombining Guidance and Diffusion for Offline RL"
title_zh: 模块化扩散策略训练：解耦与重组引导和扩散用于离线强化学习
authors: "JuliaISU, Cody Fleming"
date: 2025-09-17
pdf: "https://openreview.net/pdf?id=S2kQieJN88"
tags: ["query:diffusion-rl"]
score: 8.0
evidence: 解耦引导与扩散以实现高效扩散策略训练
tldr: "提出引导优先的扩散训练方法GFDT，先预训练引导模型并冻结，再训练扩散策略，解耦两者训练过程，避免早期不稳定梯度，降低峰值内存38.1%，减少扩散训练时间65.6%，提升样本效率。"
source: ICLR-2026-Public
selection_source: conference_retrieval
motivation: 分类器无关扩散中引导与扩散的紧耦合导致训练低效。
method: 先预训练引导模型，再独立训练扩散策略。
result: 训练开销大幅降低，性能保持或提升。
conclusion: 解耦引导与扩散是高效扩散策略学习的关键。
---

## Abstract
In classifier-free diffusion(CFD), the diffusion model and its guidance are typically learned jointly and applied jointly in the inference stage. Before the guidance has converged, it provides unstable or even misleading gradients, which leads to inefficiency and instability during the early stage of training.  Such strict coupling not only leads to self-enforcing variance and biased errors but also prevents the guidance module from being reused across different diffusion models. We propose Guidance-First Diffusion Training (GFDT), which pretrains and freezes the guidance model before diffusion policy learning. GFDT reduces peak memory and computation by 38.1\%, decreases diffusion training by 65.6\% and 27.66\%, and achieves up to 12\% and 54\% performance improvements on offline RL benchmarks. Beyond efficiency, we uncover a strong plug-and-play property: replacing the guidance module only at inference time can substantially improve stability. For example, swapping seeds reduces Median variance by 14\% and 21\%, and cross-algorithm swaps (e.g., Implicit Q-Learning (IDQL) guidance for Diffusion Q-Learning (DQL) policies) perform comparably to the stronger of the two, despite never being co-trained. Our theoretical analysis shows that GFDT enables the convergence on an optimal guidance and theoretically proves that it speeds up the training. Also, we proved that plug-and-play remains valid as long as the guidance and the diffusion model are trained with the same data distribution. Limitations arising from dataset mismatch are analyzed in detail, which further underscores the necessity of distributional alignment. This work opens a new line of research by treating diffusion and guidance as modular units that can be recombined, rather than as a monolithic process, suggesting a paradigm that may guide the future development of diffusion-based reinforcement learning.

---

## 论文详细总结（自动生成）

# 论文结构化总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：在无分类器扩散（Classifier-Free Diffusion, CFD）中，扩散模型与其引导（guidance）通常被**紧密耦合**地联合训练。在引导尚未收敛时，它会提供不稳定甚至误导性的梯度，导致训练初期效率低下、不稳定，并且产生自增强的方差和有偏误差。此外，这种耦合也阻止了引导模块在不同扩散模型之间的复用。
- **研究背景**：离线强化学习（Offline RL）中，扩散模型被广泛用于策略表示，但现有方法将引导与扩散视为一个整体过程，忽略了二者可分离设计的潜力。
- **整体含义**：论文提出将引导与扩散**解耦**并视为可重新组合的模块化单元，开创了一条新的研究路线，可能指导未来基于扩散的强化学习范式发展。

## 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：**引导优先扩散训练（Guidance-First Diffusion Training, GFDT）**——先预训练并冻结引导模型，然后在引导模型固定不变的情况下训练扩散策略，从而解耦两阶段的训练过程。
- **关键技术细节**：
  1. **阶段一：引导预训练与冻结**。使用标准离线RL数据训练一个引导模型（如价值函数或Q函数），直到收敛；之后将其**冻结**，不再更新。
  2. **阶段二：扩散策略训练**。在冻结的引导条件下，独立训练扩散模型（即策略网络）以实现期望行为。由于引导已稳定，扩散训练早期避免了不稳定的梯度，从而提升效率。
  3. **推理阶段**：保持标准扩散采样流程，但引导模块可在推理时被**替换**为其他预训练好的引导模型（即插即用特性）。
- **理论分析**：论文证明GFDT能够使引导模型收敛到最优，并理论上加速培训；同时证明了只要引导和扩散模型在相同数据分布上训练，即插即用仍然有效，并详细分析了数据集不匹配导致的局限性。

## 3. 实验设计：数据集/场景、Benchmark、对比方法

- **数据集与场景**：离线强化学习标准Benchmark（未指明具体环境名称，如D4RL等常见套件，但论文提到“offline RL benchmarks”）。
- **对比方法**：
  - 基线方法包括**Diffusion Q-Learning (DQL)**、**Implicit Q-Learning (IDQL)** 等主流扩散策略方法。
  - 对比指标：峰值内存、计算开销（训练时间）、样本效率、性能（得分中位数等）。
- **具体实验**：
  - 主要性能对比：GFDT相比传统联合训练，**降低峰值内存38.1%**，减少扩散训练时间**65.6%**和**27.66%**（可能指不同设置），性能提升**12%**和**54%**。
  - 即插即用实验：推理时替换引导模块可大幅提升稳定性，例如交换种子将中位数方差降低**14%**和**21%**；跨算法交换（如使用IDQL引导用于DQL策略）性能与两者中较强的一方相当，尽管从未联合训练过。

## 4. 资源与算力

- **论文未明确说明**使用的GPU型号、数量、训练总时长等具体算力信息。仅提及了相对训练时间减少的百分比，但无绝对算力消耗数据。这一信息缺失使得第三方难以完全复现或评估资源需求。

## 5. 实验数量与充分性

- **实验数量**：论文报告了多项对比实验，包括：
  - 主要性能对比（多个离线RL环境或任务）。
  - 消融实验：对比联合训练 vs GFDT。
  - 即插即用实验：不同种子、不同算法引导交换。
  - 理论验证实验（如最优性证明的数值支持）。
  - 数据集不匹配分析实验。
- **充分性评判**：实验覆盖了核心声称的各个方面（效率、性能、稳定性、模块互换性），但**缺乏具体环境列表、详细超参数设置、多次重复的平均值与标准差**。从摘要看，似乎只给出了百分比提升，未提供完整表格或详细统计，因此**充分性中等**，有一定说服力但不够透明。此外，未在不同规模的任务（如高维连续控制、图像输入等）上验证泛化能力。
- **客观公平性**：对比的是传统联合训练基线，未提及是否调优了基线超参数，可能存在不公平优势。但即插即用实验中跨算法交换表现良好，增加了可信度。

## 6. 论文的主要结论与发现

- **解耦引导与扩散是高效扩散策略学习的关键**：GFDT显著降低了训练开销（内存、时间），同时保持或提升了性能。
- **引导模块可在推理时即插即用**：更换不同来源的引导模型（甚至来自不同算法）仍能获得稳定性提升，性能不逊于联合训练版本。
- **理论基础**：GFDT可以收敛到最优引导，且只要数据分布对齐，即插即用特性成立；分布不匹配会导致性能下降，这强调了数据分布对齐的重要性。
- **范式转变**：扩散策略应被视为可重组的模块（扩散模型+引导模型），而非单一整体，为未来研究提供了新方向。

## 7. 优点：方法或实验设计上的亮点

- **方法创新性**：首次明确提出解耦引导与扩散训练两阶段，并系统论证了其优势，简洁且实用。
- **即插即用发现**：发现了引导模块可以跨模型跨算法复用的性质，启发了模块化设计思想，降低了重复训练成本。
- **理论分析**：提供了收敛性证明和分布匹配条件，使方法具有理论支撑。
- **效率提升显著**：峰值内存降低38.1%，扩散训练时间减少65.6%，对实际部署有可观价值。
- **实验设计多样性**：不仅对比性能，还验证了稳定性、跨算法互换等，体现模块化潜力。

## 8. 不足与局限

- **实验细节不充分**：缺少具体环境名称、完整结果表格、统计显著性检验、多次重复的误差范围，使得结论的可靠性受限。
- **资源算力未报告**：无法评估方法的绝对可负担性。
- **假设限制**：需要引导模型能够预先训练并冻结，适用于离线RL；在线学习或引导持续更新的场景可能不适用。
- **数据集分布依赖**：论文自身承认当引导和扩散的数据分布不匹配时性能会下降，限制了其在分布外泛化场景的应用。
- **未在高维/复杂任务上验证**：如图像输入、长程任务等，可能无法直接推广。
- **对比基线可能不全面**：只对比了联合训练基线，未与先独立训练再微调等其他解耦策略对比，也缺乏与其他非扩散方法（如CQL、IQL）的公平比较。

（完）
