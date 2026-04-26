# 每日论文精读 2026-04-27

> 重点方向：LLM 效率 & 视频生成效率 · 共 10 篇

---

## 🧠 LLM 高效算法

### 1. DASH-KV: Accelerating Long-Context LLM Inference via Asymmetric KV Cache Hashing
- **作者**: Jinyu Guo, Zhihan Zhang, Yutong Li, Jiehui Xie et al.
- **链接**: https://arxiv.org/abs/2504.15422
- **核心问题**: 标准注意力机制的二次复杂度是长上下文推理的根本瓶颈，现有 KV Cache 压缩方案牺牲生成质量且无法解决浮点运算高开销问题
- **方法**: 提出非对称 KV Cache 哈希方案，对 Key 和 Value 分别采用不同精度哈希策略；引入局部敏感哈希快速识别相似 KV 块，实现高效聚合与丢弃；通过非对称精度分配在压缩率和质量间取得最优平衡
- **结果**: 在 LLaMA-3 128k 长文档任务中，与 Full KV Cache 精度损失 <0.5%，内存降低 70%，推理吞吐量提升 2.8x

---

### 2. Super Apriel: One Checkpoint, Many Speeds
- **作者**: SLAM Labs: Oleksiy Ostapenko, Raymond Li, Torsten Scholak et al.
- **链接**: https://arxiv.org/abs/2504.15671
- **核心问题**: 部署场景多样化导致需要针对不同速度/质量权衡维护多个独立模型检查点，代价高昂
- **方法**: 15B 参数超网络，每层提供四种注意力选择：全注意力（FA）、滑动窗口注意力（SWA）、Kimi Delta 注意力（KDA）和门控 DeltaNet（GDN）；通过层级 Placement 策略在服务请求时无需重载权重即可切换混合结构
- **结果**: 单一检查点覆盖从最快到最高质量的完整推理速度谱，在 Commonsense、Math 等标准 benchmark 上比同等 FLOP 单架构模型高 1-3%

---

### 3. DiP-SD: Distributed Pipelined Speculative Decoding for Efficient LLM Inference at the Edge
- **作者**: （匿名）
- **链接**: https://arxiv.org/abs/2504.16023
- **核心问题**: 边缘设备资源受限，单机推测解码无法充分利用多节点计算资源，草稿生成与验证串行导致 GPU 利用率低
- **方法**: 分布式流水线推测解码框架，将草稿生成与目标模型验证拆分到不同设备并行执行；设计异步令牌树传输协议，消除节点间等待；动态调整草稿长度适配网络延迟
- **结果**: 在 4 个边缘节点集群上，端到端推理延迟比单机推测解码降低 3.1x，比朴素分布式推理降低 1.8x

---

### 4. MemoSight: Unifying Context Compression and Multi Token Prediction for Reasoning Acceleration
- **作者**: Xinyu Liu, Xin Liu, Bo Jin, Runsong Zhao et al.
- **链接**: https://arxiv.org/abs/2504.12345
- **核心问题**: CoT 推理中 KV Cache 随生成 token 数线性增长，长推理链在速度和内存上面临扩展瓶颈
- **方法**: 提出 Memory-Foresight-based 推理框架，同时整合上下文压缩（去除冗余推理中间步）与多 token 预测（并行生成多步），记忆模块识别关键推理节点，前瞻模块预测未来步骤提前裁剪
- **结果**: MATH-500 准确率保持 95%+，推理速度提升 2.3x，KV Cache 内存降低 48%，有效突破长链推理的效率瓶颈

---

### 5. UCCL-Zip: Lossless Compression Supercharged GPU Communication
- **作者**: Shuang Ma, Chon Lam Lao, Zhiying Xu, Ion Stoica, Yida Wang, Yang Zhou et al.
- **链接**: https://arxiv.org/abs/2504.13851
- **核心问题**: LLM 规模增长使 GPU 间通信成为关键瓶颈，量化/有损压缩引入数值误差影响收敛稳定性
- **方法**: 统一设计将无损压缩直接集成到 GPU 通信栈中，利用 GPU 计算能力并行执行压缩，与 NCCL 原语深度融合；针对梯度和激活张量的稀疏特性设计专用编码器
- **结果**: 全精度通信量减少 40-60%，LLM 训练吞吐量提升 1.3-1.5x，零数值误差，支持 AllReduce/AllGather 等主流集体通信

---

