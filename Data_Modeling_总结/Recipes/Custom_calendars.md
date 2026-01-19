# Custom calendars - 自定义日历（4-5-4 零售日历）

## 文章重点总结

- **场景**：实现 **4-5-4 零售日历**等自定义日历。与“自定义粒度”相比，自定义日历更灵活，适合**时间单位长度可变**的情况（如 4-5-4 的月/季）。
- **核心思路**：
  1. 创建一个 **calendar cube**（如 `calendar_454`，`public: false`），包含每个零售周的 `week_start_date`、`week_end_date`，以及周序号、月序号、财年等属性。
  2. 为每个需要“翻译到零售日历”的事实表时间维度创建**辅助日历 cube**：通过 `extends: calendar_454` 派生（例如 `calendar_454__base_orders__created_at`、`calendar_454__base_orders__completed_at`）。
  3. 在事实 cube（如 `base_orders`）上对每个时间维度分别 join 到对应辅助日历 cube：
     - join 条件常用 `BETWEEN week_start_date AND week_end_date` 来把真实日期落到某个零售周。
  4. 将日历属性（如 `week_number`、`month_number`）通过 **proxy dimensions** 引入事实 cube，供分组/过滤/展示。
- **实现细节**：示例中用递归 CTE 生成 4-5-4 周序列（并考虑 53 周年）；生产建议用 dbt 等工具把日历表**物化**到数仓中，而非运行时生成。

