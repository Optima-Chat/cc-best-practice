# 进阶技巧

本节介绍 Claude Code 的进阶使用技巧，帮助你充分发挥工具的潜力。

## 目录

- [Slash 命令](./slash-commands.md) - 创建和使用自定义命令
- [自定义 Hooks](./hooks.md) - 配置事件钩子
- [MCP 服务器配置](./mcp-servers.md) - 集成 MCP 服务器
- [提示词工程](./prompt-engineering.md) - 优化与 Claude Code 的交互

## 核心技巧速览

### 1. 并行工具调用

当任务相互独立时，一次性发起多个请求：

```
好的示例：
- "读取 package.json 和 tsconfig.json"
  → Claude Code 会并行读取两个文件

避免的做法：
- 不要逐个文件顺序请求（低效）
```

### 2. 精确的文件引用

使用 `文件路径:行号` 格式引用代码：

```
src/utils/helper.ts:42
```

### 3. 任务分解

让 Claude Code 为复杂任务创建 Todo List：

```
"帮我实现用户认证功能，包括登录、注册和密码重置"

Claude Code 会自动创建任务列表并逐步执行。
```

### 4. 上下文优化

- 使用 `.claude/CLAUDE.md` 存储项目偏好
- 创建自定义 slash 命令简化常用操作
- 配置 MCP 服务器扩展能力

## 效率提升建议

### 明确的指令

❌ 不好的：「优化一下代码」
✅ 好的：「重构 UserService.ts 中的认证逻辑，使用依赖注入模式」

### 利用 Git 工作流

```
"创建一个新分支，实现 XX 功能，然后创建 PR"
```

Claude Code 会：
1. 创建分支
2. 实现功能
3. 提交代码
4. 推送并创建 PR

### 使用 Agent 处理复杂搜索

对于需要多轮搜索的任务，使用 Task 工具启动专门的 agent：

```
"在整个代码库中找到所有使用旧 API 的地方并更新"
```

## 下一步

- 深入学习[自定义命令](./slash-commands.md)
- 配置[MCP 服务器](./mcp-servers.md)扩展能力
- 查看[实战案例](../examples/README.md)了解真实应用
