# Funnel analysis - 漏斗分析

## 文章重点总结

- **范围**：仅支持 **JavaScript** 数据模型（需 `require('Funnels')`），不支持 YAML。
- **漏斗定义**：对象标识（如 user/lead）、一组步骤（steps）、每步时间、步骤间转化时长（timeToConvert）。
- **实现方式**：Cube 内置 **Funnels** 包，使用 `Funnels.eventFunnel()` 生成可复用的漏斗 cube，并通过 **extends** 复用。
- **核心配置参数**：
  - **userId**：漏斗主体的唯一标识（sql）。
  - **time**：事件时间戳（sql）。
  - **steps**：步骤数组；每步含 `name`、`eventsView.sql`（该步事件数据集的 SQL），可选 `timeToConvert` 限制转化窗口。
  - **nextStepUserId**：当相邻步骤的用户标识会变化（匿名 → 登录）时，用于将前一步的 id 与后一步的新 id 关联。
- **生成 SQL 特点**：漏斗 SQL 通常较复杂（多步 JOIN/UNION/窗口约束等），但由包自动生成；最终可按 step 统计 conversions，并结合 time 范围过滤。

