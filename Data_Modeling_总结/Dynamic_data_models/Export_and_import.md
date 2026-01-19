# Export and import - 导出与导入

## 文章重点总结

### 适用范围
- **仅适用于 JavaScript 数据模型**，不适用于 YAML。

### 目的与原则
- 数据模型即代码；**小块、无重复**更易维护和调试。通过 **ES6 export/import** 在文件间共享常量、函数，便于复用与组织。

### 常见用法
- **常量管理**：在 `constants.js` 中 `export const TEST_USER_IDS = [1,2,3,...]`，在 cube 中 `import { TEST_USER_IDS } from "./constants"`，用于 segments、filters 等（如 `id NOT IN (${TEST_USER_IDS.join(", ")})`）。
- **辅助函数**：在 `helpers.js` 中 `export const parseDateWithTimeZone = (column) => \`PARSE_TIMESTAMP(...)\``，在多个 cube 的 dimensions 中 `import { parseDateWithTimeZone } from "./helpers"` 并调用，便于统一处理字符串时间等；参见 string time dimensions 配方。
- **从父级或兄弟目录导入**：Cube 会扁平化 model 子目录，可用相对路径如 `../shared_utils/utils` 从父级或兄弟目录 import；注意路径需与实际目录结构一致。
