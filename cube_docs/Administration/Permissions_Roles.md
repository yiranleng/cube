# Permissions, Roles & Governance (权限、角色与治理)

## 1. Roles & Permissions (角色与权限)
- **预设角色**：Admin, Editor, Viewer。
- **Custom Roles (自定义角色)**：在 Cube Cloud 中，支持定义具有特定路径访问权限的粒度化角色。

## 2. User Attributes & Groups (用户属性与组)
- **User Attributes**：可以为每个用户定义额外的元数据（如 `department_id`），这些属性会自动进入 `security_context`。
- **User Groups**：批量管理用户权限。

## 3. Governance & Audit Log (治理与审计)
- **Audit Log**：记录所有用户在工作空间内的操作（如修改模型、删除仪表板）。
- **要求**：企业级客户通常需要定期导出审计日志以满足合规性要求。
- **Versioning**：所有模型变更必须关联到 Git Commit ID。
