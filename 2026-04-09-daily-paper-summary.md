# Daily Paper Summary — 2026-04-09
> 来源：ArXiv 最新提交（2026-04-07）｜主题：LLM训练/对齐、多模态、机器人、检索、Multi-agent

---

## 🧠 LLM / 语言模型

### 1. In-Place Test-Time Training
- **作者**: Guhao Feng, Shengjie Luo, Kai Hua, Ge Zhang, Di He, Wenhao Huang, Tianle Cai (ByteDance)
- **链接**: https://arxiv.org/abs/2604.06169
- **会议**: ICLR 2026 Oral
- **代码**: https://github.com/ByteDance-Seed/In-Place-TTT

**核心问题**: LLM 的"训练后部署"静态范式无法动态适应新信息，TTT 方案又面临架构不兼容、计算低效等障碍。

**方法**:
- 将 MLP 最后的投影矩阵作为 fast weights，无需从头重训即可 drop-in 式赋予 LLM 测试时训练能力。
- 将通用重建目标替换为与下一 token 预测对齐的理论化目标，配合高效 chunk-wise 更新机制，支持上下文并行。

**效果**: 4B 模型在 128k 上下文任务中持续优于竞争 TTT 方法；从头预训版本同样优于对比基线。

---

### 2. Target Policy Optimization (TPO)
- **作者**: Jean Kaddour
- **链接**: https://arxiv.org/abs/2604.06159
- **代码**: https://github.com/JeanKaddour/tpo

**核心问题**: 标准策略梯度方法将"选哪些 completion"和"参数如何更新"耦合在一起，导致在稀疏奖励下容易过冲/欠冲。

**方法**:
- 将两个问题解耦：先构造目标分布 $q_i \propto p_i^\text{old}\exp(u_i)$，再用交叉熵拟合策略到该目标分布。
- 梯度形式为 $p^\theta - q$，当策略匹配目标分布时自然归零。

**效果**: 稀疏奖励下显著优于 PG/PPO/GRPO/DG，在简单任务上持平。

---

### 3. Toward Consistent World Models with LSE-MTP
- **作者**: Qimin Zhong, Hao Liao et al.
- **链接**: https://arxiv.org/abs/2604.06155
- **会议**: ACL 2026 Main

**核心问题**: Multi-Token Prediction (MTP) 标准训练会在隐空间产生"结构性幻觉"——模型学会非法捷径，违反环境约束。

**方法**:
- 理论证明 MTP 通过梯度耦合推动内部信念状态收敛（表示收缩性）。
- 提出 Latent Semantic Enhancement MTP (LSE-MTP)，将预测锚定到真实隐状态轨迹，弥合离散 token 与连续状态表示之间的鸿沟。

**效果**: 减少结构性幻觉，提升表示对齐和扰动鲁棒性。

---

### 4. Exclusive Unlearning
- **作者**: Mutsumi Sasaki, Kouta Nakayama et al.
- **链接**: https://arxiv.org/abs/2604.06154

**核心问题**: 现有机器遗忘方法需要逐一列举要删除的有害内容，面对多样化有害内容时难以全面覆盖。

**方法**:
- 反向遗忘策略：不列举要遗忘的内容，而是"大范围遗忘目标保留领域以外的所有知识和表达"。

**效果**: 对多种输入（包括 jailbreak）保证安全性，同时保留医学/数学等特定领域的响应能力。

---

## 👁️ 多模态 / 视觉

### 5. HaloProbe: Bayesian Detection and Mitigation of Object Hallucinations in VLMs
- **作者**: Reihaneh Zohrabi, Hosein Hasani, Akshita Gupta, Mahdieh Soleymani Baghshah, Anna Rohrbach, Marcus Rohrbach
- **链接**: https://arxiv.org/abs/2604.06165

**核心问题**: 基于 attention weight 的幻觉检测存在隐藏混淆因子（token 位置、对象重复），导致 Simpson's 悖论——聚合统计后 attention 趋势反转或消失。

