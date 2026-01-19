# Dynamic parameters - 在查询中传递动态参数

## 文章重点总结

- **场景**：希望用户选择一个筛选值，并在计算中使用该值，但**不希望该筛选影响整条查询**（避免把所有度量都过滤掉）。
- **示例目标**：用户选择 `city`，计算「某城市的人数 / 全国女性总数」这类“部分依赖用户选择”的比值。
- **核心技巧**：利用 **FILTER_PARAMS** 在 cube 的 `sql` 中把“用户选择”应用到**子查询**，然后把该子查询结果与原表 **CROSS JOIN**（或笛卡尔乘）合并，从而：
  - 保持原始行数/总体统计不被过滤；
  - 额外得到一个只包含“用户选择值”的列（如 `city_filter`），可用于**只过滤某个度量**。
- **实现结构（示例）**：
  1. `cities` CTE：`WHERE {FILTER_PARAMS.users.city.filter('city')}` 得到被选城市；
  2. 与 `data`（全量 users）做 join/笛卡尔积，生成 `city_filter` 列；
  3. 定义度量：
     - `total_number_of_women`：全量女性（不依赖 city_filter）
     - `number_of_people_of_any_gender_in_the_city`：用 `filters: city = city_filter` 仅对该度量应用用户选择
     - `ratio`：两者相除（注意 `NULLIF`/`1.0 *` 避免除零与整数除）
  4. 定义维度 `city_filter`，并在 query 里对其 equals（如 Seattle）传入用户选择。
- **安全性**：这种模式只允许从数据集中已有值中选择，避免注入任意用户输入到 SQL。

