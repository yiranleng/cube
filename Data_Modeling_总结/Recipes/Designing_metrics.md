# Designing metrics - 设计指标

## 文章重点总结

- **Entity-first（实体优先）**：View 围绕**实体**（如 orders、line_items）构建，像**反范式表**，汇集描述该实体所需的多个 measures 与 dimensions；可为同一实体建多个 view 以拆分维度过多的情况。
- **Metrics-first（指标优先）**：View 围绕**单个 measure** 构建，含该度量及用于分组、过滤的 dimensions，**至多一个时间维度**；view 常以该度量命名。若同一度量需在不同时间维度上分析，可建多个 view（如 order_count_by_order_date、order_count_by_ship_date）。利于 BI 兼容与消费方理解。
