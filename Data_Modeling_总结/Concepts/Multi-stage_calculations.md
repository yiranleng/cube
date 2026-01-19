# Multi-stage calculations - 多阶段计算

## 文章重点总结

### 概述
- **多阶段计算**：在**两个或更多阶段**中完成、且常对**已聚合数据**再运算的度量；每阶段对应生成 SQL 中的 **CTE（公共表表达式）**。
- 由 **Tesseract**（下一代数据建模引擎）支持；需通过环境变量 `CUBEJS_TESSERACT_SQL_PLANNER` 开启，当前为 **Preview**。
- **限制**：多阶段计算**暂不被 pre-aggregations 加速**。

### 典型场景
- 滚动窗口（rolling window）
- 时间平移（time-shift），如同比
- 期初至今（period-to-date），如 YTD/QTD/MTD
- 固定维度（fixed dimension），如与整体比较、占比
- 条件度量（conditional measure），按维度取值选择表达式
- 排名（ranking）

### 1. Rolling window（滚动窗口）
- 用度量的 **rolling_window** 配置；基于**时间维度粒度**与 **trailing**、**leading** 确定窗口。
- 流程：① 确定查询日期范围（来自时间维度过滤，或 min/max）；② 计算时间窗口序列；③ 对每窗口计算度量。
- 未启用 Tesseract 时，**必须**提供时间维度的 dateRange，否则报错：`Time series queries without dateRange aren't supported`。

### 2. Time shift（时间平移）
- 用 **time_shift**：将时间维度在时间轴上平移，得到“另一时刻”的度量值，如去年同期。
- 配置示例：`interval: 1 year`、`type: prior`；可与 **calendar cubes** 结合定制财年/零售历等。

### 3. Period-to-date（期初至今）
- **rolling_window** 中 `type: to_date`，配合 **granularity**：`year` → YTD，`quarter` → QTD，`month` → MTD。

### 4. Fixed dimension（固定维度）
- 在更粗粒度上先聚合（如按 `country` 的 `country_revenue`），再在细粒度上用 `multi_stage: true` 的度量做比或占比：`revenue / NULLIF(country_revenue, 0)`。
- **group_by** 指定固定聚合的维度。

### 5. Conditional measure（条件度量）
- 用 **case** + **switch**：`switch` 为维度（如 `currency`），`when` 为不同取值对应的 `sql`，`else` 为默认。
- 需与 **switch 类型维度** 一起使用。

### 6. Ranking（排名）
- **multi_stage: true**，`type: rank`；用 **order_by** 指定排序表达式与方向，**reduce_by** 指定分组维度（可多个）。
