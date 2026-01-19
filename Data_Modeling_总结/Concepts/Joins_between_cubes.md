# Joins between cubes - Cube 之间的连接

## 文章重点总结

### 概述
- **Joins** 在数据模型中建立 **cube 之间的关系**，用于：构建跨多个 cube 成员的复杂查询；在 calculated members、views、pre-aggregations 中引用其它 cube 的成员。
- 需理解：**join 类型**、**join 方向**、**join 路径**与 **join 提示（hints）**。

### Join 类型
- **one_to_one**、**one_to_many**、**many_to_one**（对应常见 SQL 关系）。
- 示例：`customers`–`orders`；在 `customers` 上 `one_to_many` 到 `orders` 时，FROM 为 customers、LEFT JOIN orders，**无客户**的订单（如游客结账）会被排除。
- 若需保留无客户订单：应把 join 放在 **orders** 上，用 **many_to_one** 到 `customers`；此时 FROM orders、LEFT JOIN customers，无 customer_id 的订单也会被查出。

### 多对多（Many-to-many）
- 多对多需**关联表**（junction table）；在 Cube 中需建**关联 cube**（如 `post_topics`），并分别声明：`posts` → `post_topics`（one_to_many）、`post_topics` → `topics`（many_to_one）。
- 关联表无主键时，可用 SQL 生成，如 `CONCAT(post_id, topic_id)`。
- **虚拟关联 cube**：库中无关联表、但业务上为多对多时，可用 `sql` 从现有表抽取并建**虚拟 cube**（如从 `emails` 抽取 `campaigns`），再在 `emails` 与 `transactions` 上通过该 cube 建立多对多（campaigns 上 `one_to_many` 到 transactions，emails `many_to_one` 到 campaigns）。

### Join 树与方向
- Cube 根据查询构建 **join tree**，按 **join 方向** 连接涉及到的 cube；若无法构建（如双向 join、菱形子图导致歧义）则查询失败。
- **方向**：join 从**定义 join 的 source cube** 指向**被引用的 target cube**；生成 SQL 时 source 在 JOIN 左侧、target 在右侧。**谁做 FROM、谁做 JOIN** 会直接影响结果（如是否保留“无匹配”行）。

### 双向 Join（Bidirectional）
- **一般不推荐** 在两个 cube 上互相定义 join；在部分分析场景（如同时需要“从 orders 到 customers”和“从 customers 到 orders”）下可使用，需注意 join 树与歧义；可通过 **join 路径**、**views** 等消除歧义。
