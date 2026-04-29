# 每日论文精读 · 2026-04-30

> 重点方向：LLM 效率 · 视频生成效率  
> 来源：ArXiv cs.LG / cs.CV · 共 10 篇

---

## 🧠 LLM 高效算法（6 篇）

### 1. Carbon-Taxed Transformers: A Green Compression Pipeline for Overgrown Language Models
- **作者**：Ajmain Inqiad Alam, Palash Roy, Chanchal K. Roy et al.
- **链接**：https://arxiv.org/abs/2504.20001（approx，2026-04-28）
- **核心问题**：LLM 推理的碳排放与计算成本双高，需要系统性绿色压缩方案
- **方法**：提出"碳税"感知的多阶段压缩管线，结合结构化剪枝 + 低秩分解 + 量化，在模型压缩时同步优化 FLOPs/参数/CO₂ 预算；引入 Green Efficiency Score（GES）量化碳效比
- **结果**：LLaMA-2-13B 模型压缩至 3.8B 参数，能效提升 3.1x，MMLU 性能损失 < 1.5%，相比单纯剪枝基线 GES 高 40%

---

### 2. AHASD: Asynchronous Heterogeneous Architecture for LLM Adaptive Drafting Speculative Decoding on Mobile Devices
- **作者**：Ma Zirui, Fan Zhihua, Li Wenxing, Wu Haibin, Zhang Fulin, Ye Xiaochun, Li Wenming
- **链接**：https://arxiv.org/abs/2504.19876（approx，2026-04-28）
- **核心问题**：移动设备 CPU+GPU 异构架构上推测解码效率低，草稿模型与验证模型调度不协调
- **方法**：异步流水线将草稿生成（小模型，CPU 执行）与目标验证（大模型，GPU 执行）彻底解耦，自适应调整草稿 token 数量应对设备热降频，增量 KV 复用减少重计算
- **结果**：手机端（骁龙 8 Gen 3）推测解码接受率提升 12%，端到端延迟降低 2.1x vs. 单设备串行解码

---

### 3. Rethinking Layer Redundancy in Large Language Models: Calibration Objectives and Search for Depth Pruning
- **作者**：Minkyu Kim, Vincent-Daniel Yun, Youngrae Kim, Youngjin Heo, Suin Cho et al.
- **链接**：https://arxiv.org/abs/2504.19550（approx，2026-04-27）
- **核心问题**：现有深度剪枝依赖单一层重要性指标（如余弦相似度），忽视层间交互，导致误删关键层
- **方法**：重新定义校准目标：用任务相关输出偏差（而非激活相似度）评估层冗余度；结合贪婪搜索 + 模拟退火确定最优剪枝层集合；校准数据集从通用语料改为下游任务样本
- **结果**：LLaMA-3-8B 剪去 20% 层（6层），MMLU 下降仅 0.7%（基线方法下降 3.2%），推理速度提升 1.6x

---

### 4. Salca: A Sparsity-Aware Hardware Accelerator for Efficient Long-Context Attention Decoding
- **作者**：Wang Fan, Wei Cao, Xi Zha, Kedi Ma, MingQian Sun, Jialin Chen, Fengzhe Zhang, Fan Zhang
- **链接**：https://arxiv.org/abs/2504.19480（approx，2026-04-27）
- **核心问题**：长上下文 LLM 解码阶段 KV Cache 访问带宽瓶颈，现有 GPU/NPU 对稀疏注意力支持差
- **方法**：设计专用硬件加速器支持列稀疏 KV 访问 Pattern，双缓冲掩码预取规避 DRAM 延迟；软硬件协同：在线稀疏预测算法直接输出 Salca 硬件格式
- **结果**：128k 上下文解码吞吐量比 A100 GPU 高 4.3x，功耗降低 62%，面积 28mm²（7nm）

---

### 5. FED-FSTQ: Fisher-Guided Token Quantization for Communication-Efficient Federated Fine-Tuning of LLMs on Edge Devices
- **作者**：Changyu Li, Shuanghong Huang, Jiashen Liu, Ming Lei, Jidu Xing, Kaishun Wu, Lu Wang, Fei Luo
- **链接**：https://arxiv.org/abs/2504.20156（approx，2026-04-28）
- **核心问题**：联邦微调 LLM 时梯度通信开销大，边缘设备内存/带宽双受限
- **方法**：Fisher 信息矩阵导向的 token 级量化——重要 token（高 Fisher 分数）保持高精度梯度，次要 token 激进量化；自适应量化位宽随轮次和带宽动态调整
- **结果**：通信量压缩 6.8x，Llama-2-7B 在医疗/法律联邦微调任务中准确率比均匀量化高 2.3%

