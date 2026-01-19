# Caching Recipes (缓存实践)

解决特定场景下的缓存问题。

## 1. Incremental pre-aggregations (增量预聚合)
- **重点/约束**：
  - 仅刷新新数据分区，不重新计算历史数据。
  - **要求**：源数据必须是不可变的（Immutable）或带有可靠的更新时间戳。

## 2. Lambda pre-aggregations (Lambda 预聚合)
- **重点/约束**：
  - 结合实时数据和历史预聚合数据。
  - **场景**：适用于需要分钟级实时性且数据量巨大的场景。

## 3. Disabling pre-aggregations (禁用预聚合)
- **重点/约束**：
  - 在开发模式下或特定查询中跳过缓存。
  - 使用 `CUBEJS_DEV_MODE=true` 或查询参数 `renewQuery: true`。

## 4. Refreshing select partitions (手动刷新特定分区)
- **重点/约束**：
  - 通过 API 触发特定时间范围内的预聚合刷新。
