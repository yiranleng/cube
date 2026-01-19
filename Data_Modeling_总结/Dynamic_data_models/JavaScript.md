# JavaScript - 使用 JavaScript 的数据建模

## 文章重点总结

### 适用范围
- **仅适用于 JavaScript 数据模型**，不适用于 YAML；YAML 的类似能力见 Jinja and Python。

### asyncModule()
- **asyncModule(async () => { ... })**：在**执行环境**中可用，用于在**数据模型编译阶段末尾**注册异步逻辑，从而**动态添加** cube/view 等定义；适合从 API 拉取配置再生成模型的场景。
- 每次编译**仅调用一次**。

### 与静态 cube() 的差异
- 在 asyncModule 中通过 `cube()` 创建时，**dimensions** 和 **measures** 的 **sql** 必须为 **() => string**；**drill_members** 必须为 **() => string[]**。需将 API 返回的字符串转成**无参函数**。
- 建议在 **model/utils.js** 中写 `convertStringPropToFunction`、`transformDimensions`、`transformMeasures`，把 `sql`、`drill_members` 等统一转成函数形式后再传给 `cube()`。

### 典型用法
- **生成**：`fetch()` 取 JSON（如 `/dynamicCubes`），transform 后 `dynamicCubes.forEach(c => cube(c.name, { ... }))`。
- **schema_version**：在 `cube.js` 中把 **schema_version** 设为 **async 函数**（如根据 `securityContext` 从 API 取版本），可在底层数据或配置变更时触发**重新编译**。
- **COMPILE_CONTEXT**：在 asyncModule 中可用全局 **COMPILE_CONTEXT**（如 `securityContext.tenantId`），实现**多租户**：按租户从 API 拉取不同的 dimensions/measures，再生成各租户可见的 cube。
- **data_source**：**多数据源**时，异步创建的 cube 需显式设置 **data_source**，并在 `cube.js` 的 **driverFactory** 中为对应数据源配置驱动。
