# 每日论文精读 · 2026-04-11

> 重点方向：LLM 效率 & 视频生成效率 · 共 10 篇论文

---

## 🧠 LLM 高效算法

### 1. KV Cache Offloading for Context-Intensive Tasks
- **作者**: Andrey Bocharnikov, Ivan Ermakov, Denis Kuznedelev
- **链接**: https://arxiv.org/abs/2604.08426
- **核心问题**: 长上下文 LLM 推理中 KV Cache 成为延迟和内存的双重瓶颈；先前研究主要评估信息提取量少的任务，对上下文密集型（需大量提取信息）的任务场景缺乏评估和优化
- **方法**: 系统研究 KV Cache 卸载（offloading）在上下文密集型任务（大量信息需从上下文提取）下的表现；分析不同卸载策略在 CPU/NVMe 上的适用场景，提出针对高抽取率场景的优化策略，动态平衡 GPU 内存和卸载带宽
- **结果**: 在文档摘要、多跳 QA 等上下文密集型场景下，KV Cache 卸载显著降低内存占用同时保持精度；对高信息提取需求场景的卸载策略选择给出实践指导

### 2. Alloc-MoE: Budget-Aware Expert Activation Allocation for Efficient MoE Inference
- **作者**: Baihui Liu, Kaiyuan Tian, Wei Wang
- **链接**: https://arxiv.org/abs/2604.08133
- **核心问题**: MoE 大模型稀疏激活机制在资源受限场景下仍有大量专家激活带来推理延迟瓶颈；现有减少专家激活的方法在降低延迟的同时导致模型性能严重退化
- **方法**: 引入激活预算（activation budget）概念作为约束，提出 Alloc-MoE 框架动态分配每层每 token 的专家激活数量；利用层级重要性感知机制在总预算内优先激活关键专家，在预算约束下最大化模型能力
- **结果**: 在资源受限推理场景下，Alloc-MoE 相比固定稀疏率方案在相同延迟预算下显著提升模型性能，实现推理效率与模型质量的更优 Pareto 前沿

### 3. DIVERSED: Relaxed Speculative Decoding via Dynamic Ensemble Verification
- **作者**: Ziyi Wang, Siva Rajesh Kasa, Ankith M S
- **链接**: https://arxiv.org/abs/2604.07622
- **核心问题**: 推测解码中刚性验证步骤要求接受的 token 分布严格匹配目标模型，导致大量合理 token 被拒绝，限制了加速比
- **方法**: 提出 DIVERSED（Dynamic Ensemble Verification），通过动态集成多个草稿模型进行验证，放宽验证步骤的严格性；在保证输出质量的前提下允许接受更多合理草稿 token，提升 token 接受率
- **结果**: 在多个 LLM 推理基准上，DIVERSED 相比标准推测解码显著提升 token 接受率和端到端加速比，在不牺牲输出质量的情况下实现更快推理

### 4. Blink: CPU-Free LLM Inference by Delegating the Serving Stack to GPU and SmartNIC
- **作者**: Mohammad Siavashi, Mariano Scazzariello, Gerald Q. Maguire
- **链接**: https://arxiv.org/abs/2604.07609
- **核心问题**: 现有 LLM 推理服务框架将 CPU 置于 token 级控制的关键路径上，使得性能对 CPU 干扰敏感，强制运营商预留 CPU 资源，导致大量算力浪费
- **方法**: 提出 Blink 端到端服务架构，将 CPU 从稳态推理路径中移除；将调度和 token 级控制委托给 GPU 和 SmartNIC，实现真正意义上的 CPU-Free LLM 推理，消除 CPU 成为瓶颈的可能
- **结果**: Blink 架构显著降低 LLM 推理延迟抖动，提升服务器整体资源利用率，使得 LLM 服务与其他负载的共置（colocation）成为可能，大幅提升数据中心经济性

### 5. AsyncTLS: Efficient Generative LLM Inference with Asynchronous Two-level Sparse Attention
- **作者**: Yuxuan Hu, Jianchao Tan, Jiaqi Zhang, et al.
- **链接**: https://arxiv.org/abs/2604.07815
- **核心问题**: 长上下文 LLM 推理面临注意力复杂度二次方增长和 KV Cache 内存双重挑战；token 级稀疏注意力精度高但索引开销大；块级方法高效但牺牲精度
- **方法**: 提出 AsyncTLS 分层稀疏注意力系统，结合粗粒度块过滤与细粒度 token 选择；配合异步卸载引擎，通过时间局部性利用将 KV Cache 传输与计算重叠，同时支持 GQA、MLA 等多种注意力架构
- **结果**: 在 Qwen3 和 GLM-4.7-Flash 上评估，AsyncTLS 在精度接近全注意力的同时大幅降低 KV Cache 内存和计算开销，实现长上下文推理的精度-效率最优平衡

