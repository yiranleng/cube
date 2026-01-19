# Data modeling Reference (建模参考)

Cube 模型属性的详细参考手册。

## 1. Cubes
- `sql`: 定义数据来源。
- `preAggregations`: 预聚合配置。

## 2. Views
- `includes`: 包含的度量和维度。
- **约束**：View 不直接定义 SQL，它是逻辑视图。

## 3. Measures
- `type`: `count`, `sum`, `avg`, `min`, `max`, `countDistinct`, `countDistinctApprox`, `runningTotal`。
- `filters`: 仅对符合条件的行进行聚合。

## 4. Dimensions
- `type`: `string`, `number`, `time`, `boolean`。
- `primaryKey`: 每个 Cube 必须定义主键（通常是 ID）。

## 5. Pre-aggregations
- `refreshKey`: 刷新逻辑（如 SQL 轮询）。
- `partitionGranularity`: 分区粒度。
- **强制要求**：在 Cube Store 中存储时，必须指定正确的外部数据库配置。

## 6. Access policies
- `securityContext`: 访问安全上下文。
- `queryRewrite`: 允许在编译时修改查询对象。
