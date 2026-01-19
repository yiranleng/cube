# Caching (缓存机制)

缓存是确保 Cube 高性能的关键。

## 1. Pre-aggregations (预聚合)
- **重点/约束**：
  - **核心定义**：预先计算并存储聚合结果。
  - **Cube Store**：默认的预聚合存储引擎。
  - **刷新策略**：通过 `refresh_key` 控制。可以基于 SQL 查询（如 `SELECT MAX(updated_at) FROM table`）触发。
  - **Aggregate Awareness**：Cube 自动匹配最合适的预聚合，无需手动指定。

## 2. Refreshing (刷新控制)
- **重点/约束**：
  - **后台刷新**：生产环境必须使用后台刷新进程（Refresh Worker）。
  - **并发限制**：预聚合构建过程受数据源连接数限制。

## 3. Best Practices (最佳实践)
- **分区 (Partitioning)**：对大数据集使用分区（如按月或按天），以加速构建和局部刷新。
- **索引 (Indexes)**：在预聚合表上定义索引以加速查询性能。
- **约束**：预聚合定义中缺失度量或维度会导致查询无法命中缓存。
