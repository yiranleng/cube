# Retention analysis - 留存分析与 Cohort

## 文章重点总结

- **目标**：做 **cohort 留存分析**（如月留存），衡量“从某起始条件（通常是注册时间）开始，后续各期仍然活跃的比例”，常以 cohort grid/曲线展示。
- **难点**：SQL 里需要构造 **user × month（或 day/week）** 的完整组合，包含“无活动”的月份（pageviews=0）。
- **建模步骤（示例：月留存）**：
  1. 从 `pages` 生成 `months_list`：所有出现过活动的月份（`DISTINCT date_trunc('month', original_timestamp)`）。
  2. 将 `users` 与 `months_list` 左连接，且 `activity_month >= signup_month`，生成每个用户从注册月开始的每个月行。
  3. 再左连接聚合后的活动表 `data`（按 user_id+activity_month 统计 `monthly_pageviews`），把无活动月份填为 0/NULL。
  4. 以上 SQL 作为 `monthly_retention` cube 的 `sql` 基表。
- **度量设计**：
  - `total_count`：当期 cohort 的用户数（`count_distinct user_id`）。
  - `total_active_count`：活跃用户数（加 `filters: monthly_pageviews > 0`）。
  - `percentage_of_active`：`100.0 * total_active_count / NULLIF(total_count, 0)`，`format: percent`。
- **维度设计（用于 cohort 网格）**：
  - `signup_date`：cohort 维度（signup_month 的 time 维度，注意 `date_trunc` 可能是 UTC，需要显式时区转换避免 time shift 造成偏差）。
  - `months_since_signup`：相对月份（`DATEDIFF('month', signup_month, activity_month)`），用于展示第 0/1/2…个月的留存。

