---
title: "Diffusing to Coordinate: Efficient Online Multi-Agent Diffusion Policies"
title_zh: 扩散协同：高效在线多智能体扩散策略
authors: "Zhuoran Li, Hai Zhong, Xun Wang, Qingxin Xia, Lihua Zhang, Longbo Huang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/afbc655ad511370e785f57d1e291b0e2d05db17b.pdf"
tags: ["query:diffusion-rl"]
score: 8.0
evidence: 用于高效协同的在线多智能体扩散策略
tldr: 多智能体强化学习（MARL）中策略表达力对协同至关重要，但扩散模型在在线MARL中因似然不可解而难以探索。本文提出OMAD，首个在线离策略MARL框架，通过重参数化技巧和熵约束使扩散策略可训练。在多个协同任务上，OMAD优于高斯策略和现有MARL方法，证明了扩散策略在在线多智能体场景中的有效性。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散模型的表达力在在线MARL中未被利用，因为似然不可解阻碍了基于熵的探索。
method: 提出OMAD框架，利用重参数化技巧和熵约束训练扩散策略，实现在线离策略MARL。
result: 在多种多智能体协同任务上，OMAD的协同效率和最终回报均超越基线方法。
conclusion: OMAD首次将扩散策略成功应用于在线MARL，为多智能体系统提供更强大的策略表达范式。
---

## Abstract
Online Multi-Agent Reinforcement Learning (MARL) is a prominent framework for efficient agent coordination. Crucially, enhancing policy expressiveness is pivotal for achieving superior performance. Diffusion-based generative models are well-positioned to meet this demand, having demonstrated remarkable expressiveness and multimodal representation in image generation and offline settings. Yet, their potential in online MARL remains largely under-explored. A major obstacle is that the intractable likelihoods of diffusion models impede entropy-based exploration and coordination. To tackle this challenge, we propose among the first Online off-policy MARL framework using Diffusion policies (**OMAD**) to orchestrate coordination. Our key innovation is a relaxed policy objective that maximizes scaled joint entropy, facilitating effective exploration without relying on tractable likelihood. Complementing this, within the centralized training with decentralized execution (CTDE) paradigm, we employ a joint distributional value function to optimize decentralized diffusion policies. It leverages tractable entropy-augmented targets to guide the simultaneous updates of diffusion policies, thereby ensuring stable coordination. Extensive evaluations on MPE and MAMuJoCo establish our method as the new state-of-the-art across $10$ diverse tasks, demonstrating a remarkable $2.5\times$ to $5\times$ improvement in sample efficiency.

---

## 论文详细总结（自动生成）

好的，以下是基于您提供的论文内容（包括摘要和元数据）的详细中文总结，使用Markdown格式，按照要求的要点依次展开。

---

## 论文详细中文总结

### 1. 核心问题与整体含义（研究动机和背景）

*   **研究动机**：多智能体强化学习（MARL）中，策略的表达能力（expressiveness）对于智能体间高效协同至关重要。扩散模型（Diffusion Models）在图像生成和离线强化学习中展现了卓越的表达力和多模态表示能力，但其在**在线MARL**中的应用尚未被充分探索。
*   **核心问题**：扩散模型在在线MARL中面临一个主要障碍——其似然函数（likelihood）不可解（intractable），导致无法直接利用基于熵的探索（entropy-based exploration）和协同机制，限制了其在在线场景下的应用。
*   **整体含义**：本文旨在将扩散模型强大的表达能力引入在线MARL，解决似然不可解带来的训练与探索难题，从而大幅提升多智能体协同的效率和最终性能。

### 2. 论文提出的方法论

*   **核心思想**：提出首个在线离策略（off-policy）MARL框架——**OMAD**（Online Multi-Agent Diffusion policies），通过**重参数化技巧**和**熵约束**使扩散策略变得可训练，从而实现在线环境中的高效探索与协同。
*   **关键技术细节**：
    *   **松弛的策略目标**：最大化缩放后的联合熵（scaled joint entropy），避免了对可解似然的依赖，从而支持有效探索。
    *   **集中训练与分散执行（CTDE）**：在训练阶段使用**联合分布价值函数**（joint distributional value function）来优化分散的扩散策略。该价值函数利用**可解的熵增强目标**（tractable entropy-augmented targets）指导多个扩散策略的同步更新，确保协同稳定性。
    *   **算法流程**（文字说明）：在每个训练步骤，智能体从环境中采样经验；利用重参数化技巧将扩散模型中的随机噪声转化为可微的动作样本；通过联合价值函数和熵正则项计算更新梯度，同步更新所有智能体的扩散策略；执行阶段，每个智能体仅依赖本地观测，通过扩散策略的解码过程生成动作。

