# Context variables - 上下文变量参考

## 文章重点总结

- 除 **CUBE** 外，在 sql、FILTER_PARAMS 等处可用：**COMPILE_CONTEXT**（含 security_context 等，编译时）、**SECURITY_CONTEXT**（查询时）、**FILTER_PARAMS**（查询过滤）等。用于动态 SQL、按租户/用户过滤、优化生成 SQL。详见文档中的列表与示例。
