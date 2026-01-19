# Event analytics - 事件分析（构建 Sessions）

## 文章重点总结

- **目标**：把原始事件数据建模为 **sessions（会话）**，以便回答：session 时长、跳出率、使用最多的区域、用户停留时间、按特定行为筛选会话等。相比“黑盒”分析工具，Cube 的 **SQL 会话模型**可完全自定义。
- **数据来源示例**：Segment `analytics.js`（`pages` 表为 page views，`tracks` 表为 events）；同样思路适用于 Snowplow 等。
- **步骤 1：统一 events 与 pageviews**
  - 用一个 `events` cube 的 `sql` 把 `tracks` 与 `pages` **UNION ALL** 合并；
  - 对 pageview 人为设置 `event='pageview'`；
  - 为每条记录生成 **event_id**（作为主键）；
  - 定义 measures：`count`（所有事件）、`page_views_count`（对 `event='pageview'` 过滤的 count）。
  - 定义 dimensions：`anonymous_id`、`event_id`（primary_key）、`timestamp`（time）、`event`（string）等，便于按时间与事件类型过滤。
- **步骤 2：生成 sessions**
  - session 定义：同一用户在一定时间窗口内的交互；默认可用 **30 分钟不活跃**作为切分阈值。
  - 用窗口函数 **LAG()** 计算相邻事件间的 `inactivity_time`（分钟）；当 `inactivity_time > 30` 或为 NULL 时，视为新 session 开始点。
  - `sessions` cube 的 `sql` 从 `events`（`${events.sql()}`）派生：
    - `session_id`（如 `ROW_NUMBER() ... || anonymous_id`）保证唯一；
    - `session_start_at`、`session_sequence`；
    - `next_session_start_at`（LEAD）用于后续判定 session 边界/时长。
  - 在 `sessions` 中定义 `count`（session 数）与必要的 time dimensions（start_at 等）。
- **下一步（文章后续铺垫）**：把 events 关联到 sessions，用于计算 session 时长、每 session 事件数，以及筛选“包含特定事件”的 sessions（可用于漏斗分析等）。

