# Access control (访问控制)

定义谁可以访问哪些数据。

## 1. Access Policies (访问策略)
- **重点/约束**：
  - **集中式管理**：所有安全逻辑都在语义层定义。
  - **多维度控制**：
    - **Row-level security (行级安全)**：通过在 SQL 中注入过滤条件实现。
    - **Member-level security (成员级安全)**：控制哪些维度或度量对特定用户可见。

## 2. Authentication (认证)
- **重点/约束**：
  - **JWT (JSON Web Token)**：最常用的认证方式。
  - **Security Context**：JWT payload 中的自定义字段（如 `user_id`, `role`）会自动注入到 Cube 模型中，供策略逻辑使用。

## 3. Security Context (安全上下文)
- **重点/约束**：
  - 开发者可以在模型代码中访问 `securityContext` 变量。
  - **强制性过滤**：可以使用 `queryRewrite` 钩子强制在所有查询中添加租户 ID 等过滤条件。
