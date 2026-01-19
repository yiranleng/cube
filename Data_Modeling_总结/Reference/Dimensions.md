# Dimensions - 维度参考

## 文章重点总结

- **维度**表示单条数据的属性；常用参数：**sql**、**type**（string、number、time、boolean 等）、**primary_key**、**description**、**meta**。**time** 型支持 **granularities**（自定义粒度）、**granularities[].interval/offset**；可被 proxy 引用如 `{created_at.year}`。**sub_query**：true 时表示 subquery 维度（引用其它 cube 的度量）。**drill_members** 用于下钻。
