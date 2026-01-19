# lkml2cube package - lkml2cube 包参考

## 文章重点总结

- **lkml2cube** 用于从 **Looker LookML** 迁移到 Cube：解析 `.lkml`，生成或辅助生成 Cube 的 cubes/views（measures、dimensions、explores 等）。适合 Looker 用户向 Cube 迁移时复用语义。需在依赖中安装，按文档配置并调用转换逻辑。