**方法**:
- HaloProbe：贝叶斯框架，分解外部描述统计和内部解码信号，估计 token 级幻觉概率。
- 用平衡训练隔离内部证据，结合外部特征学习先验，恢复真实后验。
- 作为外部评分信号用于非侵入式解码引导，不修改模型内部。

**效果**: 比现有侵入式方法更有效地减少幻觉，同时保持输出流畅度。

---

### 6. MMEmb-R1: Reasoning-Enhanced Multimodal Embedding
- **作者**: Yuchi Wang, Haiyang Yu, Weikang Bian et al.
- **链接**: https://arxiv.org/abs/2604.06156

**核心问题**: 直接将 CoT 推理引入 Embedding 学习面临两个挑战：(1) 实例级推理与成对对比监督的结构性错位；(2) 推理并非对所有样本都有益，强制推理反而会遮盖简单样本的语义信号。

**方法**:
- 将推理建模为隐变量，用反事实干预识别对 query-target 对齐真正有益的推理路径（pair-aware 推理选择）。
- 强化学习自适应控制推理触发时机，仅在必要时调用推理。

**效果**: 4B 参数在 MMEB-V2 达到 71.2 分 SOTA，同时显著降低推理开销和推理延迟。

---

## 🤖 机器人 / 具身智能

### 7. Action Images: End-to-End Policy Learning via Multiview Video Generation
- **作者**: Haoyu Zhen, Zixian Gao, Qiao Sun, Yilin Zhao, Yuncong Yang, Yilun Du, Tsun-Hsuan Wang, Yi-Ling Qiao, Chuang Gan
- **链接**: https://arxiv.org/abs/2604.06168
- **项目页**: https://actionimages.github.io/

**核心问题**: 现有 World Action Models 依赖独立动作模块或非像素对齐的动作表示，难以充分利用预训练视频模型的知识，且跨视角/环境迁移受限。

**方法**:
- 将 7-DoF 机器人动作翻译为"动作图像"：像素对齐的多视角动作视频，显式追踪机械臂运动。
- 该表示让视频主干直接充当零样本策略，无需独立的 policy head 或动作模块。
- 同一统一模型同时支持视频-动作联合生成、动作条件视频生成和动作标注。

**效果**: 在 RLBench 和真实场景评测中取得最强零样本成功率。

---

## 🔍 检索 / RAG

### 8. Data, Not Model: Explaining Bias toward LLM Texts in Neural Retrievers
- **作者**: Wei Huang, Keping Bi, Yinqiong Cai, Wei Chen, Jiafeng Guo, Xueqi Cheng
- **链接**: https://arxiv.org/abs/2604.06163

**核心问题**: 神经检索器普遍偏好 LLM 生成的文本，此前被认为是模型的固有缺陷。

**发现**: 偏好源于检索训练数据中正/负样本之间的系统性非语义差异（流畅度、术语特异性等），与 LLM/人类文本的差异高度一致；embedding 空间中偏差方向与"人类→LLM 文本"方向对齐。

**方法**:
1. 减少训练数据中的人为差异
2. 在 embedding 空间减去偏差向量的投影

**效果**: 两种方案均大幅降低来源偏差。

---

## 🛠️ Multi-agent / 工具

### 9. Paper Circle: An Open-source Multi-agent Research Discovery and Analysis Framework
- **作者**: Komal Kumar, Aman Chadha, Salman Khan, Fahad Shahbaz Khan, Hisham Cholakkal
- **链接**: https://arxiv.org/abs/2604.06170
- **会议**: ACL 2026 Main (Oral)
- **网站**: https://papercircle.vercel.app/ | **代码**: https://github.com/MAXNORM8650/papercircle

**方法**:
- **Discovery Pipeline**: 多源离线/在线检索 + 多标准评分 + 多样性感知排序 + 结构化输出
- **Analysis Pipeline**: 将论文转化为带类型节点（概念、方法、实验、图表）的结构化知识图谱，支持图感知 QA 和覆盖度验证
- 所有步骤输出 JSON/CSV/BibTeX/Markdown/HTML，完全可复现

**评测**: 在论文检索和综述生成上 benchmark，报告 hit rate、MRR、Recall@K；更强的 agent 模型带来一致提升。
