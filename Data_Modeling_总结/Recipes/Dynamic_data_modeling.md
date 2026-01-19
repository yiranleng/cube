# Dynamic data modeling - 动态生成数据模型（动态度量）

## 文章重点总结

- **场景**：希望按订单状态（status）统计分布比例，但 status 可能新增，或来自外部 API；不想每次都手动改模型。
- **思路**：用 JavaScript **程序化生成 measures**：
  - 一个总量度量 `total_orders`（count）
  - 对每个 status 生成：
    - `total_<status>_orders`：带 filters 的 count（`status = '<status>'`）
    - `percentage_of_<status>`：计算度量，引用 `total_<status>_orders` 与 `total_orders`，做比例并格式化为 percent
- **实现要点**：
  - 用数组 `statuses = [...]` 驱动生成；
  - 用函数工厂 `createTotalByStatusMeasure(status)`、`createPercentageMeasure(status)` 返回 measure 定义；
  - 用 `statuses.reduce(...)` + `Object.assign(...)` 合并到 `cube('orders', { measures: ... })`；
  - SQL 中需注意类型转换与小数计算（示例用 `ROUND(... * 100.0, 2)`）。
- **效果**：新增状态只需在 `statuses` 列表中加一项（或从 API 获取后填充），即可自动生成对应度量与百分比度量。

