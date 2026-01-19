# Custom time dimension granularities - 自定义时间粒度

## 文章重点总结

- **场景**：需要按非默认时间粒度分组（例如周从周日开始、财年/财季不是 1 月 1 日开始）。
- **定义方式**：在 `type: time` 的维度上配置 **granularities**，每个粒度用 **interval + offset** 定义：
  - **sunday_week**：`interval: 1 week`，`offset: -1 day`（周日为周起点）
  - **fiscal_year / fiscal_quarter**：用 `interval`（1 year/1 quarter）+ `offset`（如 -3 months）实现财年从 10 月等场景
- **对外暴露**：建议用 **proxy dimensions** 将自定义粒度暴露成可查询的维度（如 `sql: "{timestamp.fiscal_year}"`），便于在 Playground/BI（SQL API）中直接使用。
- **派生标签**：可在字符串维度中基于自定义粒度计算展示字段（示例 `fiscal_quarter_label` 由 FY 年份 + 季度拼接）。

