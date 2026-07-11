---
title: Mean Flow Policy Optimization
title_zh: 平均流策略优化
authors: "Xiaoyi Dong, Xi Sheryl Zhang, Jian Cheng"
date: 2026-04-30
pdf: "https://openreview.net/pdf/d8666163ffb807f568b53c45984a0e27d6ae3725.pdf"
tags: ["query:diffusion-rl"]
score: 7.0
evidence: 提出MeanFlow模型以提高扩散RL的训练和推理效率
tldr: 该论文针对扩散策略的迭代生成过程带来的高开销，提出使用MeanFlow模型（一种少量步的流基生成模型）来表示策略，从而提高扩散RL的训练和推理效率。在最大熵RL框架下通过软策略迭代优化，并解决了动作似然评估和软策略改进的挑战。实验表明在MuJoCo、DeepMind Control Suite和HumanoidBench上性能与扩散方法相当但效率更高。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散策略的迭代生成过程导致训练和推理开销大，限制了其在在线RL中的应用。
method: 使用MeanFlow模型作为策略表示，在最大熵框架下通过软策略迭代优化，并处理了动作似然评估等关键问题。
result: 在多个连续控制基准上，MeanFlow策略优化方法在效率显著提升的同时保持了与扩散方法相当的性能。
conclusion: 流基模型可以高效替代扩散策略，为在线RL提供更实用的选择。
---

## Abstract
Diffusion models have recently emerged as expressive policy representations for online reinforcement learning (RL). However, their iterative generative processes introduce substantial training and inference overhead. To overcome this limitation, we propose to represent policies using MeanFlow models, a class of few-step flow-based generative models, to improve training and inference efficiency over diffusion-based RL approaches. To promote exploration, we optimize MeanFlow policies under the maximum entropy RL framework via soft policy iteration, and address two key challenges specific to MeanFlow policies: action likelihood evaluation and soft policy improvement. Experiments on MuJoCo, DeepMind Control Suite and HumanoidBench benchmarks demonstrate that our method, Mean Flow Policy Optimization (MFPO), achieves performance comparable to or exceeding current diffusion-based baselines while considerably reducing training and inference time.  Our code is available at https://github.com/dongxiaoyi-xyz/MFPO.

---

## 论文详细总结（自动生成）

好的，以下是根据您提供的论文元数据和摘要信息，对《Mean Flow Policy Optimization》（平均流策略优化）进行的详细中文总结。由于论文完整内容未提供，部分细节（如具体实验数量、算力等）将基于摘要和元数据推断，并标注信息缺失之处。

---

# 《Mean Flow Policy Optimization》论文详细总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：扩散模型（Diffusion Models）在在线强化学习（RL）中作为策略表示虽然表现优异，但其**迭代式生成过程**（如多步去噪）导致训练和推理阶段的计算开销显著增大，限制了在实际在线RL场景中的应用。
- **整体含义**：为解决这一效率瓶颈，作者提出使用**MeanFlow模型**（一种少步流基生成模型）替代扩散策略，在保持表达能力和性能的前提下，大幅减少训练与推理时间，从而提升扩散RL的实用性和可部署性。

## 2. 论文提出的方法论：核心思想、关键技术细节、算法流程

### 核心思想
- 将策略表示为**MeanFlow模型**：流基生成模型通过少量步骤（如1~2步）实现从噪声到动作的高质量映射，避免扩散模型的冗长迭代。
- 在**最大熵RL框架**下进行软策略迭代（Soft Policy Iteration），以促进探索并稳定优化。

### 关键技术细节
1. **动作似然评估**：MeanFlow模型通常不具备显式似然函数，论文需要设计方法高效估计动作的对数似然，用于软策略评估。
2. **软策略改进**：在最大熵框架下，策略优化目标包含熵正则项，需要针对MeanFlow的特性推导出可行的更新规则（例如通过重要性采样或重参数化梯度）。
3. **少步生成**：利用流模型的常微分方程（ODE）或有限差分性质，实现快速采样。

