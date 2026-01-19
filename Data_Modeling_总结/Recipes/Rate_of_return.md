# Rate of return - 内部收益率（XIRR）

## 文章重点总结

- **目标**：对不等周期的现金流计算 **XIRR（内部收益率）**。
- **能力范围**：
  - `XIRR` 函数在 **SQL API / DAX API / MDX API** 中可用；
  - 同时在 **Cube Store** 中实现，因此当查询命中 pre-aggregations 时，SQL/REST 也可用。
- **关键限制**：若查询**未命中 pre-aggregations**，可能报错：`function xirr(numeric, date) does not exist`。因此建议将其放入**多阶段计算**度量并配合预聚合。
- **建模方式（示例）**：
  - 基础度量：`total_payments`（sum(payment)）
  - `xirr` 度量：`multi_stage: true`，`sql: "XIRR({total_payments}, {date__day})"`，`type: number_agg`，并通过 `add_group_by: [date__day]` 确保按天聚合后再计算；
  - 配置 `pre_aggregations`（按 day 粒度 rollup `total_payments`）以便命中 Cube Store。
- **查询示例**：
  - SQL API 可先在子查询中按 day 聚合再 `XIRR(payment, date)`；
  - 命中预聚合时：`SELECT MEASURE(xirr) FROM payments;` 或 REST `{"measures":["payments.xirr"]}`。
- **依赖**：多阶段计算由 **Tesseract** 支持（`CUBEJS_TESSERACT_SQL_PLANNER`，Preview）。

