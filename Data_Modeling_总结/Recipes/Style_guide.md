# Style guide - 风格指南

## 文章重点总结

- **语法**：默认 **YAML**、**snake_case**；遵循 YAML/SQL 推荐。
- **目录**：cubes 放 **model/cubes**、views 放 **model/views**；** 按业务单元分子目录（如 finance、sales）。
- **Cubes**：建议 **public: false**，只通过 **views** 对外；名称用复数业务实体，可加 **base_** 避免与 view 重名；优先 **sql_table** 而非 sql；用 **many_to_one / one_to_many / one_to_one**；参数顺序：name, sql_alias, extends, data_source, sql/sql_table, title, description, public, refresh_key, meta, pre_aggregations, joins, dimensions, hierarchies, segments, measures, access_policy。
- **Dimensions / Measures**：主键维度放首位；参数顺序：name, title, description, sql, type, primary_key, sub_query, public, format, filters, drill_members；不直观时用 **title**、**description**。
- **Views**：面向消费方与可视化；参数顺序：name, description, public, cubes, folders, access_policy。
- **SQL**：2 空格缩进、尾逗号、关键字大写、`!=`、`AS` 别名、多行换行、优先 **CTE** 而非子查询、JOIN 时列加表前缀、单引号、可读性优先。
- **YAML**：`.yml`、2 空格、列表缩进、行宽约 80、多行用 `|`、需引号时用双引号。
