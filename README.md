```mermaid
flowchart LR
  A[数据源 PitchBook/LinkedIn/USPTO] --> B[实体解析与链接]
  B --> C[事件规范化 & token化]
  C --> D[时间表达 (双时间 + Segment + no-event)]
  D --> E[序列编码器 life2vec/Mamba 等]
  E --> F1[生成式竞争风险时间头 Delphi++]
  E --> F2[判别多任务头 life2vec]
  F1 --> H[评估与治理]
  F2 --> H
```
