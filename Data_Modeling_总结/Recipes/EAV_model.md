# EAV model - 实体-属性-值模型（EAV）

## 文章重点总结

- **场景**：EAV 将“实体”放在一张表，把大量“属性-值”对放在另一张表；每个实体拥有的属性集合不固定，呈**稀疏矩阵**。目标是在 Cube 中把“属性”建模成维度（或维度集合）。
- **示例问题**：以 `users` 为实体、`orders.status` 为属性，想得到每个用户在各 status 下**最早的订单创建时间**（属性值）。
- **静态属性（属性集合稳定）**：
  - 直接在 `sql` 里对每个属性写一条 join（例如对 completed/processing/shipped 分别 LEFT JOIN 一次），并在 SELECT 中计算 `MIN(created_at)`；
  - 再把这些结果列映射为多个 dimensions（如 `completed_created_at` 等）。缺点：属性集合变化时需要多处改动（SQL + dimensions）。
- **静态属性 + DRY（减少重复）**：
  - 改用 **JavaScript** 数据模型，把属性列表（如 `statuses = [...]`）作为数组；
  - 用函数生成 SELECT 片段与 JOIN 片段（`createValue/createJoin` 等），在循环中拼装 SQL 与维度定义，降低维护成本。
- **关键点**：当“属性集合”来源于维度枚举且变化频率较低，可用静态方案；当变化更频繁或希望复用，建议用 JS/动态方式生成（也可与动态数据模型结合）。