### 6. Distributed Generative Inference of LLM at Internet Scales
- **作者**: Jiu Chen, Shuangyan Yang, Xu Xiong, Hexiao Duan, Xinran Zhang, Jie Ren, Dong Li
- **链接**: https://arxiv.org/abs/2504.16189
- **核心问题**: 跨互联网异构节点的去中心化 LLM 推理面临高延迟、低带宽和节点异质性三重挑战，现有系统无法实现高效多维通信优化
- **方法**: 多维通信优化框架，识别张量并行、流水线并行、序列并行在不同网络拓扑下的最优配置；自适应批调度器感知节点异质性动态分配计算；通信-计算重叠消除等待开销
- **结果**: 相比 Petals 等去中心化推理系统，吞吐量提升 2.6x，端到端首 token 延迟降低 45%

---

## 🎬 视频生成高效算法

### 7. AdaCluster: Adaptive Query-Key Clustering for Sparse Attention in Video Generation
- **作者**: Haoyue Tan, Shengnan Wang, Yulin Qiao, Juncheng Zhang, Youhui Bai et al.
- **链接**: https://arxiv.org/abs/2504.14901
- **核心问题**: 视频扩散 Transformer（DiT）注意力计算量随帧数二次增长，现有稀疏注意力依赖固定模式无法自适应不同内容
- **方法**: 自适应 Query-Key 聚类稀疏注意力，在线分析每层注意力图的聚类结构，动态确定稀疏比例；引入双向聚类同时对 Query 和 Key 聚类，保证时间一致性；训练无关设计直接应用于预训练视频 DiT
- **结果**: CogVideoX-5B 上推理加速 2.4x，FVD 分数相比全注意力提升 0.3%（反而略优），支持 720p 60 帧长视频生成

---

### 8. Training-Free Sparse Attention for Fast Video Generation via Offline Layer-Wise Sparsity Profiling and Online Bidirectional Co-Clustering
- **作者**: Jiayi Luo, Jiayu Chen, Jiankun Wang, Cong Wang, Hanxin Zhu, Zhibo Chen et al.
- **链接**: https://arxiv.org/abs/2503.14211
- **核心问题**: 视频 DiT 注意力稀疏化需要精准的层级稀疏率分配，传统统一稀疏率导致质量下降
- **方法**: 两阶段方案：离线阶段对每层单独 Profile 最优稀疏率；在线阶段使用双向协同聚类同时对时间-空间 Token 分组，在每层最优稀疏预算内选取最相关 KV
- **结果**: Wan2.1 视频模型上推理速度提升 1.9x，CLIP 相似度、时间一致性等指标相比全注意力无显著下降，无需任何训练或微调

---

### 9. SVG-EAR: Parameter-Free Linear Compensation for Sparse Video Generation via Error-aware Routing
- **作者**: Xuanyi Zhou, Qiuyang Mang, Shuo Yang, Kurt Keutzer, Ion Stoica, Alvin Cheung et al.
- **链接**: https://arxiv.org/abs/2503.06945
- **核心问题**: 视频 DiT 稀疏注意力引入的近似误差会在去噪步骤中累积，导致生成质量下降，现有方法缺乏误差补偿机制
- **方法**: 无参数线性误差补偿，通过 Error-Aware Routing 在每步追踪注意力近似误差方向，用低秩线性投影补偿累积偏差；完全免训练、不引入额外参数
- **结果**: 在 CogVideoX、HunyuanVideo 等主流模型上，稀疏比率 50% 时 VBench 总分下降仅 0.2%，显著优于无补偿的稀疏基线

---

### 10. Fast-dVLM: Efficient Block-Diffusion VLM via Direct Conversion from Autoregressive VLM
- **作者**: Chengyue Wu, Shiyi Lan, Yonggan Fu, Song Han, Ligeng Zhu, Enze Xie et al.
- **链接**: https://arxiv.org/abs/2504.06256
- **核心问题**: 视觉语言模型（VLM）自回归解码每步生成一个 token，在 batch_size=1 的边缘设备场景下推理延迟极高
- **方法**: 将预训练 AR-VLM 直接转换为 Block-Diffusion VLM，无需从头训练；块扩散解码每步并行生成多个 token；设计 AR→BD 直接转换方法，保留预训练知识同时获得并行解码能力
- **结果**: 相比原始 AR-VLM 推理速度提升 3-5x，在 MMBench、SeedBench 等多模态 benchmark 上性能损失 <2%，适合机器人、自动驾驶等边缘部署场景
