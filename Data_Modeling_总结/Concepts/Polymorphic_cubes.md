# Polymorphic cubes - 多态 Cube

## 文章重点总结

### 概述
- **多态**：用同一符号表示多种类型；数据库中常见**单表存多种实体**（通过 `type` 等列区分），如 `users` 表中 `type` 为 teacher/student。
- **Polymorphic cubes**：基于 **extends**，从同一基表派生出多个**子 cube**，各子 cube 用 `sql` 过滤出对应类型，避免重复定义同时保持正确领域语义。

### 建模步骤
1. **建基 cube**（如 `users`）：包含**共有**的 measures、dimensions；`sql` 或 `sql_table` 指向整张表。
2. **派生子 cube**（如 `teachers`、`students`）：`extends: users`，**覆盖 `sql`**：`SELECT * FROM ${users.sql()} WHERE type = 'teacher'`（或 `'student'`）。`${users.sql()}` 引用父 cube 的 SQL，再加 `WHERE` 过滤。
3. **在关联 cube 上建 join**：如 `lessons` 分别 `many_to_one` 到 `students`（`student_id`）和 `teachers`（`teacher_id`），这样 `teacher_id`/`student_id` 与正确的“类型子集”对应，语义清晰。

### 使用场景
- 单表多实体：users（教师/学生）、events（多种事件类型）等；需要分别统计、分别 join 时，用多态 cube 比在一个 cube 里加很多 `type` 过滤更清晰、可复用。
