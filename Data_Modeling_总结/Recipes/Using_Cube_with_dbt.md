# Using Cube with dbt - 将 Cube 与 dbt 结合使用

## 文章重点总结

- **定位**：dbt 负责在数仓中做数据转换（staging/intermediate → marts），Cube 在其上定义 cubes/views（维度/度量/joins/pre-aggregations）并通过 API 对外提供语义层。
- **数据准备最佳实践**：
  - **时间字段**：在 dbt 模型里尽量把各类时间格式统一转换为 **TIMESTAMP**，便于在 Cube 里用 `type: time`。
  - **refresh keys**：建议每个 dbt 模型有 `updated_at`，Cube `refresh_key.sql` 可用 `SELECT MAX(updated_at) FROM ...`；若使用 dbt snapshots，可用 `dbt_valid_from/dbt_valid_to` 组合构造 refresh_key（如 `CONCAT(COUNT(*), MAX(dbt_valid_from), MIN(dbt_valid_to))`）。
  - **预聚合刷新**：pre-aggregations 有自己的 refresh key；可结合 Airflow/Dagster/Prefect 等通过 Orchestration API 在 dbt 更新后触发刷新。
- **cube_dbt 集成流程**（面向 YAML+Jinja+Python 的动态模型最友好）：
  1. **安装**：在项目根 `requirements.txt` 加入 `cube_dbt`。
  2. **加载 dbt 元数据**：基于 `manifest.json`（dbt build/run 会生成），可本地 `Dbt.from_file()` 或远程 `Dbt.from_url()`；也可自行拉取后 `json.loads()` 再 `Dbt(manifest_dict)`。
  3. **筛选模型**：可按 `paths`（如 `marts/`）、`tags`、`names` 过滤，默认排除 ephemeral。
  4. **在 Jinja 中渲染为 cubes**：在 `model/globals.py` 里用 `TemplateContext` 暴露 `dbt_models()`/`dbt_model(name)`；模板中遍历并用 `model.as_cube()` 生成 `name/sql_table`，用 `model.as_dimensions()` 把列渲染为 dimensions。
  5. **再补充**：对生成的 cubes 添加 measures、joins、pre-aggregations，并用 views 作为最终数据产品对外暴露。

