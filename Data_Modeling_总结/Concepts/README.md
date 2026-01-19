# Concepts - 概念

## 文章重点总结

### 术语来源与示例数据
- 术语多借鉴 **OLAP 理论**，文档面向新用户与老用户复习。
- 示例使用电商库：`orders`、`line_items` 表；采用 **entity-first（实体优先）** 的设计思路。

### Cubes（立方体）
- **Cube** 表示数据集，概念上类似 SQL 的 view；通常每文件一个 cube。
- 通过 `sql_table` 指向数据源中的单表；也可用 `sql` 编写更复杂的 SQL（如多表 JOIN）。
- 每个 cube 包含 **members**：dimensions、measures、segments。
- 用 **joins** 定义 cube 间关系；用 **pre-aggregations** 加速查询；可被 **views** 引用。
- 支持：**extension**（扩展）、**polymorphism**（多态）、**data blending**（数据混合）；零售历等可用 **calendar cubes**。
- 可静态定义，也可构建 **dynamic data models**。
- 大规模多租户（如 >100 租户）可考虑 **multi-cluster deployment**。

### Views（视图）
- **View** 位于 cubes 的数据图之上，为整个数据模型提供统一门面，便于：定义指标、治理与访问控制、消除歧义 join 路径。
- View **不定义**自己的 members，而是通过指定 join 路径引用 cube 并 include 其 members。
- 可用 `join_path`、`includes`、`excludes`、`prefix` 等控制暴露的成员。
- View **不定义** pre-aggregations，而是**复用**底层 cube 的 pre-aggregations。
- 可用 **folders** 组织 view 的成员（支持扁平与嵌套），兼容不同可视化工具。

### Dimensions（维度）
- **维度**表示**单个**数据点的属性；`orders` 表多为维度（如 id、status）。
- 主键维度需设置 `primary_key: true`，便于 join 时去重；可存在多个主键（组合主键）。
- 维度可放入 **hierarchies**；**proxy dimensions** 利于复用；**subquery dimensions** 可隐式 join。
- **维度类型**：time、string、number、boolean 等，与 SQL 类型有常见对应关系。
- **时间维度**：使用 `type: time`；支持年/季/月/周/日/时/分/秒等粒度，可自定义 **granularities** 和 **proxy dimensions**（如周日为一周的周、财年）。时间维度对**分区预聚合**和**增量刷新**很重要。

### Measures（度量）
- **度量**表示**一组**数据点的聚合属性；如 `count`（type: count）、`total`（sql: price, type: sum）。
- 可结合 **calculated measures**、**subquery dimensions** 做度量组合；**multi-stage calculations** 支持更复杂度量。
- **度量类型**：与 SQL 聚合函数对应，如 avg、count、count_distinct、sum、min、max、number（百分位/中位数）等。

### Segments、Joins、Pre-aggregations
- **Segments**：用于过滤的逻辑分组（在文中另有详解）。
- **Joins**：定义 cube 间关系，支持 one_to_one、one_to_many 等。
- **Pre-aggregations**：预计算与存储聚合结果以加速查询。

### Entity-first 方法
- 设计时遵循 entity-first；metrics-first 的对比可参考 “designing metrics” 配方。
