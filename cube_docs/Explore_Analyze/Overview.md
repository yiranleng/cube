# Explore & Analyze, Present & Share

本章节涵盖了用户如何交互、探索和分享分析结果。

## Explore & Analyze (探索与分析)
- **Workbooks (工作簿)**：
  - 用于可视化探索。支持实时运行 SQL 和查看结果。
- **Analytics Chat (AI 聊天)**：
  - 自然语言查询接口。**要求**：模型必须定义 `description` 以供 AI 理解。
- **Playground (游乐场)**：
  - 开发者的测试工具，用于构建查询、验证模型并生成前端代码。

## Present & Share (展示与分享)
- **Dashboards (仪表板)**：
  - 组合多个图表。支持自动刷新和交互式过滤。
- **Embedding (嵌入)**：
  - **Private Embedding**：需要登录 Cube 账户。
  - **Signed Embedding (签名嵌入)**：通过 JWT 令牌提供安全访问，无需 Cube 账户。
  - **约束**：签名嵌入必须在后端生成令牌，不可在前端泄露密钥。
