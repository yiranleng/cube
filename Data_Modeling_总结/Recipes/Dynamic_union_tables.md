# Dynamic union tables - 动态 UNION 表

## 文章重点总结

- **场景**：数据库里有很多结构相同、代表同一实体的表（例如按客户拆分的 `elon_musk_table`、`john_doe_table`…），希望建**一个** cube 覆盖全部数据。
- **基础做法**：在 cube 的 `sql` 中用 `UNION ALL` 把多表合并成“union 表”，并可额外加一列标识来源（示例 `name`）。
- **痛点**：手写 UNION SQL 难维护、表多时很繁琐。
- **推荐做法**：结合动态数据模型（YAML+Jinja 或 JS）生成 UNION SQL：
  - 例如用字典 `customer_tables = { table_name: display_name }`；
  - 在 Jinja 循环里输出每个 `SELECT ... FROM table`，并用 `if not loop.last` 拼接 `UNION ALL`；
  - 再正常定义 measures/dimensions（如 `count`、`name`）。
- **效果**：新增/删除表只需修改列表/字典，Cube 模型自动生成对应的 UNION SQL。

