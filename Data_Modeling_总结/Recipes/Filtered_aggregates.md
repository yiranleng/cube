# Filtered aggregates - 过滤聚合

## 文章重点总结

- **场景**：对**已 join 的**下游 cube 做聚合，且聚合需按**另一 cube 的维度**过滤（例如：按零售商汇总销售，但只统计 goal_start 之后的销售）。
- **做法**：在**被 join 的 cube**（如 store → sales）上定义两种 measure：① 全量（如 total_sales）；② 带 **filters** 的（如 total_sales_for_goal，`filters: [{ sql: \`{sales}.sold_at >= {retailer.goal_start}\` }]`，需通过 join 能访问到 retailer 的 goal_start）。再在**上游 cube**（retailer）用 **subquery dimension**（**sub_query: true**）把这两个 measure 引入，最后在上游用计算度量做比，如 `sales_for_goal / NULLIF(sales_goal, 0)` 表示目标达成率。依赖正确 join 与 subquery 的路径解析。
