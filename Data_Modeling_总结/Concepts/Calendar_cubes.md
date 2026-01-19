# Calendar cubes - 日历 Cube

## 文章重点总结

### 概述
- **Calendar cubes** 用于实现**自定义日历**（如零售日历）；若数据模型中有日历表，可建模为 calendar cube。
- 作用：**覆盖 time-shift 度量的默认行为**；**覆盖时间维度的默认粒度**。
- 由 **Tesseract** 支持，需 `CUBEJS_TESSERACT_SQL_PLANNER` 开启（Preview）。

### 配置
- 在 cube 上设置 **calendar: true** 即表示该 cube 为 calendar cube，从而可使用自定义 time shift 与 granularities。
- `sql` 通常从日历表选择：date_key、calendar_date、start_of_week/month/year、week_ago、month_ago、year_ago 等。
- 需有 **time 类型** 的 **primary_key** 维度（如 `date_key`），以及用于 time_shift、granularities 的 time 维度（如 `date`）。

### Joins
- Calendar cube 需通过 **join** 与业务 cube 关联才有意义；要求：calendar 的 join 维度为 **time + primary_key**；业务方 join 维度也为 **time**。例如：`{CUBE}.date = {fiscal_calendar.date_key}`，relationship: many_to_one。

### 覆盖 Time shifts
- 默认 `prior + 1 month` 会用 `INTERVAL '1 month'`；自定义日历可通过 **time_shift** 配置，用日历表中的**预计算列**（如 `month_ago`）做映射。
- 在时间维度的 **time_shift** 中为每种 `type`+`interval` 指定 **sql**（或 **name** 自定义名）；业务 cube 的 time_shift 度量在查询时若与 calendar 的 time 维度一同使用，会采用 calendar 中的定义。

### 覆盖 Granularities
- 默认用 `DATE_TRUNC` 等算周/月/年；自定义日历可在 **granularities** 中为每个粒度写 **sql**（如零售 4-5-4 周的 `start_of_week`、`mid_month`）。
- 业务 cube 按 calendar 的 time 维度并以该粒度查询时，生成 SQL 会使用 calendar 的 sql 表达式，而非默认 DATE_TRUNC。
