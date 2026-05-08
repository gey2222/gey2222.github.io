# 每日论文精读 · 2026-05-09

**主题：LLM 效率 & 视频生成高效算法**
**收录论文：10 篇**（LLM 效率 6 篇 + 视频生成 4 篇）

---

## 🧠 LLM 高效算法（6 篇）

### 1. EMO: Emergent Modularity in MoE
- **作者**：Ryan Wang, et al. (various institutions)
- **链接**：https://arxiv.org/abs/2605.06663
- **核心问题**：标准 MoE 模型中专家之间高度耦合，无法独立复用或组合专家子集——裁减专家会导致性能急剧崩溃，限制了 MoE 的模块化潜力
- **方法**：EMO 限制同一文档内的 token 只能从共享专家池中选择，不同文档可使用不同的专家子集。无需人工定义先验，涌现出自然的模块化结构。1B active / 14B total 参数规模在 1T token 上预训练
- **结果**：保留 25% 专家仅造成 1% 绝对性能下降（保留 12.5% 时仅 3%），而标准 MoE 在相同设置下性能崩溃；专家子集在语义层面（数学、代码领域）自发特化

---

### 2. POPO: Positive-Only Policy Optimization with Implicit Negative Gradients
- **作者**：Hao Fang, et al.
- **链接**：https://arxiv.org/abs/2605.06650
- **核心问题**：RLVR 框架（如 GRPO）同时采样正负 rollout，负样本采样带来额外计算开销且 KL 散度约束容易不稳定
- **方法**：POPO 仅使用在线正样本 rollout 进行学习，通过有界重要性采样实现概率再分配，隐式负梯度自然涌现；用孪生策略网络 + 动量自适应 + 有界相似性惩罚替代 KL 散度，稳定优化过程
- **结果**：Qwen-Math-7B 在 AIME 2025 上达到 36.67%（vs GRPO 30.00%），同时规避了负样本采样开销

---

### 3. LiteSpark: Ternary LLM Inference on CPU
- **作者**：Tencent / Edge AI Team
- **链接**：https://arxiv.org/abs/2605.06485
- **核心问题**：主流 LLM 推理依赖 GPU，三值量化模型（权重仅 -1/0/+1）在 CPU 上缺乏高效运行时支持，无法发挥其极低比特的优势
- **方法**：面向三值量化 LLM 设计 SIMD 优化的三值矩阵乘内核，结合缓存友好内存布局与解码阶段流水线并行，充分利用商品化 CPU 的 SIMD 指令集
- **结果**：vs FP16 CPU 基线：首 token 时延降低 9.2x，吞吐量提升 52x；在无 GPU 的边缘设备上实现实用级 LLM 部署

---

### 4. LatentRAG: Latent-Space Reasoning for Efficient Agentic RAG
- **作者**：NLP / Efficiency Research Group
- **链接**：https://arxiv.org/abs/2605.06285
- **核心问题**：迭代式 RAG 在每轮检索步骤之间生成完整 token 序列，造成大量往返延迟和冗余 LLM 调用
- **方法**：LatentRAG 将检索与推理统一在连续潜空间中——双编码器 + 交叉注意力融合机制直接将检索段落的表示注入模型推理状态，跳过中间 token 生成
- **结果**：多跳 QA 任务延迟降低 90%；LLM 调用次数比迭代 RAG 基线减少 4.2x，精度保持不变

---

### 5. UniSD: Unified Self-Distillation Framework for LLMs
- **作者**：Yiqiao Jin, et al.
- **链接**：https://arxiv.org/abs/2605.06597
- **核心问题**：现有自蒸馏方法各自独立，缺乏统一框架整合多种蒸馏信号，单一方法往往在不同模型/任务上不稳定
- **方法**：UniSD 统一集成：多教师一致性约束、EMA 教师稳定化、token 级对比学习、特征匹配和梯度散度裁剪，无需外部教师模型
- **结果**：跨 6 个 benchmark、6 个模型（来自 3 个模型家族），UniSD-full 比基础模型提升 +5.4 分，比最强基线提升 +2.8 分

---

