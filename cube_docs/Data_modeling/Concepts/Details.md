# Data modeling Concepts (建模概念)

深入理解 Cube 数据建模的高级概念。

## 1. Calculated members (计算成员)
- **重点/约束**：
  - 基于现有度量或维度进行计算。
  - **SQL 限制**：计算成员的 `sql` 必须是数据库原生支持的语法。

## 2. Multi-stage calculations (多阶段计算)
- **重点/约束**：
  - 允许在一个 Cube 中引用另一个 Cube 的聚合结果。
  - **性能影响**：可能导致复杂的子查询，建议配合预聚合使用。

## 3. Joins between cubes (Cube 间联接)
- **重点/约束**：
  - 类型：`one_to_one`, `one_to_many`, `many_to_one`。
  - **约束**：Join 路径必须是无环的（A cyclic graph is not allowed）。

## 4. Calendar cubes (日历 Cube)
- **重点/约束**：
  - 用于处理缺失日期的数据补全。
  - **要求**：需要一个包含所有日期的连续序列表。

## 5. Extension (扩展)
- **重点/约束**：
  - 使用 `extends` 关键字复用其他 Cube 的定义。
  - **复用性**：可以覆盖（Override）父 Cube 的维度或度量。

## 6. Data blending (数据混合)
- **重点/约束**：
  - 同时从多个数据源获取数据并在应用层合并。
  - **限制**：目前 Cube 主要在查询级别处理混合，而非物理层。
