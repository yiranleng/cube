# Nested aggregates - 嵌套聚合

## 文章重点总结

- **场景**：对事实表做**二次聚合**，如「每个门店下，按产品汇总销售后再取中位数」。
- **做法**：单次 measure 只能对应一层聚合。**内层**（如 SUM）在 fact cube 的 measure；**外层**（如 PERCENTILE_CONT）放在**另一 cube**，通过 **subquery dimension**（**sub_query: true**）把内层 measure 引进来，再在该 cube 的 measure 上做 PERCENTILE_CONT 等。两 cube 之间需定义 **join**（如按 store_id、product_id 组合键）。实现上对应 SQL 的 CTE：先 GROUP BY 内层，再对结果做外层 GROUP BY。
