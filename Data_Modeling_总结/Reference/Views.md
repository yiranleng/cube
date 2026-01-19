# Views - 视图参考

## 文章重点总结

### 定义与用途
- **View** 位于 cubes 数据图之上，作为**门面**供消费方使用；用于：定义指标、治理与访问控制、消除歧义 join 路径。必选参数：**name**、**cubes**。

### 主要参数
- **name**：唯一标识。
- **extends**：继承另一 view 的成员，再通过 **cubes** 追加 join_path 等。
- **title**、**description**、**public**：与 cube 类似；**public** 可用 `COMPILE_CONTEXT.security_context.xxx` 做动态可见性。
- **meta**：自定义元数据，可传给前端。
- **cubes**：数组，每项为 `{ join_path, includes [, excludes, prefix, alias, title, description, format, meta ] }`。
  - **join_path**：点分路径，如 `base_orders`、`base_orders.line_items.products`。
  - **includes**：要包含的成员列表；`"*"` 表示全部；可为 `{ name, alias, title, description, format, meta }` 对象以覆盖属性。
  - **excludes**：在 includes 为 `*` 时排除的成员。
  - **prefix**：true 时成员加 cube 名前缀；可用 **alias** 自定义前缀或重命名成员。
- **folders**：将成员分组；每项 `{ name, includes }`；**includes** 可含成员名或嵌套 folder；支持多级嵌套。展示依赖可视化工具与 Playground。
