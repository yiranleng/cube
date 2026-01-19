# Joins - 连接参考

## 文章重点总结

- 在 cube 的 **joins** 中定义：**name**（目标 cube）、**sql**（ON 条件，如 `${CUBE}.id = ${orders.user_id}`）、**relationship**（one_to_one、one_to_many、many_to_one）。可选：**alias**（join 别名，用于歧义路径）等。详见 Concepts / Joins between cubes。
