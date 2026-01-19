# Data Sources & Config Deep Dive (数据源与配置深度解析)

## 1. Concurrency (并发管理)
- **重点/约束**：
  - `CUBEJS_CONCURRENCY`：限制 Cube Server 同时处理的外部请求数量。
  - `CUBEJS_DB_MAX_POOL_SIZE`：数据库连接池上限。
  - **关键要求**：并发数必须小于连接池大小，以避免由于等待连接导致的请求超时。
  - **队列 (Queueing)**：Cube 会在内存中对超出的请求进行排队，长时间在队列中的请求会被强制断开。

## 2. Multitenancy Recipes (多租户实践)
- **Per-tenant Data Sources (分租户数据源)**：
  - 通过 `driver_factory` 根据 `security_context` 动态返回不同的数据库驱动和配置。
  - **要求**：必须在 `cube.js` 或 `cube.py` 的配置文件中实现该逻辑。
- **Per-tenant Data Models (分租户模型)**：
  - 使用 `repository_factory` 根据租户身份加载不同的模型文件。
  - **约束**：这种方式会增加内存消耗，因为每个租户的模型都需要独立编译和缓存。

## 3. Environment Variables & SSL (环境变量与安全)
- **关键变量**：
  - `CUBEJS_DB_SSL`: 强制启用 SSL。
  - `CUBEJS_DB_SSL_CA`, `CUBEJS_DB_SSL_CERT`: 指定证书。
  - **生产要求**：禁止在生产环境中使用未加密的连接访问包含敏感数据的数据仓库。
