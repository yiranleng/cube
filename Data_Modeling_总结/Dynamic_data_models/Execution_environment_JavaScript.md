# Execution environment (JavaScript) - 执行环境（JavaScript）

## 文章重点总结

### 运行方式
- 数据模型由 **Node.js VM** 执行：便于预编译、从外部库加载模型、在沙箱中执行。语法为 **Node 8+ 的 JavaScript**，有若干限制与扩展。

### require
- 模型内**没有**原生 Node `require`；Cube 的 **require()** 由 Data Model Compiler 提供，可加载：其它 model 文件、普通 Node 模块、以及 **Cube 包**（如 Funnels）。

### Node 全局与 process.env、console
- **process、console** 等 Node 全局在 model 内**不可用**。需要 **process.env** 时：在 **model 目录外**（如项目根）写 `tablePrefix.js`，`exports.tableSchema = () => process.env.TABLE_SCHEMA`，再在 model 中 `import { tableSchema } from "../tablePrefix"` 使用。
- **console.log** 在 VM 中不可用；复杂 SQL 生成与调试建议放到 model 外的**辅助服务**中，按普通 Node 调试。

### Cube 全局
- **cube()**、**context()**、**asyncModule()** 为 Cube 在模型上下文中注入的全局函数。

### import / export
- ES6 **import/export** 会被转译成对应 require/export；支持 **named** 与 **default** 导出。

### asyncModule
- 通过 **asyncModule()** 做异步加载、从外部存储拉取模型；参见 JavaScript 动态数据模型。

### Context 与转译（Context symbols transpile）
- 对 cube/context 中部分**保留属性**（如 **sql、measures、dimensions、segments、time_dimension、drill_members、context_members**），Cube 会做**引用解析转译**：将 `${CUBE}`、`${count}` 等转成**函数参数**，例如 `sql: (CUBE, count) => \`SUM(${CUBE}.amount) / ${count}\``。
- 若要把这类定义**抽到 cube 外**（如变量、工厂函数），需**手写为函数形式** `(CUBE, count) => ...`，否则转译器不会注入参数。