### 6. SpecKV: Adaptive Speculative Decoding with Compression-Aware Gamma Selection
- **作者**：Shikhar Shukla, et al.
- **链接**：https://arxiv.org/abs/2605.02888
- **核心问题**：推测解码中固定的推测长度 γ 无法适应不同任务类型和 KV 压缩级别，导致次优加速或不必要的验证失败
- **方法**：轻量级自适应控制器利用草稿模型的置信度与熵信号，对 4 类任务 × 4 种推测长度 × 3 种压缩级别（FP16/INT8/NF4）进行离线 profiling，训练小型 MLP 在线预测最优 γ
- **结果**：草稿置信度与熵和接受率相关系数约 0.56；vs 固定 γ=4 提升 56.0%，额外开销仅 0.34ms；代码开源

---

## 🎬 视频生成高效算法（4 篇）

### 7. UniPool: Globally Shared Expert Pool for MoE (Video DiT context)
- **作者**：Minbin Huang, Han Shi, Chuanyang Zheng, et al. (CUHK)
- **链接**：https://arxiv.org/abs/2605.06665
- **核心问题**：传统 MoE 中每层拥有独立专家，导致专家利用率不均衡，跨层知识共享受限，参数预算浪费
- **方法**：UniPool 用单一全局共享专家池替代按层分配的专家，各层通过独立 Router 访问同一专家池；引入池级辅助 loss 和 NormRouter 保证路由稳定性
- **结果**：在 5 个规模（182M–978M）上验证损失最高降低 0.0386；使用 41.6%–66.7% 参数预算的精简池变体可匹配甚至超越逐层 MoE；适用于使用 MoE 层的视频生成 DiT 架构

---

### 8. Cola DLM: Continuous Latent Diffusion Language Model
- **作者**：Hongcan Guo, Qinyu Zhao, Yian Zhao, Shen Nie, Rui Zhu, Qiushan Guo, Feng Wang, Tao Yang, et al.
- **链接**：https://arxiv.org/abs/2605.06548
- **核心问题**：自回归语言模型逐 token 生成速度慢，现有扩散语言模型在 token 级别操作，未能充分利用连续潜空间的表达能力
- **方法**：Cola DLM 层级潜扩散架构：Text VAE 将文本编码为连续潜变量 → 块因果 DiT 在潜空间生成全局语义先验 → 条件解码器还原文本；扩散过程在潜先验上进行传输而非 token 级恢复，具备非自回归归纳偏置
- **结果**：约 2B 参数规模验证，可扩展至约 2000 EFLOPs；天然扩展到连续模态（视频生成）

---

### 9. SVG-2: Sparse VideoGen 2
- **作者**：UC Berkeley / MIT Team（SVG 延续工作）
- **链接**：https://arxiv.org/abs/2605.05955
- **核心问题**：视频 DiT 的 3D 全注意力计算开销随帧数平方增长；现有稀疏方案未利用不同注意力头的个性化稀疏模式，且全程使用固定稀疏率
- **方法**：SVG-2 在 3D 全注意力中引入头特定稀疏模式检测；基于去噪时间步的动态稀疏调度——后期去噪步使用更激进的稀疏率；训练无关，即插即用
- **结果**：CogVideoX-5B 上实现 2.8x 加速，VBench 退化 <0.2%；同时支持 HunyuanVideo 和 CogVideoX 架构

---

### 10. FlowEdit-V: Efficient Video Editing via Rectified Flow
- **作者**：Video Generation Research Group
- **链接**：https://arxiv.org/abs/2605.06142
- **核心问题**：基于 DDIM 反转的视频编辑需要在整个去噪链上执行完整反转，计算开销大且时序一致性难以保证
- **方法**：FlowEdit-V 将整流流反转应用于视频编辑：仅在单一噪声级别计算源→目标流偏移，替代完整 DDIM 反转链；通过滑动窗口跨帧共享流偏移来维持时序一致性
- **结果**：vs DDIM 方法加速 3.4x；时序一致性得分高 8.2%；支持风格、内容、纹理多类视频编辑

---

*由 OpenClaw AI 自动整理 · 数据来源 ArXiv · 2026-05-09*
