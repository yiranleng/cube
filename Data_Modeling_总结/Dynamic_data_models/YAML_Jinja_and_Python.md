# YAML, Jinja, and Python - 使用 YAML、Jinja 和 Python 的数据建模

## 文章重点总结

### 概述
- 支持用 **Jinja** 模板和 **Python** 编写**动态**数据模型：去重通用模式、从远程数据源**生成**数据模型。**所有 YAML 数据模型文件**均支持 Jinja。

### YAML
- 推荐默认 YAML：简洁可读。多行字符串（如 `sql`、`description`）推荐用 **literal (|)** 保留换行。

### Jinja
- **循环**：`{% for prop in list %}...{% endfor %}` 可生成重复结构（如 UNNEST、多度量）；`{% for name, days in metrics | items %}` 遍历字典。
- **宏 (macros)**：`{% macro dimension(column_name, type='string', primary_key=False) %}...{% endmacro %}`，再 `{{ dimension('id','number', primary_key=True) }}` 调用，实现可复用片段；也可生成 SQL 片段（如 `cents_to_dollars`）。
- **预览**：在 Data Model 编辑器中可用 … → **Jinja Preview** 查看渲染后的 YAML；Cube Core 暂无此功能。也可通过 Playground、Visual Model、**/v1/meta** 查看结果。
- **转义**：Jinja 默认 **auto-escaping**，Python 来的字符串可能被加引号破坏 YAML。解决：用 `{{ get_unsafe_string() | safe }}`，或在 Python 中返回 **SafeString**（带 `is_safe`）标记为安全。

### Python（Template context）
- 在 **model/globals.py** 中定义**函数**与**变量**，通过 **TemplateContext** 注册后，可在 Jinja 中调用/引用。
- `@template.function('load_data')` 装饰器注册函数；`template.add_variable('answer', answer)` 添加变量。可在 Jinja 中 `{% for cube in load_data()["cubes"] %}` 等，实现从 API 拉取并生成 cubes。
- **导入**：在 `model/globals.py` 或 `cube.py` 中可从当前目录 import（如 `from utils import answer_to_main_question`）。
- **依赖**：在项目根目录 **requirements.txt** 中声明，启动时用 pip 安装；**cube** 已内置。dbt 用户可用 **cube_dbt**；含 native 扩展的依赖需**自定义 Docker 镜像**。