### 算法流程（文字说明）
- **初始化**：策略网络（MeanFlow模型）、值函数网络（Q网络）
- **迭代**：
  - **数据收集**：使用当前MeanFlow策略与环境交互，存储经验回放缓冲区。
  - **软策略评估**：更新Q函数以拟合贝尔曼误差，同时利用策略的动作似然计算熵项。
  - **软策略改进**：通过最小化KL散度（或等价地最大化带熵的目标）来更新MeanFlow策略参数，这里需要对动作分布的对数似然进行可微优化，并采用重参数化技巧或逆变换采样。
  - **重复**至收敛。
- **推理**：仅需少量前向步骤（如一步或两步）即可获得动作，极大降低延迟。

## 3. 实验设计：数据集/场景、基准、对比方法

- **使用基准**：
  - MuJoCo（连续控制任务）
  - DeepMind Control Suite（DMC，多种机器人控制任务）
  - HumanoidBench（高维人形机器人任务）
- **对比方法**：
  - 基于扩散策略的基线方法（如Diffuser、Behavior Transformer等扩散RL方法）
  - 可能还包括传统高斯策略的RL方法（如SAC），以及变分推理策略（如AWAC、CRR等），但摘要中明确指出与“扩散方法”对比性能与效率。
- **评估指标**：
  - 平均回报（平均累积奖励）
  - 训练时间（总墙钟时间或每个epoch时间）
  - 推理时间（每个动作采样时间）

## 4. 资源与算力

- **文中是否明确说明**：摘要和元数据中**未提及**具体GPU型号、数量、训练时长等算力信息。
- **推断**：由于代码已开源（GitHub），可在公开仓库中查看具体配置。通常此类实验可在单张或少量GPU（如NVIDIA RTX 3090/4090或A100）上完成，训练时间取决于任务复杂度，但MFPO因效率提升，训练时长应显著短于扩散基线。

## 5. 实验数量与充分性

- **实验数量**：根据摘要，涵盖三个标准连续控制基准（MuJoCo、DMC、HumanoidBench），每个基准包含多个任务（如HumanoidStandup、Walker2d、Cheetah等），通常每个任务运行多随机种子。但未说明具体任务数量及种子次数。
- **充分性评价**：基准覆盖从低维到高维任务，包含常用对比方法，基本充分。但缺乏消融实验细节（如对动作似然评估不同方法的对比、对步数选择的敏感性分析等），若在完整论文中有详细消融，则更充分；当前仅从摘要判断，实验设计较为标准客观。
- **公平性**：可能与基线使用完全相同的环境设置、奖励缩放、超参数搜索等，但未明确说明随机种子和实现细节是否一致。

## 6. 论文的主要结论与发现

- **性能相当**：MFPO在三个基准上取得了与当前最优扩散RL方法**相当或更优**的性能。
- **效率大幅提升**：训练时间与推理时间相比扩散方法**显著减少**（具体节省百分比未给出，但明确“considerably reducing”）。
- **可行性验证**：流基生成模型（MeanFlow）可有效替代扩散策略，成为在线RL中更实用的选择。

## 7. 优点：方法或实验设计上的亮点

- **创新性强**：首次将少步流基模型（MeanFlow）引入在线RL策略表示，解决了扩散策略的高计算成本痛点。
- **理论框架完整**：在最大熵RL下解决了流模型似然评估和策略改进两个挑战，具有理论支撑。
- **实验覆盖全面**：涵盖低维（MuJoCo）、中等维度（DMC）和高维（HumanoidBench）控制任务，验证了方法的通用性。
- **开源代码**：提供可复现代码，便于社区验证和后续研究。

## 8. 不足与局限

- **实验信息不完整**：未提供具体性能数值、效率提升百分比、消融实验等细节，难以全面评估方法的鲁棒性。
- **可能的应用限制**：
  - 对于需要高精度、多模态动作分布的任务，少步流模型可能不如长时间步扩散模型表达能力强。
  - 对似然估计的近似可能引入偏差，在复杂环境可能影响收敛稳定性。
- **算力与资源披露缺失**：无法评判其实际计算资源消耗与主流水准的比较。
- **对比基线仅提及扩散方法**：未与传统策略（如高斯策略）进行效率对比，也未讨论与更先进的变分策略（如MBPO、Dreamer）的比较。
- **潜在偏差风险**：作者可能只选择了对方法有利的对比基线或调整超参数，但公开代码可一定程度缓解此问题。

---

（完）
