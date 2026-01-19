# Averages and percentiles - 平均值与百分位

## 文章重点总结

- **用途**：了解数值属性的分布；**平均数**在偏态分布下易误导，**百分位**更稳健；**中位数**即 50% 百分位。
- **实现**：平均用 **type: avg**、**sql: 列名**。百分位用 **type: number** + 库函数：Postgres/Snowflake 等用 **PERCENTILE_CONT(n) WITHIN GROUP (ORDER BY col)**（n 如 0.5、0.95）；BigQuery 用 **APPROX_QUANTILES**。中位数示例：`PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY age)`；P95：0.95。
