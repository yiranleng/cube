# Getting started (快速入门)

本章节介绍了如何开始使用 Cube，包括连接数据、使用 AI 聊天分析、创建工作簿、在 IDE 中开发以及嵌入分析。

## 1. Connect your data (连接数据)
- **重点/约束**：
  - 支持主流云数据仓库（BigQuery, Snowflake, Redshift, Athena 等）。
  - 连接需要提供主机名、端口、数据库名、用户名和密码。
  - **安全性要求**：建议为 Cube 提供只读权限的数据库用户。对于生产环境，建议使用 SSL 连接。

## 2. Use analytics chat (使用分析聊天)
- **重点/约束**：
  - AI Agent 依赖语义层中的元数据（Measures, Dimensions）来理解查询。
  - **准确性要求**：模型定义的清晰度直接影响 AI 的回答准确性。必须在模型中提供清晰的描述（description）。

## 3. Create workbooks and dashboards (创建工作簿和仪表板)
- **重点/约束**：
  - 工作簿（Workbooks）是进行即席分析的地方。
  - 仪表板（Dashboards）用于持久化可视化结果。
  - 支持将工作簿中的图表直接保存到仪表板。

## 4. Develop in IDE (在 IDE 中开发)
- **重点/约束**：
  - Cube Cloud 提供在线 IDE，支持实时预览数据模型。
  - **开发流程**：通过 Git 集成进行版本控制。更改必须先在开发分支进行，通过预览测试后再合并到生产。

## 5. Embed analytics (嵌入分析)
- **重点/约束**：
  - 支持通过 JavaScript SDK 嵌入图表。
  - **安全性要求**：嵌入通常涉及生成带有签名（JWT）的令牌，以确保数据安全。

## 6. Cube Core (Cube 核心版)
- **重点/约束**：
  - Cube Core 是开源版，适合本地开发或自托管。
  - **环境要求**：支持 Docker 部署。
  - **基础工作流**：
    1. 创建项目 (`npx cubejs-cli create`).
    2. 配置环境变量 (`.env`).
    3. 生成数据模型 (`generate`).
    4. 运行服务.
