# Calculated members - 计算成员（计算度量与计算维度）

## 文章重点总结

### 概述
- 维度和度量除直接映射表列或简单聚合外，还可**引用同一或其他 cube 的成员**、使用 **SQL 表达式**、**基于其他度量/维度做计算**。
- 常见模式：**calculated measures**（计算度量）、**proxy dimensions**（代理维度）、**subquery dimensions**（子查询维度）。

### 1. Calculated measures（计算度量）
- **定义**：用 SQL 函数和运算符对其他度量做运算，将复杂度量（如比率、百分比）拆成简单度量的公式；也有助于**非可加度量**与 pre-aggregations 配合。
- **同一 cube**：如 `completed_ratio = 1.0 * completed_count / count`；注意用 `1.0` 避免 SQL 整数除。
- **跨 cube**：若 `first_cube` 与 `second_cube` 有 join，可在 `first_cube` 中定义引用 `second_cube.measure` 的度量；查询时 Cube 会自动生成带 join 的 SQL。

### 2. Proxy dimensions（代理维度）
- **定义**：引用同一或其它 cube 的**维度**，用于复用已有维度定义、减少重复。
- **同一 cube**：如 `full_name` 引用 `initials`、`last_name` 组合成新维度。
- **跨 cube**：通过 join 把 `second_cube.dimension` 作为 `first_cube` 的维度（或换名）；仅查该代理维度时，Cube 可能优化掉不必要的 join。
- **时间维度粒度**：引用 `time` 维度时可指定 **granularity**（如 `{created_at.year}`、`{created_at.sunday_week}`），支持默认粒度或自定义粒度。

### 3. Subquery dimensions（子查询维度）
- **定义**：引用**其它 cube 的度量**，在当前 cube 中以**维度**形式暴露；用于“度量的度量”、嵌套聚合、过滤聚合等。
- 需设置 `sub_query: true`；实现上类似**相关子查询**，但用 join 方式以保证性能与可移植性。
- **在度量中使用**：subquery 维度可像普通维度一样被度量引用，例如 `avg_order_count` 对 `order_count` 做 `avg` 聚合。
- 相关配方：nested aggregates、filtered aggregates。
