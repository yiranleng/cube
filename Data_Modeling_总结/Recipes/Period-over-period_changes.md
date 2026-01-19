# Period-over-period changes - 环比/同比变化

## 文章重点总结

- **场景**：周环比、月环比、年同比等（如点击、订单、收入的增长）。
- **步骤**：① 定义**本期** measure（如 `current_month_sum`）；② 定义 **time_shift** 的 measure：**multi_stage: true**，`sql: "{current_month_sum}"`，**time_shift: [{ interval: 1 month, type: prior }]** 得到上期；② 定义 **multi_stage** 的**计算度量，如 `{current_month_sum} / NULLIF({previous_month_sum}, 0)` 表示环比倍数。
- **依赖**：Tesseract（`CUBEJS_TESSERACT_SQL_PLANNER`）。查询时 **time dimension 的 granularity** 需与周期一致（如月环比用 month）。
