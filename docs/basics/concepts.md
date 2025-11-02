# 核心概念

理解 Claude Code 的核心概念有助于更高效地使用它。

## Tool 工具

Claude Code 通过各种工具与你的代码库交互：

### 文件操作工具
- **Read**: 读取文件内容
- **Write**: 创建新文件
- **Edit**: 编辑已有文件
- **Glob**: 文件模式匹配搜索
- **Grep**: 内容搜索

### 执行工具
- **Bash**: 执行 shell 命令
- **Task**: 启动子代理处理复杂任务

### 最佳实践

1. **优先使用专用工具**：使用 Read 而不是 `cat`，使用 Edit 而不是 `sed`
2. **并行工具调用**：当任务相互独立时，Claude Code 会并行调用多个工具以提升效率
3. **了解工具限制**：某些工具有字符或行数限制

## Todo List 任务管理

Claude Code 使用 TodoWrite 工具来管理和跟踪任务：

### 任务状态
- `pending`: 待执行
- `in_progress`: 执行中（同时只有一个）
- `completed`: 已完成

### 何时使用

- 多步骤复杂任务
- 用户提供多个任务
- 需要跟踪进度的场景

### 示例

```
用户：帮我重构这个组件并添加测试

Claude Code 会创建：
1. 分析当前组件结构
2. 重构组件代码
3. 编写单元测试
4. 运行测试验证
```

## Slash Commands 斜杠命令

自定义命令可以简化重复性工作。

### 创建自定义命令

在 `.claude/commands/` 目录下创建 `.md` 文件：

```markdown
# /review

请审查最近的代码变更，关注：
- 代码质量
- 潜在的 bug
- 性能问题
```

### 使用

```
/review
```

## Hooks 钩子

Hooks 允许在特定事件时自动执行命令。

### 常见 Hook 类型

- `user-prompt-submit-hook`: 用户提交消息前执行
- `tool-call-hook`: 工具调用前执行

配置位置：`~/.claude/config.yaml`

## MCP (Model Context Protocol)

MCP 服务器扩展 Claude Code 的能力：

- 访问外部数据源
- 集成第三方服务
- 自定义工具和资源

配置位置：`~/.claude/mcp_settings.json`

## Context 上下文管理

### CLAUDE.md

- 全局配置：`~/.claude/CLAUDE.md`
- 项目配置：项目根目录的 `CLAUDE.md`

用于提供持久化的指令和偏好设置。

### 最佳实践

1. 在 CLAUDE.md 中记录项目规范
2. 定义代码风格偏好
3. 说明特殊的工作流程要求

## 下一步

了解核心概念后，可以：

- 学习[常用命令](./commands.md)
- 探索[进阶技巧](../advanced/README.md)
- 查看[实战案例](../examples/README.md)
