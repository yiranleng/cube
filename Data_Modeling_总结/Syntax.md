# Syntax - 语法

## 文章重点总结

### 目录与语法形式
- 数据模型文件放在 **model** 目录；可用 `schema_path` 改名，或 `repository_factory` 动态指定目录与内容。
- 建议：每个 cube 单独文件放 **model/cubes**，每个 view 放 **model/views**。
- **模型语法**：支持 **YAML**（.yml）和 **JavaScript**（.js），可混用。YAML 用 Jinja+Python 做动态；JS 用 JavaScript。默认推荐 YAML（简洁可读）；需要更强动态能力时用 JS。

### 命名
- 以**字母**开头；仅**字母、数字、下划线**；不得为 Python 保留字（如 from、return、yield）；使用 DAX API 时不得与日期层级列名冲突。建议 **snake_case**。
- 示例：cubes（orders, stripe_invoices）、views（opportunities, arr）、measures（count, avg_price）、dimensions（name, created_at）、pre-aggregations（main, orders_by_status）。

### SQL 表达式
- `sql`、`sql_table` 中的 SQL 需符合**数据库方言**（如 Snowflake 的 LISTAGG、BigQuery 的 STRING_AGG）。可使用数据源中的 **UDF**。**大小写敏感**的库需正确加引号，如 `'public."Orders"'`。

### 引用语法（References）
- **column**：裸列名，简单场景；有 join 且列名冲突时易歧义。
- **{member}**：花括号引用**同一 cube** 的成员，如 `{name}`、`{surname}`；跨 cube、子查询等需加 cube 前缀。
- **{time_dimension.granularity}**：引用时间维度的某粒度（默认或自定义），如 `{created_at.year}`、`{created_at.sunday_week}`。
- **{cube}.column、{cube.member}**：带 cube 限定，消除 join 歧义；生产环境推荐**全限定**；当前 cube 可用 **{CUBE}** 避免重复写名。
- **{cube1.cube2.member}**：**join 路径**，多级 cube 时指定经过的中间 cube，解决菱形 join 等歧义；可用于 calculated members、views、pre-aggregations。
- **{CUBE}**：引用当前 cube 的上下文变量，列、成员均可。
- **{cube.sql()}**：引用**另一 cube 的 sql**，用于多态、data blending 等，如 `FROM ${organisms.sql()} WHERE ...`。
- **花括号与转义**：YAML 用 `{ref}`；JS 用 `` `${ref}` ``。字面花括号（如 JSON）需转义，如 `\{`、`\}`。
- **非 SQL 上下文**：在 `sql`/`sql_table` 之外，裸名视为**成员名**，可用 `member`、`cube.member`、`CUBE.member`；如 pre_aggregations 的 `dimensions: [CUBE.status]`。

### Context variables
- 除 **CUBE** 外还有若干**上下文变量**，用于 SQL 优化与**动态数据模型**；详见 context variables 参考。

### 故障排查
- **Can't parse timestamp**：时间维度的 SQL 结果需为 **TIMESTAMP** 类型；若为字符串可参考 “string time dimensions” 配方。
