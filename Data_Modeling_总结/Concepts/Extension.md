# Extension - 扩展（extends）

## 文章重点总结

### 概述
- **extends** 参数可用于 **cubes** 和 **views**：子 cube/view **复用父级**已声明的全部成员，便于构建可复用数据模型。
- 子 cube **继承**父 cube 的 measures、dimensions、segments、joins 等，**列表会合并**：父有 `a`、子有 `b`，则子拥有 `a` 和 `b`。
- 常见做法：把通用 measures、dimensions、joins 放到**父 cube**，子 cube 只定义**特有**部分，减少重复、便于维护和重构。

### 使用要点
- 在父、子 cube 中引用列或成员时，应使用 **`CUBE`** 变量，**不要**写死父 cube 名（如 `${base_events}`），否则 extends 时可能出错。
- 子 cube 需设置自己的 **sql_table**（或 **sql**），与父 cube 的 `sql_table`/`sql` 无关；继承的是成员定义，不是基表。

### 与 FILTER_PARAMS 配合
- 若父 cube 在 `sql` 中使用了 **FILTER_PARAMS**，子 cube 有两种方式：
  1. **在子 cube 中覆盖 `sql`**：按子 cube 名写 `FILTER_PARAMS.child_cube.xxx.filter('time')` 等。
  2. **在父 cube 集中写所有子 cube 的过滤**：用 `AND` 串联 `FILTER_PARAMS.base_events...`、`FILTER_PARAMS.product_purchases...`、`FILTER_PARAMS.page_views...`；对当前子 cube 未用到的会渲染为 `1 = 1`，不影响结果。
