# Pre-aggregations - 预聚合参考

## 文章重点总结

- **pre_aggregations** 在 cube 中定义，用于加速：**rollup**（含 **measures**、**dimensions**、**time_dimension** 与 **granularity**）、**original_sql**、**rollup_only** 等类型。**partition_granularity**、**refresh_key**（含 **every**、**incremental**）、**scheduled_refresh** 等用于分区与刷新。View 复用底层 cube 的 pre-aggregations。详见 Caching 文档。
