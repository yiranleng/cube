# Integration & Specialized APIs (集成与专项 API)

## 1. Orchestration API (任务编排)
- **支持工具**：Airflow, Dagster, Prefect。
- **功能**：允许数据管道在数据仓库加载完成后，立即触发 Cube 预聚合的刷新。

## 2. Specialized APIs (专项 API)
- **DAX API**：支持 Power BI 的原生协议连接。
- **MDX API**：支持 Excel 等工具通过多维分析协议连接。
- **约束**：这些 API 目前可能处于预览或特定支持阶段，功能覆盖面可能窄于 REST/SQL API。

## 3. JavaScript SDK (前端 SDK)
- **重点/约束**：
  - 核心功能：处理异步查询、长轮询、自动重试和本地缓存。
  - **版本兼容性**：SDK 版本必须与 Cube Server 的主版本保持兼容。

## 4. Semantic Layer Sync
- **功能**：自动将 Cube 模型同步至 Tableau, Looker 等 BI 工具。
- **要求**：需要提供目标工具的 API 访问凭据。
