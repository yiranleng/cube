# AI, Workspace & Monitoring (AI、工作空间与监控)

## 1. AI Agents & Rules
- **AI Agent Rules**：定义 AI 的语气、偏好和安全边界。
- **Agent Memories**：持久化保存 AI 从用户对话中学到的知识。
- **约束**：AI 的输出必须符合语义层定义的访问策略，不得越权。

## 2. Workspace Tools (工作空间工具)
- **Rollup Designer**：基于查询历史自动推荐并创建预聚合。
- **Query History**：精细化记录每个查询的耗时、扫描数据量和错误信息。
- **Visual Model**：可视化的 ER 图，用于理解 Cube 间的 Join 关系。

## 3. Monitoring & Observability (监控与可观测性)
- **集成的监控方案**：
  - Amazon CloudWatch
  - Datadog
  - Grafana Cloud
  - New Relic
- **关键指标**：
  - 查询延迟 (Query Latency)
  - 缓存命中率 (Cache Hit Ratio)
  - 预聚合构建时长 (Pre-aggregation build time)
  - 连接池使用率 (Pool Usage)
