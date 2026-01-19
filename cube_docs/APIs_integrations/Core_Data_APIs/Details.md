# APIs Reference (API 参考)

Cube 提供的各种接口详情。

## 1. SQL API
- **兼容性**：Postgres 协议。
- **认证**：使用环境变量中的 `CUBEJS_API_SECRET` 生成的 JWT。
- **约束**：不支持 DDL 操作（CREATE/DROP），仅支持 SELECT。

## 2. REST API
- `/v1/load`: 加载数据。
- `/v1/meta`: 获取模型元数据。
- `/v1/dry-run`: 预览生成的 SQL 且不执行。
- **约束**：REST API 请求大小受服务器限制。

## 3. GraphQL API
- 提供 schema 内省（Introspection）。
- **优势**：允许前端按需请求字段，减少数据传输。

## 4. MCP Server
- **最新功能**：支持 Model Context Protocol，允许 AI 工具直接与 Cube 语义层集成。
