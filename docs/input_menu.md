# 输入框快捷菜单功能

## 功能概述

在输入框中输入特定字符时，自动显示下拉菜单供快速选择。

| 触发字符 | 功能 | 说明 |
|----------|------|------|
| `/` | 命令/Skill 列表 | 显示所有命令和 Skill，支持过滤 |
| `@` | 文件路径列表 | 显示项目文件路径，支持过滤 |

---

## `/` 命令 - Skill 列表

### 使用方式

1. 输入 `/` → 显示所有命令和 Skill 列表
2. 输入 `/关键字` → 过滤显示包含关键字的命令和 Skill

### 显示格式

```
/memory 管理记忆
/help 帮助
/setting 显示设置
/godot-gdscript-patterns [Master Godot 4 GDScript patterns...]
/godot-shader-fundamentals [Godot 4 Shader 基础语法与数学...]
...
```

### 选中行为

- 命令：插入命令 + 空格，如 `/memory `
- Skill：插入 `/skill_name ` + 空格
- 选中后输入框自动获取焦点，光标定位到末尾

---

## `@` 命令 - 文件路径列表

### 使用方式

1. 输入 `@` → 显示根目录 `res://` 下的文件和文件夹（最多20条）
2. 输入 `@关键字` → 搜索所有层级，返回包含关键字的文件路径

### 显示格式

```
res://project.godot
res://icon.svg
res://scenes/
res://scripts/
...
```

### 选中行为

- 插入文件路径 + 空格，如 `res://project.godot `
- 选中后输入框自动获取焦点，光标定位到末尾

---

## 技术实现

### 关键文件

| 文件 | 路径 |
|------|------|
| `input_container.gd` | `addons/agent/ui/chat/input_container.gd` |

### 核心逻辑

#### 1. 菜单类型枚举

```gdscript
enum MenuListType {
    None,
    Command,
    Skill,
    File
}
```

#### 2. 输入检测 (`on_user_input_text_changed`)

```gdscript
func on_user_input_text_changed():
    var text = user_input.text

    # 检测 @ 触发文件列表
    if "@" in text:
        # 显示文件列表...

    # 检测 / 触发命令或 skill
    elif text.begins_with("/") and check_disallowed_char(text):
        # 显示命令和 skill 列表...
```

#### 3. 选中处理 (`on_input_menu_list_item_selected`)

```gdscript
func on_input_menu_list_item_selected(index: int):
    match menu_list_type:
        MenuListType.Command:
            # 插入命令 + 空格
        MenuListType.Skill:
            # 插入 /skill_name + 空格
        MenuListType.File:
            # 插入文件路径 + 空格
```

### 辅助函数

| 函数 | 作用 |
|------|------|
| `get_filtered_skill_list(prefix)` | 获取过滤后的 Skill 列表 |
| `get_skill_description(skill_name)` | 获取 Skill 的描述信息 |
| `get_filtered_file_list(text)` | 获取过滤后的文件列表 |
| `get_project_file_list(start_path, interation)` | 获取项目文件列表（支持递归深度） |

---

## 配置说明

### 文件搜索深度

`get_project_file_list` 默认 `interation=-1`（无限制），搜索所有层级文件。

### 文件列表最大显示数

`get_filtered_file_list` 限制最多显示 20 条结果。

### 忽略文件模式

```gdscript
var ignore_patterns = [".alpha", ".godot", "*.uid", "addons", "*.import"]
```
