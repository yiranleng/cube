# Advanced Caching & Production (高级缓存与生产运行)

## 1. Matching & Refreshing (匹配与刷新)
- **Matching Pre-aggregations**：
  - Cube 使用 **Aggregate Awareness** 自动选择。
  - **失效场景**：如果查询包含预聚合定义之外的 `filter` 或 `dimension`，则无法匹配。
- **Refreshing Select Partitions**：
  - 支持手动刷新特定分区，而不是全量刷新。
  - **约束**：必须在预聚合定义中开启分区 (`partition_granularity`)。

## 2. Lambda Pre-aggregations (Lambda 预聚合)
- **架构要求**：
  - 将海量历史数据（预聚合）与最新增量数据（直接查询或实时预聚合）结合。
  - **优点**：兼顾超高性能和秒级实时性。
  - **复杂度**：需要维护两套刷新逻辑。

## 3. Running in Production (生产运行要求)
- **Cube Store**：生产环境必须使用分布式 Cube Store，并配合持久化存储（如 S3/GCS）。
- **Refresh Worker**：必须单独部署 Worker 实例负责构建预聚合，以防干扰主查询服务的稳定性。
- **Monitoring**：必须监控 `refresh_key` 的执行情况，防止因数据库源端变动导致的预聚合构建失败。
