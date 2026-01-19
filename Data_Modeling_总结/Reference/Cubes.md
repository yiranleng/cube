# Cubes - Cube 参考

## 文章重点总结

### 定义与内容
- **Cube** 表示 Cube 中的一张**数据表**；每文件一个 cube，内含：measures、dimensions、hierarchies、segments、joins、pre-aggregations、access policies。

### 主要参数
- **name**：唯一标识，需符合命名规范。
- **sql_alias**：当自动生成的表别名过长被库截断时（如 Postgres），可设短别名；影响成员名、pre-aggregation 表名。
- **extends**：继承另一 cube 的全部成员；JS 中可用未命名 `cube({...})` 返回引用再 extends，便于动态生成与复用。
- **data_source**：多数据源时指定数据源名，会传给 `driverFactory()`。
- **sql**：自定义 SQL 生成被查询的“表”；可为 `SELECT * FROM t` 或引用 `{cube.sql()}`；**不要**在 cube 级写 GROUP BY。
- **sql_table**：`SELECT * FROM table` 的简写，直接写表名；推荐在适用时优先于 sql。
- **title**：展示名；默认会对 name 做 humanize。
- **description**： human-readable 说明，会在 Playground、API 中暴露。
- **public**：true/false；false 时该 cube 不可通过 API 查询，默认 true。
- **refresh_key**：缓存失效依据。默认：BigQuery/Athena/Snowflake 等 `every: '2 minute'`，其它 `every: '10 second'`。可自定义：`sql: SELECT MAX(updated_at)...` 或 `every: '1 hour'`、CRON；可配 `timezone`。`every` 支持 second/minute/hour/day/week 或 CRON；CRON 不支持“每月某日”“每月”间隔。
- **calendar**：true 表示 calendar cube（见 Concepts/Calendar cubes）。
- 其它：**joins**、**measures**、**dimensions**、**hierarchies**、**segments**、**pre_aggregations**、**access_policies** 等见对应参考页。
