# Data blending - 数据混合

## 文章重点总结

### 概述
- 当需要在**同一图表**中绘制**来自不同 cube 的多个度量**，或基于它们做**计算度量**时，通常需要建立 cube 间的 join。若**除时间维度外无法 join**，可采用 **data blending** 模式。
- **Data blending**：基于**两个或多个已有 cube** 创建一个新 cube，其 `sql` 为底层 cubes 的 **UNION ALL**，从而在“合并后的数据集”上统一查询。

### 适用场景与优势
- **两表结构高度相似**（如线上线下订单均有 amount、created_at、客户 id 等），用 UNION 对齐列后在一个 cube 里定义度量更自然。
- **数据量很大**：blending 下**先在各子查询/子表上聚合、再合并**，比“先按日期 join 再聚合”往往更高效。
- 需要看**多源度量之间的相关、对比**（如线上/线下 revenue 随时间变化）时，既可在**一个 blending cube** 内做计算度量（如 `online_revenue_percentage`），也可在**客户端**对多个 cube 的查询结果做合并展示。

### 实现要点
- 新 cube 的 **sql**：`SELECT ... FROM ${online_orders.sql()} UNION ALL SELECT ... FROM ${retail_orders.sql()}`；通过别名统一列名（如 `user_id AS customer_id`），并增加 **row_type**（如 'online'/'retail'）以区分来源。
- **Measures**：在合并后的列上定义 `customer_count`、`revenue`；用 **filters**（如 `row_type = 'online'`）得到 `online_revenue`、`offline_revenue`；计算度量如 `online_revenue / NULLIF(online_revenue + offline_revenue, 0)` 作占比。
- **Dimensions**：`created_at`（time）、`revenue_type`（即 `row_type`）等。
- **客户端多查询合并**：对 `online_orders` 与 `retail_orders` 分别发查询（相同 dateRange、granularity），用 `cubeApi.load(queries)` 一次取回，在应用层按时间对齐后绘图。
