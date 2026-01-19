# String time dimensions - 字符串时间维度处理

## 文章重点总结

- **要求**：Cube 的 `type: time` 维度需要输入为 **带时区的 timestamp（或兼容类型）**。
- **问题**：源表的日期/时间字段经常以 **string** 存储，需要在 `sql` 中解析为 timestamp。
- **做法**：使用数据库的解析函数将字符串转时间类型（示例 BigQuery）：
  - `sql: PARSE_TIMESTAMP('%Y-%m-%d', date)`，`type: time`
- **时区注意**：若解析函数不指定时区（如 BigQuery `PARSE_TIMESTAMP` 默认 UTC），需确保与 Cube 的时区转换逻辑一致，必要时显式设置/转换。
- **性能建议**：大数据引擎（BigQuery/Presto）解析开销通常可接受；传统 RDBMS（如 Postgres）可能明显变慢，建议在上游转换或新增 timestamp 列并建索引。