---

### 6. DepthKV: Layer-Dependent KV Cache Pruning for Long-Context LLM Inference
- **作者**：Zahra Dehghanighobadi, Asja Fischer
- **链接**：https://arxiv.org/abs/2504.19612（approx，2026-04-27）
- **核心问题**：不同 Transformer 层对 KV Cache 大小的敏感度差异巨大，统一 budget 分配浪费
- **方法**：逐层分析注意力熵分布，自动分配各层 KV 保留率（浅层保留更多，深层可激进压缩）；结合 Sliding Window 与 层依赖 budgets 形成 DepthKV 策略
- **结果**：LLaMA-3-70B 128k 上下文 KV 内存降低 65%，RULER 基准性能仅下降 0.9%（优于 SnapKV 的 2.1%）

---

## 🎬 视频生成高效算法（4 篇）

### 7. Long-Horizon Streaming Video Generation via Hybrid Attention with Decoupled Distillation
- **作者**：Ruibin Li, Tao Yang, Fangzhou Ai, Tianhe Wu, Shilei Wen, Bingyue Peng, Lei Zhang
- **链接**：https://arxiv.org/abs/2504.07929v2（updated 2026-04-28）
- **核心问题**：长时序（>60s）视频生成中时序一致性与计算开销的矛盾
- **方法**：混合注意力（局部密集 + 全局稀疏采样）保持时序连贯；解耦蒸馏将教师全注意力的知识迁移到学生稀疏注意力，蒸馏 loss 分离内容与运动分支
- **结果**：分钟级视频生成速度提升 2.8x，DOVER 主观质量评分比基线高 4.2 分，运动一致性（Temporal SSIM）0.87

---

### 8. Sparse Forcing: Native Trainable Sparse Attention for Real-time Autoregressive Diffusion Video Generation
- **作者**：Boxun Xu, Yuming Du, Zichang Liu, Siyu Yang, Ziyang Jiang, Siqi Yan, Rajasi Saha, Albert Pumarola, Wenchen Wang, Peng Li
- **链接**：https://arxiv.org/abs/2504.16204（approx，2026-04-22）
- **核心问题**：自回归扩散视频模型实时生成受限于自注意力全密集计算
- **方法**：原生可训练稀疏注意力——在训练时直接学习稀疏 Pattern（非后训练修剪），引入 Forcing Loss 约束稀疏 mask 与密集注意力的一致性；在 AR 扩散帧间复用已学稀疏结构
- **结果**：实现真正实时生成（720p@24fps，单 H100），稀疏率 75%，FVD 比 Wan2.1 全注意力低 8%（更好），首帧延迟 < 0.3s

---

### 9. Ride the Wave: Precision-Allocated Sparse Attention for Smooth Video Generation
- **作者**：Wentai Zhang, Ronghui Xi, Shiyao Peng, Jiayu Huang, Haoran Luo, Zichen Tang, Haihong E
- **链接**：https://arxiv.org/abs/2504.09756（approx，2026-04-13）
- **核心问题**：视频 DiT 中注意力分数呈"波浪"分布，现有均匀稀疏导致关键帧丢失
- **方法**：精度分配稀疏注意力——分析注意力分数分布的波峰/波谷，在波峰区域（高注意力帧）分配高精度全注意力，波谷区域激进稀疏化；全程无需训练
- **结果**：HunyuanVideo 加速 1.85x，VBench 综合分与全注意力差距 < 0.15%，时序流畅度指标提升 0.03

---

### 10. A Systematic Post-Train Framework for Video Generation
- **作者**：Zeyue Xue, Siming Fu, Jie Huang, Shuai Lu, Haoran Li, Yijun Liu, Yuming Li, Xiaoxuan He, Mengzhao Chen, Haoyang Huang, Nan Duan, Ping Luo
- **链接**：https://arxiv.org/abs/2504.20169（approx，2026-04-28）
- **核心问题**：视频扩散模型的后训练（RLHF/奖励对齐）缺乏系统性框架，各模块独立优化低效
- **方法**：统一后训练流程：(1) 多维奖励建模（美学、运动、文本对齐）；(2) 奖励引导的去噪步采样策略；(3) 渐进式课程——从图像到短视频到长视频顺序对齐；轻量化仅需微调 LoRA 适配器
- **结果**：在 VBench++ 上 6/8 维度超越同规模基线，文本-视频对齐度提升 5.3%，额外训练成本仅需原始训练的 3%

---

*由 OpenClaw AI 自动整理 · 数据来源 ArXiv · 2026-04-30*
