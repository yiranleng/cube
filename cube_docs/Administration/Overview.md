# Administration (管理)

本章节涵盖了 Cube 的部署、权限管理和监控。

## 1. Users & permissions (用户与权限)
- **重点/约束**：
  - 基于角色的访问控制 (RBAC)。
  - 在 Cube Cloud 中，可以定义管理员、编辑者和查看者。

## 2. Deployment (部署)
- **重点/约束**：
  - **Cube Cloud**：全托管服务，提供自动扩展、备份和监控。
  - **Self-hosted (自托管)**：使用 Docker 或 Kubernetes。**要求**：需要自行配置 Cube Store 和 Redis (用于缓存元数据)。

## 3. Monitoring (监控)
- **重点/约束**：
  - 提供查询历史、性能分析和预聚合状态。
  - 支持集成 Datadog, Prometheus 等第三方监控工具。

## 4. Development mode vs Production mode
- **重点/约束**：
  - **开发模式**：允许动态生成模型，响应速度较慢，不缓存结果。
  - **生产模式**：模型已编译，强制执行预聚合和缓存策略，具有更高的安全性和性能。
