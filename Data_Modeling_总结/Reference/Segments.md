# Segments - 片段参考

## 文章重点总结

- **segments** 是**预定义过滤**的逻辑分组；参数 **sql** 为布尔表达式（如 `${CUBE}.status = 'completed'`）。查询时选用 segment 等价于加上对应 filter。用于复用常用过滤、在 Explore/Playground 中快速筛选。
