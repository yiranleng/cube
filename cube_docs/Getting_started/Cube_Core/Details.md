# Cube Core (开源核心版)

本子目录介绍了 Cube 开源核心版的具体操作流程。

## 1. Create a project (创建项目)
- **重点/约束**：
  - 使用 CLI 命令 `npx cubejs-cli create <project-name> -d <db-type>`。
  - **环境要求**：需要 Node.js 环境。
  - **Docker 支持**：推荐使用 Docker Compose 运行 Cube、Redis 和 Cube Store。

## 2. Query data (查询数据)
- **重点/约束**：
  - 查询由 `measures`（度量）、`dimensions`（维度）和 `filters`（过滤器）组成。
  - **时间粒度**：`timeDimensions` 必须指定 `granularity`（如 day, month）。

## 3. Add a pre-aggregation (添加预聚合)
- **重点/约束**：
  - 预聚合定义在 `preAggregations` 块中。
  - **外部存储要求**：必须配置 `CUBEJS_EXTERNAL_DB_TYPE=cubestore`。
  - **匹配原则**：只有查询包含的度量和维度是预聚合定义的子集时，才会命中。

## 4. Learn more (深入学习)
- **重点/约束**：
  - 涉及高级主题，如多租户实现、自定义认证和性能调优。
