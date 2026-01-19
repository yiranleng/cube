# Cube 定义 360 度评估标准（360° Data Modeling Assessment）

> 参考文档：[Cube.dev Documentation](https://cube.dev/docs/product/introduction)

---

## 一、评估说明

- **目标**：为 Cube 数据建模提供可量化的优劣判定基准，确保模型性能、可维护性与安全性。
- **结构**：采用"整体治理 → 对象定义 → 性能运维 → 安全策略"的下钻结构。
- **应用场景**：代码审查（Code Review）、性能调优、AI 生成模型校验。
- **评估等级**：
  - **【优】** - 最佳实践，推荐采用
  - **【良】** - 可接受，有改进空间
  - **【劣】** - 存在问题，需要修改
  - **【严重】** - 可能导致错误或安全问题

---

## 二、整体架构与治理评估

### 2.1 数据模型架构

| 评估项 | 【优】 | 【劣】 |
|-------|--------|--------|
| **暴露策略** | 所有底层 Cube 设为 `public: false`；仅通过 View 暴露精选成员 | 直接暴露底层 Cube；缺乏 View 层封装，内部物理表结构直接泄露 |
| **Cube 与 View 关系** | Cube 定义业务实体和计算逻辑；View 作为数据产品面向消费者 | Cube 和 View 职责混淆；View 中定义复杂逻辑 |
| **层次结构** | 清晰的分层：物理表 → Cube → View → API | 扁平结构，缺乏抽象层 |

### 2.2 命名与语义一致性

| 评估项 | 【优】 | 【劣】 |
|-------|--------|--------|
| **Cube 命名** | 复数实体命名（如 `orders`, `users`）；统一 `snake_case` | 单数命名或数据库物理表名（如 `tbl_ord_df`）；命名中包含动词（如 `get_orders`） |
| **成员命名** | Measure：动作/聚合含义（`total_revenue`, `count`）；Dimension：属性含义（`created_at`, `status`） | 模糊命名（如 `m1`, `val`, `d1`）；大小写混用 |
| **View 命名** | 体现业务主题（如 `sales_performance`, `customer_360`） | 与底层 Cube 同名或无意义命名 |

### 2.3 文件组织与模块化

| 评估项 | 【优】 | 【劣】 |
|-------|--------|--------|
| **目录结构** | 按业务域分子目录（`model/cubes/finance/`） | 所有定义堆在一个巨型文件中 |
| **文件粒度** | 一个文件仅定义一个主要 Cube 或 View | 缺乏子目录划分，导致文件查找困难 |
| **代码复用** | 使用 `extends` 继承公共定义；动态模型用于重复模式 | 复制粘贴代码；无抽象复用 |

---

## 三、Cube 定义评估

### 3.1 核心参数评估

| 参数 | 必填 | 【优】 | 【劣】 |
|------|------|--------|--------|
| **name** | ✅ | 唯一标识符；遵循命名规范；复数形式 | 与其他 Cube/View 重名；使用保留字 |
| **sql_table** | 推荐 | 优先使用 `sql_table` 引用物理表 | 不存在的表名；未使用 schema 前缀 |
| **sql** | 替代 | 仅包含简单清洗逻辑；无 `GROUP BY` | 复杂子查询；在 Cube 级写 `GROUP BY` 破坏动态聚合 |
| **public** | - | 底层 Cube 设为 `false`；仅 View 对外暴露 | 敏感 Cube 设为 `true` 直接暴露 |
| **title** | - | 提供人类可读的显示名称 | 依赖自动转换，名称不清晰 |
| **description** | - | 所有 Cube 都有业务说明 | 缺失描述，无法理解用途 |

### 3.2 sql_table vs sql 选择

```
决策树：
├── 数据来源是单张物理表？
│   └── 是 → 使用 sql_table: `schema.table_name`
│   └── 否 → 使用 sql 参数
├── 需要简单过滤或别名？
│   └── 是 → 使用 sql: `SELECT * FROM table WHERE ...`
├── 需要复杂 JOIN 或窗口函数？
│   └── 是 → 使用 sql 参数，考虑 original_sql 预聚合
```

**【严重】** 在 `sql` 中使用 `GROUP BY` 会破坏 Cube 的动态聚合能力！

### 3.3 高级参数评估

| 参数 | 【优】 | 【劣】 |
|------|--------|--------|
| **data_source** | 多数据源场景正确配置 | 未配置导致查错数据库 |
| **sql_alias** | 长名称 Cube 配置短别名（避免 Postgres 截断） | 生成的 SQL 别名过长被截断 |
| **extends** | 复用公共定义减少重复代码 | 过度继承导致关系混乱 |
| **refresh_key** | 配置 `MAX(updated_at)` 或合理的 `every` 间隔 | 未配置导致数据不刷新或频繁刷新 |
| **calendar** | 自定义日历场景设为 `true` | 普通 Cube 误设为 `true` |

### 3.4 refresh_key 配置最佳实践

```yaml
# 【优】基于数据变化的刷新
refresh_key:
  sql: SELECT MAX(updated_at) FROM orders

# 【优】固定间隔刷新（适用于批处理数据）
refresh_key:
  every: 1 hour

# 【优】CRON 表达式（指定时区）
refresh_key:
  every: "30 5 * * *"
  timezone: "America/Los_Angeles"

# 【劣】未配置，使用默认值（10秒或2分钟）
# refresh_key: # 缺失
```

---

## 四、Dimension 定义评估

### 4.1 核心参数评估

| 参数 | 必填 | 【优】 | 【劣】 |
|------|------|--------|--------|
| **name** | ✅ | 唯一标识符；遵循命名规范 | 与其他成员重名 |
| **sql** | ✅ | 简单列引用或表达式 | 包含聚合函数（应定义为 Measure） |
| **type** | ✅ | 正确选择类型（见下表） | 类型与数据不匹配 |
| **primary_key** | 关键 | 每个 Cube **必须定义**主键维度 | 缺失主键导致 Join 产生 Fan-out |

### 4.2 Dimension 类型选择

| 类型 | 适用场景 | 【优】示例 | 【劣】示例 |
|------|----------|-----------|-----------|
| **string** | 文本属性 | `status`, `name`, `category` | 用于数字 ID |
| **number** | 数值属性 | `price`, `quantity`, `age` | 用于金额（应用 format） |
| **boolean** | 布尔标志 | `is_active`, `has_paid` | 用 string 存储 'true'/'false' |
| **time** | 时间戳 | `created_at`, `updated_at` | 字符串时间直接定义为 string |
| **geo** | 地理坐标 | `[longitude, latitude]` | 分开定义经纬度为 number |

### 4.3 primary_key 规则

```yaml
# 【优】显式定义主键
dimensions:
  - name: id
    sql: id
    type: number
    primary_key: true  # 设置后 public 默认变为 false

# 【优】复合主键（多个 primary_key: true）
dimensions:
  - name: order_id
    sql: order_id
    type: number
    primary_key: true
  - name: line_number
    sql: line_number
    type: number
    primary_key: true

# 【严重】缺失主键 - Join 会产生重复计算！
dimensions:
  - name: id
    sql: id
    type: number
    # primary_key: true  # 缺失！
```

### 4.4 时间维度最佳实践

| 评估项 | 【优】 | 【劣】 |
|--------|--------|--------|
| **类型声明** | `type: time` | `type: string` 存储时间字符串 |
| **默认粒度** | 使用内置粒度：year, quarter, month, week, day, hour, minute, second | 手动计算粒度 |
| **自定义粒度** | 使用 `granularities` 参数定义财年、自定义周等 | 硬编码粒度计算 |

```yaml
# 【优】自定义粒度示例
dimensions:
  - name: created_at
    sql: created_at
    type: time
    granularities:
      - name: fiscal_year
        interval: 1 year
        origin: "2025-04-01"  # 财年起始日
      - name: week_starting_sunday
        interval: 1 week
        offset: -1 day
```

### 4.5 其他重要参数

| 参数 | 【优】 | 【劣】 |
|------|--------|--------|
| **public** | 敏感字段（如 ID、密码哈希）设为 `false` | 暴露敏感信息 |
| **format** | 使用 `id` 格式确保数字 ID 不被千分位分隔 | 数字 ID 显示为 "1,000" |
| **case** | 用于条件映射维度值 | 在 sql 中写复杂 CASE WHEN |
| **sub_query** | 需要引用其他 Cube 的 Measure 时使用 | 手动写子查询 |
| **order** | 设置 `asc` 或 `desc` 定义默认排序 | 每次查询指定排序 |

---

## 五、Measure 定义评估

### 5.1 核心参数评估

| 参数 | 必填 | 【优】 | 【劣】 |
|------|------|--------|--------|
| **name** | ✅ | 唯一标识符；表达聚合含义 | 模糊命名（`m1`, `val`） |
| **sql** | 条件 | 根据 type 正确配置（见下表） | 类型与 sql 不匹配 |
| **type** | ✅ | 优先使用内置聚合类型 | 滥用 `type: number` 手写聚合 |

### 5.2 Measure 类型与 sql 参数

| 类型 | sql 参数要求 | 【优】示例 | 性能 |
|------|-------------|-----------|------|
| **count** | 可省略或指定列 | `type: count` | ⭐⭐⭐ 可加性 |
| **sum** | 非聚合表达式 | `sql: amount` `type: sum` | ⭐⭐⭐ 可加性 |
| **avg** | 非聚合表达式 | `sql: price` `type: avg` | ⭐⭐ 非加性 |
| **min** | 非聚合表达式 | `sql: created_at` `type: min` | ⭐⭐⭐ 可加性 |
| **max** | 非聚合表达式 | `sql: updated_at` `type: max` | ⭐⭐⭐ 可加性 |
| **count_distinct** | 非聚合表达式 | `sql: user_id` `type: count_distinct` | ⭐ 非加性，慢 |
| **count_distinct_approx** | 非聚合表达式 | `sql: user_id` `type: count_distinct_approx` | ⭐⭐ HyperLogLog |
| **number** | 包含聚合函数的完整表达式 | `sql: "SUM(a) / COUNT(b)"` `type: number` | ⚠️ 无法预聚合加速 |

### 5.3 计算度量最佳实践

```yaml
# 【优】使用内置聚合类型
measures:
  - name: total_revenue
    sql: amount
    type: sum

  - name: order_count
    type: count

  - name: unique_customers
    sql: customer_id
    type: count_distinct_approx  # 大数据量使用近似去重

# 【优】计算度量 - 引用其他度量
measures:
  - name: average_order_value
    sql: "{total_revenue} / NULLIF({order_count}, 0)"  # 防除零
    type: number

# 【优】带过滤条件的度量
measures:
  - name: completed_orders_count
    sql: id
    type: count
    filters:
      - sql: "{CUBE}.status = 'completed'"

# 【劣】手写聚合 - 无法被预聚合加速
measures:
  - name: total_amount
    sql: "SUM(amount)"  # 应改为 type: sum
    type: number
```

### 5.4 多阶段计算（Multi-stage）

```yaml
# 【优】时间偏移计算（同比/环比）
measures:
  - name: revenue
    sql: revenue
    type: sum

  - name: revenue_prior_year
    multi_stage: true
    sql: "{revenue}"
    type: number
    time_shift:
      - time_dimension: created_at
        interval: 1 year
        type: prior

  - name: revenue_yoy_change
    multi_stage: true
    sql: "({revenue} - {revenue_prior_year}) / NULLIF({revenue_prior_year}, 0)"
    type: number
    format: percent
```

### 5.5 滚动窗口计算

```yaml
# 【优】滚动窗口度量
measures:
  - name: rolling_30d_revenue
    sql: amount
    type: sum
    rolling_window:
      trailing: 30 day

  - name: cumulative_revenue
    sql: amount
    type: sum
    rolling_window:
      trailing: unbounded  # 累计值

# 注意：滚动窗口需要查询包含时间维度和日期范围
```

### 5.6 其他重要参数

| 参数 | 【优】 | 【劣】 |
|------|--------|--------|
| **format** | 配置 `currency`, `percent` 等格式 | 无格式，前端需额外处理 |
| **drill_members** | 配置下钻维度便于数据探索 | 无下钻配置 |
| **public** | 内部计算度量设为 `false` | 暴露中间计算度量 |
| **filters** | 使用 `filters` 参数而非 sql 硬编码 | 在 sql 中 WHERE 硬编码 |

---

## 六、Join 定义评估

### 6.1 Join 类型选择

| 关系类型 | 使用场景 | 示例 |
|----------|---------|------|
| **many_to_one** | 多对一（推荐首选） | orders → customers |
| **one_to_many** | 一对多 | authors → books |
| **one_to_one** | 一对一 | users → profiles |

**重要**：Cube 只支持 `LEFT JOIN`，所有 Join 都从定义 Join 的 Cube 出发。

### 6.2 Join 定义规则

```yaml
# 【优】正确的 Join 定义
cube:
  name: orders
  sql_table: orders

  joins:
    - name: customers
      relationship: many_to_one  # 多个订单对应一个客户
      sql: "{CUBE}.customer_id = {customers.id}"

  dimensions:
    - name: id
      sql: id
      type: number
      primary_key: true  # 必须有主键！

# 【严重】缺失主键会导致 Fan-out（重复计算）
```

### 6.3 Join 最佳实践

| 评估项 | 【优】 | 【劣】 |
|--------|--------|--------|
| **主键定义** | 被 Join 的 Cube 必须定义 `primary_key` | 缺失主键导致计数翻倍 |
| **关联条件** | `sql` 仅包含主外键关联 | 关联条件包含业务过滤（应移至 Segment） |
| **关系方向** | 明确 `many_to_one`（推荐）或其他关系 | 关系类型错误导致结果错误 |
| **传递性** | 利用 Transitive Joins 减少冗余关联 | 重复定义相同关联 |
| **循环 Join** | 避免 Join Loop | 存在循环路径导致查询引擎无法解析 |

### 6.4 常见错误

```yaml
# 【严重】Join 条件包含业务逻辑
joins:
  - name: active_customers
    relationship: many_to_one
    sql: "{CUBE}.customer_id = {customers.id} AND {customers}.status = 'active'"
    # 应将 status = 'active' 移至 Segment

# 【正确】纯关联条件
joins:
  - name: customers
    relationship: many_to_one
    sql: "{CUBE}.customer_id = {customers.id}"

segments:
  - name: with_active_customer
    sql: "{customers}.status = 'active'"
```

---

## 七、Pre-aggregations 定义评估

### 7.1 预聚合类型选择

| 类型 | 适用场景 | 存储位置 |
|------|---------|---------|
| **rollup** | 标准聚合查询（推荐） | Cube Store |
| **original_sql** | 复杂 SQL（窗口函数、嵌套子查询） | 数据源 |
| **rollup_join** | 跨数据源 Join | Cube Store |
| **rollup_lambda** | 实时数据 + 历史数据混合 | Cube Store |

### 7.2 Rollup 预聚合最佳实践

```yaml
# 【优】高频查询路径的 rollup
pre_aggregations:
  - name: orders_by_status_daily
    measures:
      - count
      - total_amount
    dimensions:
      - status
    time_dimension: created_at
    granularity: day
    partition_granularity: month  # 大数据量必须分区
    refresh_key:
      sql: "SELECT MAX(updated_at) FROM orders"

# 【优】增量更新配置
pre_aggregations:
  - name: orders_incremental
    measures:
      - count
    time_dimension: created_at
    granularity: day
    partition_granularity: month
    incremental: true
    update_window: 7 day  # 增量更新窗口
```

### 7.3 可加性度量评估

| 度量类型 | 可加性 | 预聚合性能 |
|---------|--------|-----------|
| count | ✅ 可加 | ⭐⭐⭐ 最佳 |
| sum | ✅ 可加 | ⭐⭐⭐ 最佳 |
| min | ✅ 可加 | ⭐⭐⭐ 最佳 |
| max | ✅ 可加 | ⭐⭐⭐ 最佳 |
| count_distinct_approx | ✅ 可加 (HLL) | ⭐⭐ 良好 |
| avg | ❌ 非加性 | ⭐ 需特殊处理 |
| count_distinct | ❌ 非加性 | ⚠️ 无法跨分区聚合 |

### 7.4 预聚合配置规则

| 评估项 | 【优】 | 【劣】 |
|--------|--------|--------|
| **分区** | 大数据量开启 `partition_granularity` | 未开启导致全量重刷 |
| **维度选择** | 仅包含查询需要的维度 | 包含高基数维度（如 `user_id`） |
| **度量选择** | 优先使用可加性度量 | 大量非加性度量 |
| **刷新策略** | 配置 `refresh_key` 或 `incremental` | 依赖默认刷新 |
| **数量控制** | 合理数量的预聚合 | 过多相似预聚合导致存储爆炸 |

### 7.5 rollup_join 和 rollup_lambda

```yaml
# 【优】跨数据源 Join
pre_aggregations:
  - name: orders_with_users
    type: rollup_join
    measures:
      - count
    dimensions:
      - users.name
    rollups:
      - users.users_rollup
      - orders_rollup
    # 注意：rollup_join 不需要设置 scheduled_refresh

# 【优】Lambda 预聚合（实时 + 历史）
pre_aggregations:
  - name: orders_lambda
    type: rollup_lambda
    union:
      - orders_historical  # 历史数据预聚合
      - orders_realtime    # 实时数据源
    # rollup_lambda 必须定义在其他预聚合之前
```

---

## 八、Access Policies 评估

### 8.1 访问策略基础

```yaml
# 【优】基于角色的访问控制
access_policy:
  - group: manager
    member_level:
      includes: "*"  # 所有成员
    row_level:
      filters:
        - member: region
          operator: equals
          values: ["{ securityContext.region }"]

  - group: analyst
    member_level:
      excludes:
        - sensitive_revenue  # 排除敏感度量
```

### 8.2 访问策略评估

| 评估项 | 【优】 | 【劣】 |
|--------|--------|--------|
| **默认拒绝** | 定义特定组的策略后，其他组自动被拒绝 | 未定义策略导致数据暴露 |
| **成员级别** | 使用 `includes`/`excludes` 控制可见成员 | 所有成员都可见 |
| **行级别** | 使用 `row_level.filters` 动态过滤行 | 在 sql 中硬编码 WHERE 破坏缓存 |
| **条件策略** | 使用 `conditions` 定义复杂访问规则 | 策略过于简单或过于复杂 |

### 8.3 Security Context 使用

```yaml
# 【优】使用 securityContext/userAttributes
access_policy:
  - group: "*"
    conditions:
      - if: "{ securityContext.tenant_id }"  # Cube Core
      # 或
      - if: "{ userAttributes.tenant_id }"   # Cube Cloud
    row_level:
      filters:
        - member: tenant_id
          operator: equals
          values: ["{ securityContext.tenant_id }"]

# 【劣】硬编码过滤条件
# sql: "... WHERE tenant_id = 123"  # 破坏缓存和多租户
```

---

## 九、View 定义评估

### 9.1 View 最佳实践

```yaml
# 【优】View 作为数据产品
views:
  - name: sales_performance
    description: "销售业绩分析视图"
    
    cubes:
      - join_path: orders
        includes:
          - count
          - total_revenue
          - created_at
      - join_path: orders.customers
        includes:
          - name
          - segment
        prefix: true  # 添加前缀避免冲突

# 【劣】View 与 Cube 同名或无抽象价值
```

### 9.2 View 评估

| 评估项 | 【优】 | 【劣】 |
|--------|--------|--------|
| **职责** | 面向业务主题的数据产品 | 简单复制 Cube 成员 |
| **命名** | 体现业务主题 | 与底层 Cube 同名 |
| **成员选择** | 精选相关成员 | 包含所有成员 |
| **前缀** | 多 Cube 成员使用 `prefix: true` 避免冲突 | 成员名冲突 |
| **访问策略** | View 可覆盖 Cube 的成员可见性 | 未配置导致暴露不该暴露的成员 |

---

## 十、典型优劣对比示例

### 10.1 【劣质定义示例】

```yaml
# ❌ 多个问题的示例
cube:
  name: order_table  # 问题1: 使用单数和 _table 后缀
  sql: |
    SELECT * FROM orders
    GROUP BY customer_id  # 问题2: Cube sql 中使用 GROUP BY
  
  # 问题3: 缺失 primary_key
  
  dimensions:
    - name: created_at
      sql: created_at
      type: string  # 问题4: 时间应该用 time 类型
    
    - name: status
      sql: status
      type: string
      public: true  # 问题5: 不必要的显式设置

  measures:
    - name: total_amount
      sql: "SUM(amount)"  # 问题6: 应该用 type: sum
      type: number
    
    - name: m1  # 问题7: 无意义的命名
      sql: "COUNT(*)"
      type: number

  joins:
    - name: customers
      relationship: many_to_one
      sql: "{CUBE}.customer_id = {customers.id} AND {customers}.status = 'active'"
      # 问题8: Join 条件包含业务逻辑
```

### 10.2 【优质定义示例】

```yaml
# ✅ 最佳实践示例
cubes:
  - name: orders
    sql_table: public.orders
    public: false  # 通过 View 暴露
    
    description: "订单事实表，包含所有订单交易记录"
    
    refresh_key:
      sql: "SELECT MAX(updated_at) FROM orders"

    dimensions:
      - name: id
        sql: id
        type: number
        primary_key: true
      
      - name: status
        sql: status
        type: string
        description: "订单状态：pending, completed, cancelled"
      
      - name: created_at
        sql: created_at
        type: time
        granularities:
          - name: fiscal_year
            interval: 1 year
            origin: "2025-04-01"

    measures:
      - name: count
        type: count
        description: "订单数量"
      
      - name: total_amount
        sql: amount
        type: sum
        format: currency
        description: "订单总金额"
      
      - name: average_order_value
        sql: "{total_amount} / NULLIF({count}, 0)"
        type: number
        format: currency
        description: "平均订单金额"

    joins:
      - name: customers
        relationship: many_to_one
        sql: "{CUBE}.customer_id = {customers.id}"

    segments:
      - name: completed_orders
        sql: "{CUBE}.status = 'completed'"

    pre_aggregations:
      - name: orders_by_status_daily
        measures:
          - count
          - total_amount
        dimensions:
          - status
        time_dimension: created_at
        granularity: day
        partition_granularity: month
        refresh_key:
          sql: "SELECT MAX(updated_at) FROM orders"

    access_policy:
      - group: analyst
        member_level:
          includes: "*"
        row_level:
          filters:
            - member: region
              operator: equals
              values: ["{ securityContext.region }"]
```

---

## 十一、评估检查清单

### 11.1 必检项（每个 Cube 必须满足）

- [ ] 定义了 `primary_key` 维度
- [ ] `sql_table` 或 `sql` 参数正确配置
- [ ] 时间字段使用 `type: time`
- [ ] 命名遵循规范（snake_case，复数）

### 11.2 建议项（推荐满足）

- [ ] 底层 Cube 设置 `public: false`
- [ ] 所有成员都有 `description`
- [ ] 度量使用内置聚合类型
- [ ] 高频查询配置预聚合
- [ ] 敏感数据配置访问策略

### 11.3 性能检查

- [ ] 预聚合覆盖主要查询路径
- [ ] 大数据量预聚合配置分区
- [ ] 使用 `count_distinct_approx` 替代 `count_distinct`
- [ ] 避免高基数维度进入预聚合

### 11.4 安全检查

- [ ] 敏感字段设置 `public: false`
- [ ] 配置适当的访问策略
- [ ] 使用 `securityContext` 实现行级安全
- [ ] 避免在 sql 中硬编码过滤条件

---

## 十二、参考资源

- [Cube.dev Documentation](https://cube.dev/docs/product/introduction)
- [Cubes Reference](https://cube.dev/docs/product/data-modeling/reference/cube)
- [Measures Reference](https://cube.dev/docs/product/data-modeling/reference/measures)
- [Dimensions Reference](https://cube.dev/docs/product/data-modeling/reference/dimensions)
- [Joins Reference](https://cube.dev/docs/product/data-modeling/reference/joins)
- [Pre-aggregations Reference](https://cube.dev/docs/product/data-modeling/reference/pre-aggregations)
- [Access Policies Reference](https://cube.dev/docs/product/data-modeling/reference/data-access-policies)
- [Style Guide](https://cube.dev/docs/product/data-modeling/recipes/style-guide)
