# Data modeling (数据建模)

数据建模是 Cube 的核心，定义了业务逻辑和数据关系。

## 1. Concepts (核心概念)
- **Cubes**：映射到数据库表或 SQL 查询。定义维度（Dimensions）和度量（Measures）。
- **Views**：对最终用户可见的逻辑层，用于重组和简化 Cubes。
- **Measures**：聚合计算（sum, count, countDistinct, avg, min, max, runningTotal）。
- **Dimensions**：分类属性或时间戳。
- **Joins**：定义 Cube 之间的关联关系（one_to_one, one_to_many, many_to_one）。

## 2. Syntax (语法)
- **重点/约束**：
  - 支持 **YAML** 和 **JavaScript** 定义模型。
  - **命名规范**：Cube 名称必须唯一。
  - **SQL 定义**：在 Cube 中可以使用 `sql` 参数定义原始 SQL 查询，但建议保持简单。

## 3. Dynamic data models (动态建模)
- **重点/约束**：
  - 使用 **Jinja** (在 YAML 中) 或 **JavaScript** 进行动态生成。
  - 允许根据环境变量或外部 API 结果动态创建 Cubes。

## 4. Reference & Best Practices (参考与最佳实践)
- **重点/约束**：
  - **度量可加性**：确保度量在聚合时是逻辑正确的。
  - **时间维度**：始终为包含时间戳的表定义时间维度，以便支持时序分析。
  - **避免在 SQL 中进行预聚合**：让 Cube 的聚合引擎处理聚合，而不是在 Cube 的 `sql` 定义中写 `GROUP BY`。
