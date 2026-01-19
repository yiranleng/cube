# APIs & integrations (API 与集成)

Cube 作为一个头无（Headless）BI，通过多种 API 提供服务。

## 1. REST API
- **重点/约束**：
  - 用于获取查询结果、元数据和管理任务。
  - 结果通常以 JSON 格式返回。

## 2. SQL API
- **重点/约束**：
  - **Postgres 兼容**：任何支持 Postgres 的 BI 工具（如 Tableau, Superset, PowerBI）都可以连接 Cube。
  - **Semantic SQL 扩展**：支持直接在 SQL 中使用 Cube 定义的度量。

## 3. GraphQL API
- **重点/约束**：
  - 提供强类型的查询接口，适合现代前端应用。

## 4. SDKs
- **JavaScript SDK**：支持 React, Vue, Angular。处理状态管理、重试逻辑和结果缓存。

## 5. Semantic Layer Sync
- **重点/约束**：
  - 自动将 Cube 的定义同步到下游工具（如 Tableau 语义模型）。
  - **要求**：需要配置对应的同步插件或集成。
