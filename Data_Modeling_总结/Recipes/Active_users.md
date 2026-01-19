# Active users - 活跃用户（DAU/WAU/MAU）

## 文章重点总结

- **目标**：衡量用户/客户参与度，常用指标：**DAU/WAU/MAU**。
- **实现核心**：用 measure 的 **rolling_window** 参数在滚动窗口内做 `count_distinct(user_id)`：
  - **MAU**：`trailing: 30 day`，`offset: start`
  - **WAU**：`trailing: 7 day`，`offset: start`
  - **DAU**：`trailing: 1 day`，`offset: start`
- **组合指标**：可基于已定义指标做计算度量，如 **WAU/MAU 比**：
  - `sql: 100.0 * {weekly_active_users} / NULLIF({monthly_active_users}, 0)`，`format: percent`
- **查询要点**：建议在 query 里设置 `timeDimensions` 的 `dateRange`（并选择合适 granularity）以得到稳定的时间窗口统计结果。

