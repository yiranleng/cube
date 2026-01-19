# Data Sources & Config (数据源与配置)

本章节介绍了如何连接各种数据源以及相关的配置选项。

## 1. Data sources (数据源)
- **重点/约束**：
  - 支持 50+ 种数据源，包括 Athena, BigQuery, Snowflake, ClickHouse, MySQL, Postgres 等。
  - **连接要求**：必须配置正确的驱动程序（在 Core 中）或在 Cloud 中直接配置。
  - **权限要求**：Cube 需要对元数据表和数据表具有读取权限。

## 2. Multiple data sources (多数据源)
- **重点/约束**：
  - 一个 Cube 项目可以连接多个不同的数据库。
  - **跨库 Join**：Cube 支持跨数据源的联接，但性能可能受限。建议对于大数据量跨库，使用预聚合。

## 3. Concurrency (并发控制)
- **重点/约束**：
  - `CUBEJS_CONCURRENCY`：控制同时处理的查询数量。
  - **排队机制**：超出并发限制的查询会进入队列。

## 4. Multitenancy (多租户)
- **重点/约束**：
  - 支持通过 `securityContext` 实现租户隔离。
  - **动态连接**：可以根据租户身份动态切换数据库连接或架构（Schema）。
  - **上下文变量**：在数据模型中可以使用 `COMPILE_CONTEXT` 来引用租户信息。

## 5. Configuration (配置)
- **重点/约束**：
  - **环境变量**：所有核心配置（如数据库连接、API 密钥、缓存设置）都通过环境变量管理。
  - **JavaScript 配置**：`cube.py` 或 `cube.js` 可用于更复杂的动态配置逻辑。