### 6. SAT: Balancing Reasoning Accuracy and Efficiency with Stepwise Adaptive Thinking
- **作者**: Weiyang Huang, Xuefeng Bai, Kehai Chen, et al.
- **链接**: https://arxiv.org/abs/2604.07922
- **核心问题**: 大型推理模型（LRM）普遍存在"过度思考"问题，生成不必要的冗长推理链；现有解决方案在提升 token 效率的同时往往牺牲细粒度控制或破坏推理逻辑完整性
- **方法**: 提出逐步自适应思考（SAT）框架，进行步骤级、难度感知的推理剪枝同时保留核心推理结构；将推理建模为有限状态机（FSM），定义慢思考/正常/快速/跳过四种思考模式，通过轻量级过程奖励模型（PRM）动态切换状态
- **结果**: SAT 在多个数学推理和逻辑推理基准上，相比标准 Long CoT 显著减少 token 用量（25-40%），同时维持与全量推理相当的准确率，实现推理效率的精细化动态控制

---

## 🎬 视频生成高效算法

### 7. AdaSpark: Adaptive Sparsity for Efficient Long-Video Understanding
- **作者**: Handong Li, Zikang Liu, Longteng Guo, et al.
- **链接**: https://arxiv.org/abs/2604.08077
- **核心问题**: 长视频 Video-LLM 处理计算成本极高；现有效率方法要么通过不可逆信息丢弃损害细粒度感知，要么通过固定稀疏模式限制长程时序建模
- **方法**: 提出 AdaSpark 自适应稀疏框架，将视频输入划分为 3D 时空立方体，通过两个协同设计的上下文感知组件：(1) 自适应立方体选择注意力（AdaS-Attn），动态决定每个立方体的计算强度；(2) 可逆信息聚合，确保关键信息不被永久丢弃
- **结果**: AdaSpark 在多个长视频理解基准上，以显著更低的计算量（FLOPs 减少 40%+）达到与全量计算相当甚至更好的性能，为长视频理解提供实用的效率方案

### 8. ABMamba: Aligned Hierarchical Bidirectional Scan Mamba for Efficient Video Captioning
- **作者**: Daichi Yashima, Shuhei Kurita, Yusuke Oda, et al.
- **链接**: https://arxiv.org/abs/2604.08050
- **核心问题**: 现有基于 Transformer 的视频描述模型注意力复杂度随序列长度二次方增长，在长视频上计算代价极高；Mamba 等线性复杂度方法在多模态视频建模上的对齐机制尚不成熟
- **方法**: 提出 ABMamba（Aligned Hierarchical Bidirectional Scan Mamba），具有线性计算复杂度的全开源多模态 LLM；设计对齐的分层双向扫描机制，在保持线性复杂度的同时有效捕获视频的双向时序依赖；通过分层结构建模不同时间尺度的视频特征
- **结果**: ABMamba 在多个视频描述基准上达到 Transformer 基线相当的性能，同时计算复杂度为线性，大幅降低长视频推理延迟和内存占用，支持更长视频序列的可扩展处理

### 9. CodecSight: Leveraging Video Codec Signals for Efficient Streaming VLM Inference
- **作者**: Yulin Zou, Yan Chen, Wenyan Chen, et al.
- **链接**: https://arxiv.org/abs/2604.06036
- **核心问题**: 视频流分析是视觉语言模型的关键工作负载，但多模态推理的高成本限制了可扩展性；先前系统分别针对 ViT 或 LLM 单独优化，未能捕获端到端机会；现有冗余检测方法开销大，不适合动态实时流
- **方法**: CodecSight 利用视频编解码器信号（运动向量、残差等）作为免费的时序冗余信号，同时优化 ViT 和 LLM 两个阶段；利用编解码器元数据零成本识别帧间冗余，跳过冗余帧处理，实现端到端端到端效率提升
- **结果**: CodecSight 在视频流 VLM 推理上相比全量推理实现显著加速，同时维持任务精度，特别在高帧率实时流场景下推理吞吐量大幅提升

### 10. FP4 Explore, BF16 Train: Diffusion Reinforcement Learning via Efficient Rollout Scaling
- **作者**: Yitong Li, Junsong Chen, Shuchen Xue, et al. (includes Song Han, Enze Xie)
- **链接**: https://arxiv.org/abs/2604.06916
- **核心问题**: 扩散模型 RL 后训练中增大 rollout 组规模可显著提升对齐性能，但对大规模扩散模型（如 FLUX.1-12B）进行 rollout 扩展计算代价极高
- **方法**: 探索将 FP4 量化引入扩散 RL 的 rollout 阶段以加速采样；识别到朴素量化 pipeline 因训练-推理精度不匹配而不稳定；提出 FP4 Explore, BF16 Train 框架，rollout 用 FP4（快速探索），梯度更新用 BF16（稳定训练），并设计量化感知的分组奖励归一化消除精度差异
- **结果**: 在 FLUX 等大规模文生图扩散模型上，rollout 吞吐量显著提升（约 2-3x），同时对齐训练稳定性与全精度方案相当，以更低计算成本扩展 rollout 规模实现更好人类偏好对齐
