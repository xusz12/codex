# 使用技能扩展 Claude - Claude Code Docs

技能扩展了 Claude 能做的事情。创建一个包含说明的 `SKILL.md` 文件，Claude 就会将其添加到其工具包中。Claude 在相关时使用技能，或者你可以使用 `/skill-name` 直接调用一个技能。

## 技能存放位置

| 位置 | 路径 | 适用于 |
|------|------|--------|
| 企业 | 参阅托管设置 | 你的组织中的所有用户 |
| 个人 | `~/.claude/skills/<skill-name>/SKILL.md` | 你的所有项目 |
| 项目 | `.claude/skills/<skill-name>/SKILL.md` | 仅此项目 |
| 插件 | `<plugin>/skills/<skill-name>/SKILL.md` | 启用插件的位置 |

## 技能文件结构

```
my-skill/
├── SKILL.md           # 主要说明（必需）
├── template.md        # Claude 要填写的模板
├── examples/
│   └── sample.md      # 显示预期格式的示例输出
└── scripts/
    └── validate.sh    # Claude 可以执行的脚本
```

## SKILL.md 前置元数据参考

```yaml
---
name: my-skill
description: What this skill does
disable-model-invocation: true
allowed-tools: Read, Grep
context: fork
agent: Explore
---
```

### 字段说明

| 字段 | 描述 |
|------|------|
| `name` | 技能的显示名称 |
| `description` | 技能的作用以及何时使用它 |
| `disable-model-invocation` | 设置为 true 以防止 Claude 自动加载 |
| `user-invocable` | 设置为 false 以从 / 菜单中隐藏 |
| `allowed-tools` | 此技能处于活动状态时 Claude 可以使用的工具 |
| `context` | 设置为 `fork` 以在分叉的子代理上下文中运行 |
| `agent` | 设置 `context: fork` 时要使用的子代理类型 |

## 字符串替换

| 变量 | 描述 |
|------|------|
| `$ARGUMENTS` | 调用技能时传递的所有参数 |
| `${CLAUDE_SESSION_ID}` | 当前会话 ID |

## 控制谁调用技能

| 前置元数据 | 你可以调用 | Claude 可以调用 |
|-----------|-----------|----------------|
| （默认） | 是 | 是 |
| `disable-model-invocation: true` | 是 | 否 |
| `user-invocable: false` | 否 | 是 |

## 高级模式

### 注入动态上下文

使用 ``!`command`" 语法在技能内容发送给 Claude 之前运行 shell 命令：

```yaml
---
name: pr-summary
description: Summarize changes in a pull request
context: fork
agent: Explore
---
## Pull request context
- PR diff: !`gh pr diff`
```

### 在子代理中运行技能

当你想让技能在隔离中运行时，在前置元数据中添加 `context: fork`：

```yaml
---
name: deep-research
description: Research a topic thoroughly
context: fork
agent: Explore
---
Research $ARGUMENTS thoroughly:
1. Find relevant files using Glob and Grep
2. Read and analyze the code
3. Summarize findings with specific file references
```

## 共享技能

- **项目技能**：将 `.claude/skills/` 提交到版本控制
- **插件**：在你的插件中创建 `skills/` 目录
- **托管**：通过托管设置部署组织范围内