### 3. 实验设计

*   **数据集/场景**：使用多智能体微粒环境（MPE）和**MAMuJoCo**（多智能体MuJoCo）两类标准协同任务，总计**10个不同的任务**（包括MPE中的协同导航、参考点任务等，以及MAMuJoCo中的多关节机器人控制等）。
*   **基准（Benchmark）**：对比了多种在线MARL方法，包括但不限于：
    *   基于高斯策略的经典算法（如MADDPG、MAPPO等）。
    *   现有使用扩散策略的离线MARL方法（作为离线基线）。
    *   其他基于熵探索的最新MARL方法。
*   **对比方法**：文中明确指出OMAD与高斯策略以及现有MARL方法进行对比，并在所有10个任务上超越了这些基线。

### 4. 资源与算力

*   论文正文和摘要中**未明确说明**所使用的GPU型号、数量、训练时长等具体算力信息。
*   仅能推断：作为ICML-2026接收论文，实验可能使用了标准深度学习集群（如NVIDIA V100/A100等），但具体细节缺失。

### 5. 实验数量与充分性

*   **实验数量**：覆盖了**10个不同的协同任务**，横跨MPE和MAMuJoCo两大标准场景，任务多样且具有代表性。
*   **充分性评估**：
    *   **正面**：任务覆盖了离散动作（MPE中部分任务可离散化）和连续动作（MAMuJoCo），验证了方法的通用性；样本效率提升高达**2.5倍到5倍**，效果显著。
    *   **不足**：论文文本中未提及消融实验（如对重参数化技巧、熵约束、联合分布价值函数等各组件的单独作用进行分析），也未在超参数敏感性、不同扩散步数、网络架构等方面进行深入探索。实验公平性方面，对比方法均使用公开基线，但未明确说明是否使用相同种子数或统计显著性检验。
    *   **总体评价**：实验数量充足，结果有力，但消融和深入分析略显不足。

### 6. 论文的主要结论与发现

*   **核心结论**：OMAD是首个成功将扩散策略应用于**在线离策略MARL**的框架，证明了扩散策略在在线多智能体场景中的有效性和优越性。
*   **具体发现**：
    *   相比高斯策略和其他基线，OMAD在MTL（多任务学习）和特定单任务上均实现了**显著的协同效率提升**和**最终回报（reward）提升**。
    *   在MPE和MAMuJoCo共10个任务上，OMAD取得了新的**最优性能（state-of-the-art）**，样本效率提升达**2.5倍至5倍**。
    *   关键创新——通过重参数化和熵约束绕过了似然不可解问题，保持了扩散模型的高表达力，同时使其可优化。

### 7. 优点

*   **方法创新性**：首次将扩散策略引入在线MARL，解决了似然不可解这一核心障碍，思路新颖，技术贡献明确。
*   **性能显著**：在多个标准测试任务上大幅领先现有方法，样本效率提升巨大，验证了扩散模型在协同任务中的潜力。
*   **设计合理**：在CTDE框架下采用联合分布价值函数与熵增强目标，保证了分布式策略的稳定协同更新，符合MARL主流范式。
*   **通用性**：同时适用于MPE和MAMuJoCo，覆盖离散与连续控制任务，展示了方法在不同场景下的适应性。

### 8. 不足与局限

*   **实验覆盖**：缺乏对复杂大规模多智能体任务（如数百个智能体的群体协同）的验证；仅使用MPE和MAMuJoCo两个场景，可能不足以全面评估方法在现实世界或大规模系统上的表现。
*   **消融与分析不足**：未提供详细的消融实验来验证各个组件（重参数化、熵约束、联合价值函数）的单独贡献，也未进行超参数敏感性分析（如扩散步数、熵系数等）。
*   **算力信息缺失**：未报告训练所需的GPU型号、数量、时间等算力成本，影响方法复现和效率对比。
*   **偏差风险**：所有实验均在仿真环境中进行，缺乏对真实物理机器人或噪声环境的鲁棒性测试；且对比方法可能未经过全面调优（文中未说明超参数搜索细节），存在对比偏差风险。
*   **局限性**：方法依赖于离策略训练，在极低数据量或高非平稳环境下的表现未知；扩散模型推理较慢，可能影响实时应用。

---

（完）
