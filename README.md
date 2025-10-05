flowchart LR
  %% ====== 数据侧 ======
  A[数据源<br/>PitchBook: 轮次/估值/投资方/金额<br/>LinkedIn: 团队/岗位/技能/高管<br/>USPTO: 专利/家族/被引/主题<br/>(可选: 宏观利率/指数/政策)]:::grp --> 
  B[实体解析与链接<br/>• 公司/投资人/自然人/专利 统一ID<br/>• PB↔LI: URL/域名 + 名称/地址<br/>• USPTO↔PB: 名称清洗 + 地址 + 共现(高管/发明人)<br/>• 置信度与别名表]:::grp -->
  C[事件规范化 & token 化<br/>• 事件 → 概念token<br/>• 连续量 → 分位token(估值/金额/家族规模/被引…)<br/>• 每个事件是一小句token]:::grp -->
  D[时间表达(双时间 + Segment + no‑event)<br/>• Time2Vec: 公司年龄 + 绝对日历时间<br/>• Segment A/B/C: 同日多事件<br/>• no‑event 心跳: 按月/季补“无事件”]:::core

  %% ====== 表示学习 & 检索 ======
  D --> E[序列编码器(基础表示)<br/>• life2vec: 局部Softmax窗口 + 全局Performer<br/>• 可选: Mamba/RetNet 长序列骨干<br/>• Set‑Encoder + 同刻屏蔽<br/>• company‑summary(公司级摘要向量)]:::core
  E --> G[检索增强 & 网络级联<br/>• 相似公司库(FAISS): Top‑K 的下一事件/间隔统计<br/>• 投资人‑公司‑人才 多层网络邻域特征<br/>• 作为条件侧信息注入编码器/时间头]:::core

  %% ====== 任务头 ======
  E --> F1[生成式竞争风险时间头(Delphi++)<br/>• 输出: 下一事件分布 + 到达时间<br/>• 时间分布: 混合 Log‑Normal/分段 hazard(重尾/多峰)<br/>• 条件采样生成整段未来(可校准)<br/>• Conformal 生存分位/区间]:::head
  E --> F2[判别多任务头(life2vec 风格)<br/>• 12/24/36月: 融资/并购/IPO( PU‑Learning )<br/>• 估值分位: 序数分类<br/>• 多损失加权 + AUL 早停]:::head
  G --> F1
  G --> F2

  %% ====== 评估与治理 ======
  F1 --> H[评估与治理<br/>• 生成对齐: 累计事件曲线/群体校准<br/>• 分层与移位: 行业/地区/年份/来源(IRM/GroupDRO/多校准)<br/>• 在线服务: API/看板/模型卡/审计]:::eval
  F2 --> H

  classDef grp fill:#fff,stroke:#000,stroke-width:1px
  classDef core fill:#fff,stroke:#000,stroke-width:1.2px
  classDef head fill:#fff,stroke:#000,stroke-width:1.2px
  classDef eval fill:#fff,stroke:#000,stroke-width:1px
