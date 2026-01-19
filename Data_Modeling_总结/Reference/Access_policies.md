# Access policies - 数据访问策略参考

## 文章重点总结

- **access_policies** 在 cube 或 view 中定义，用于**行级/列级**访问控制。通常为 **rules** 数组，每条含 **grant**、**filter**（SQL 或 `FILTER_PARAMS` 等）、**allowed_measures**、**allowed_dimensions** 等，可按 `SECURITY_CONTEXT` 动态限制可见数据与成员。详见 Access control 文档。
