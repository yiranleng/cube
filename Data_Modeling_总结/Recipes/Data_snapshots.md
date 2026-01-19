# Data snapshots - 数据快照（SCD2）

## 文章重点总结

- **场景**：对随时间变化的属性（如订单状态）做“截至某日的最新状态”统计；属于 **SCD Type 2**（缓慢变化维）。
- **目标问题**：例如「在某天仍处于 shipped 状态的订单数是多少？」。
- **实现思路**：
  1. 生成日期范围（从最早 `changed_at` 到最晚 `changed_at`，按天）。
  2. 将日期与状态变更表 join，并只保留**截至该日**每个对象（如 order_id）的**最新一条**状态记录。
- **实现方式（示例）**：
  - 建 `status_snapshots` cube，**extends** 原 `statuses` 以复用维度定义；
  - 在 `sql` 中用 CTE `range` 生成日期序列（Postgres 用 `GENERATE_SERIES`；BigQuery 可用 `GENERATE_DATE_ARRAY` 等同类函数）；
  - `LEFT JOIN statuses`：`range.date >= statuses.changed_at`，并用相关子查询 `statuses.changed_at = (SELECT MAX(changed_at) ... WHERE order_id=...)` 选出最新状态；
  - 新增维度 `date`（time）代表快照日期。
- **查询方式**：对 `date` 做 equals 过滤选择快照日，再对 `status` 过滤（如 shipped），度量用 `count` 得到该日的数量。

