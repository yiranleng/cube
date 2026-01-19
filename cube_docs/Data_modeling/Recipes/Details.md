# Data modeling Recipes (数据建模实践指南)

## 1. Metric Design (指标设计)
- **重点/约束**：
  - **去重计数 (countDistinct)**：对于大数据量，建议使用 `countDistinctApprox`（使用 HyperLogLog 算法）以提升性能。
  - **嵌套聚合 (Nested Aggregates)**：在一个度量中引用另一个度量的聚合结果。
  - **约束**：嵌套聚合必须确保底层度量已经定义了正确的聚合类型。

## 2. Time-Series Analysis (时序分析)
- **Period-over-period (环比/同比)**：
  - 通过定义两个带有不同 `filters` 的度量来实现。
- **String Time Dimensions (字符串时间维度)**：
  - 如果数据库中的时间是字符串格式，必须在维度中使用 `sql` 进行显式转换。

## 3. Advanced Modeling (高级建模)
- **Polymorphic Cubes (多态 Cube)**：
  - 允许一个 Cube 根据不同的维度表现出不同的行为。
- **EAV Model (实体-属性-值模型)**：
  - 如何将稀疏的 EAV 表映射为 Cube 的维度。
  - **挑战**：这种模型通常性能较差，强烈建议对此类 Cube 使用预聚合。

## 4. Dbt Integration (与 dbt 集成)
- **重点/约束**：
  - 使用 `lkml2cube` 或直接读取 dbt 的 `manifest.json` 来生成 Cube 模型。
  - **同步要求**：dbt 模型变更后，必须重新生成或同步 Cube 模型。
