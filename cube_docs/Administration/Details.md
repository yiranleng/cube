# Administration & Workspace (管理与工作空间)

关于环境管理、监控和部署的详细细节。

## 1. Workspace (工作空间)
- **SQL Runner**：直接对数据源运行 SQL，用于数据验证。
- **Query History**：记录所有查询、耗时和是否命中缓存。
- **Performance Insights**：分析查询瓶颈。

## 2. AI Agents & AI Space
- **Agent Rules**：定义 AI 代理的回答行为准则。
- **Agent Memories**：允许 AI 记住之前的查询上下文或用户偏好。

## 3. Deployment Options
- **BYOC (Bring Your Own Cloud)**：
  - 在客户自己的 AWS/GCP/Azure 账户中部署 Cube Cloud 实例。
  - **安全性要求**：需要配置 VPC Peering 或 PrivateLink 以访问数据源。

## 4. Multitenancy Strategy
- **Per-tenant data models**：为不同租户加载不同的模型文件。
- **Per-tenant data sources**：根据 `securityContext` 动态指定不同的数据库连接。
