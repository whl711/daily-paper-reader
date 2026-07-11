---
title: "From Generative to Episodic: Sample-Efficient Replicable Reinforcement Learning"
title_zh: 从生成到情境：样本高效的可重复强化学习
authors: "Max Hopkins, Sihan Liu, Christopher Ye, Yuichi Yoshida"
date: 2026-04-30
pdf: "https://openreview.net/pdf/e692640cf859e714bdbcbbd7a4ca5fe02364c979.pdf"
tags: ["query:rl"]
score: 5.0
evidence: 样本高效的可重复强化学习理论
tldr: 强化学习的可重复性问题长期存在，现有理论在探索场景下样本复杂度界限过高。本文针对情境式强化学习，提出了新的可重复算法并给出了更紧的样本复杂度上界，缩小了与批处理设置的差距，推动了可重复强化学习理论的发展。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 强化学习中的可重复性研究存在样本复杂度高的问题。
method: 提出新的可重复强化学习算法，利用探索策略生成数据再转换为情境式学习。
result: 获得了比现有方法更紧的样本复杂度上界。
conclusion: 该工作从理论上提升了可重复强化学习的样本效率。
---

## Abstract
The epidemic failure of replicability across empirical science and machine learning has recently motivated the formal study of replicable learning algorithms [Impagliazzo et al. (2022)]. In contrast to batch settings (i.e. data comes from a fixed i.i.d. source) where the cost of replicability is relatively well understood, there remain significant gaps in our knowledge for control settings like reinforcement learning where an agent must interact directly with a shifting environment. Indeed, there is a large gap between the best upper bound of $\tilde{O}(S^7 A^7)$ [Eaton et al. (2023)] for RL with exploration, and $\tilde{O}(S^2 A^2)$ [Karbasi et al. (2023)] for the RL `batch' setting. This gap raises a key question in the broader theory of replicability: Is replicable exploration inherently more expensive than batch learning? Is sample-efficient replicable RL even possible?

In this work, we (nearly) resolve this problem (for low-horizon tabular MDPs): exploration is not a significant barrier to replicable learning! Our main result is a replicable RL algorithm on $\tilde{O}(S^2A)$ samples, bridging the gap between the generative and episodic settings. We complement this with a lower bound in the episodic setting of $\tilde{\Omega}(S^2)$ showcasing the near-optimality of our algorithm with respect to the state space $S$.

---

## 论文详细总结（自动生成）

# 详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **研究动机**：可重复性危机在实证科学和机器学习中日益严重，催生了可重复学习算法[Impagliazzo et al. (2022)]的正式研究。在强化学习（RL）的控制场景中，智能体必须直接与动态环境交互，其可重复性的样本复杂度远高于批处理（batch）设置（固定i.i.d.数据源）。例如，探索式RL的最佳上界为$\tilde{O}(S^7 A^7)$ [Eaton et al. (2023)]，而RL批处理设置仅为$\tilde{O}(S^2 A^2)$ [Karbasi et al. (2023)]，存在巨大差距。
- **核心问题**：可重复的探索是否天生就比批处理学习更昂贵？是否存在样本高效的可重复强化学习？
- **整体含义**：本文（几乎）解决了低horizon情境式表格MDP中的这一问题，表明探索并非可重复学习的显著障碍。主要结果为$\tilde{O}(S^2A)$样本量的可重复RL算法，弥合了生成式（generative）设置与情境式（episodic）设置之间的差距，并配以下界$\tilde{\Omega}(S^2)$，证明了算法在状态空间$S$上的近最优性。

## 2. 论文提出的方法论
- **核心思想**：将探索过程生成的数据转换为情境式学习，实现样本高效的可重复RL。
- **关键技术细节**（基于摘要推断）：
  - 针对低horizon情境式表格MDP，设计了一种新的可重复RL算法。
  - 算法利用探索策略生成数据，再将这些数据转化为情境式学习所需的格式，从而绕过传统探索中可重复性带来的指数级样本开销。
  - 通过精巧的“可重复探索”机制，使得在相同环境交互次数下，能以高概率获得相同（或近似）的策略输出。
- **算法流程**（文字说明）：
  - 步骤1：智能体在有限Horizon的表格MDP中执行探索策略，收集交互轨迹。
  - 步骤2：将收集到的轨迹转换为离线数据，并应用可重复批处理学习技术（如可重复价值迭代或可重复Q学习）。
  - 步骤3：输出一个可重复的策略，使得在不同随机种子下重复实验时，策略性能的差异可控。
- **公式**：主要理论结果为样本复杂度上界$\tilde{O}(S^2A)$和下界$\tilde{\Omega}(S^2)$。

## 3. 实验设计
- **数据集/场景**：论文为纯理论工作，未涉及具体实验（数据集、benchmark或方法对比）。根据摘要和元数据，没有报告任何数值实验或仿真结果。
- **Benchmark**：理论对比了现有可重复RL算法的样本复杂度上界（Eaton et al. 2023的$\tilde{O}(S^7A^7)$和Karbasi et al. 2023的$\tilde{O}(S^2A^2)$）。
- **对比方法**：未显式列出，但隐含对比了前期可重复RL算法（如Eaton et al., Karbasi et al.）。

## 4. 资源与算力
- 论文未提及任何GPU型号、数量、训练时长等算力信息。作为理论文章，不涉及实验计算资源消耗。

## 5. 实验数量与充分性
- 本文为理论分析论文，无实验。因此无法评估实验数量、消融实验、公平性等。理论推导的充分性取决于数学证明，文中应提供了完整的理论分析，但摘要未给出详细证明步骤。

## 6. 论文的主要结论与发现
- 探索并非可重复强化学习的显著障碍；在低horizon表格MDP中，可重复RL可以达到接近批处理设置的样本效率。
- 提出了样本复杂度为$\tilde{O}(S^2A)$的可重复RL算法，几乎匹配下界$\tilde{\Omega}(S^2)$（忽略动作空间A的对数因子），从而极大地缩小了与生成式/批处理设置（$\tilde{O}(S^2A^2)$）的差距。
- 主要理论突破：证明了情境式可重复RL可以在多项式样本下实现，解决了开放问题。

## 7. 优点
- **理论贡献突出**：首次在情境式RL中实现近最优的可重复样本复杂度，弥合了探索式与批处理设置之间的鸿沟。
- **紧下界**：给出$\tilde{\Omega}(S^2)$下界，证明了算法在状态空间S上的最优性（忽略对数因子）。
- **方法新颖**：将生成式探索转化为情境式学习，为可重复RL提供了新思路。
- **问题重要性**：解决了可重复性学习理论中的一个核心问题，对RL的可信度有重要意义。

## 8. 不足与局限
- **理论假设较强**：仅适用于低horizon表格MDP，未扩展到无限horizon、函数逼近或连续状态/动作空间。
- **缺乏实验验证**：纯理论工作，未在标准RL基准环境（如Atari, MuJoCo）中验证实际可重复性效果。
- **实际应用距离**：样本复杂度仍含隐藏常数，实际部署时可能仍需大量样本。
- **仅关注可重复性**：未与其他RL鲁棒性（如对抗鲁棒、分布偏移）结合。

（完）
