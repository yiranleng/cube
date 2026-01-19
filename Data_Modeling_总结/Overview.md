# Overview - 数据建模入门

## 文章重点总结

### 核心定位
- **数据模型的作用**：将原始数据转化为有意义的业务定义，并对数据进行预聚合以优化查询结果。

### 1. 创建 Cube
- **Cube** 用于组织实体及实体间的连接；通常每个数据库表对应一个 cube（如 `users`、`orders`、`products`）。
- 在 `sql_table` 参数中定义该 cube 的基表。

### 2. 添加度量 (Measures) 和维度 (Dimensions)
- **Measures（度量）**：定量数据，如销量、独立访客数、利润等。
- **Dimensions（维度）**：定性数据，如城市、性别、产品名、时间单位等。
- 例如：`count` 度量配合 `type: count` 和 `sql: id`，会生成 `SELECT COUNT(id) AS count FROM users`。
- 在查询中加入维度（如 `city`）时，Cube 会生成带 `GROUP BY` 的 SQL。

### 3. 为度量添加过滤器 (Measure Filters)
- 通过 **filters** 数组可对度量施加条件，例如只统计付费用户：`paying_count` 使用 `filters: [{ sql: \`${CUBE}.paying = 'true'\` }]`。
- 最佳实践：引用表列时用 cube 名或 `CUBE` 常量做前缀。
- 实现方式：SQL 中会变为 `CASE WHEN ... THEN ... END` 形式的条件聚合。

### 4. 使用计算度量 (Calculated Measures)
- 计算度量通过 SQL 表达式引用其他度量，如：`paying_percentage` = `100.0 * ${paying_count} / ${count}`。
- 可设置 `type: number`、`format: percent` 等以控制类型和展示格式。
- 计算度量也可与维度一起使用。
