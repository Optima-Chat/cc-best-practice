# 最佳实践

Claude Code 使用的最佳实践和建议。

## 目录

- [项目结构优化](./project-structure.md) - 如何组织项目以便 Claude Code 更好理解
- [性能优化技巧](./performance.md) - 提升使用效率
- [协作开发](./collaboration.md) - 团队协作最佳实践
- [安全实践](./security.md) - 保护代码和数据安全

## 核心原则

### 1. 明确沟通

- 使用清晰、具体的指令
- 提供充分的上下文
- 设定明确的成功标准

详见: [提示词工程](../advanced/prompt-engineering.md)

### 2. 结构化项目

- 合理的目录结构
- 清晰的命名规范
- 完善的文档

详见: [项目结构优化](./project-structure.md)

### 3. 利用工具

- 自定义 Slash Commands
- 配置合适的 Hooks
- 集成 MCP 服务器

详见: [进阶技巧](../advanced/README.md)

### 4. 迭代改进

- 从简单任务开始
- 逐步优化流程
- 积累团队知识库

## 常见模式

### 开发工作流

```
1. 创建功能分支
   "创建新分支 feature/user-profile"

2. 实现功能
   "实现用户资料页面，包括查看和编辑功能"

3. 测试
   "运行测试，修复所有失败的用例"

4. 代码审查
   "/review"

5. 创建 PR
   "创建 PR 到 main 分支"
```

### 调试流程

```
1. 复现问题
   "用户报告登录失败，错误信息：..."

2. 定位问题
   "检查 auth/login.ts 和相关日志"

3. 分析原因
   "分析为什么 token 验证失败"

4. 修复问题
   "修复 token 过期时间计算错误"

5. 验证
   "运行测试确保问题解决"
```

### 重构模式

```
1. 识别问题代码
   "这个文件太长了，需要重构"

2. 制定计划
   "提出重构方案：拆分为 3 个模块"

3. 执行重构
   "按方案逐步重构"

4. 确保测试通过
   "运行所有测试"

5. 更新文档
   "更新相关文档"
```

## 效率提升

### 并行工作

让 Claude Code 并行处理独立任务：

```
同时：
- 读取 package.json 和 tsconfig.json
- 分析两个文件的配置问题
- 提出改进建议
```

### 批量操作

```
在以下目录查找并修复所有 ESLint 错误：
- src/components/
- src/services/
- src/utils/
```

### 模板复用

创建 Slash Commands 复用常见操作：

```
/new-component - 创建新组件（包含测试和样式）
/api-endpoint - 创建新的 API 端点
/db-migration - 创建数据库迁移
```

## 团队协作

### 共享配置

将 Claude Code 配置提交到版本控制：

```
.claude/
  commands/          # 团队共享命令
  CLAUDE.md          # 项目规范
```

### 统一规范

在 CLAUDE.md 中定义：

```markdown
# 团队规范

## 代码风格
- TypeScript 严格模式
- 函数式组件优先
- 使用 CSS Modules

## Git 提交规范
- feat: 新功能
- fix: Bug 修复
- refactor: 重构
- docs: 文档更新
```

### 知识传承

记录最佳实践：

```markdown
# 常见任务

## 添加新的 API 端点
使用命令: `/api-endpoint`

## 数据库迁移
使用命令: `/db-migration`

## 部署检查
使用命令: `/deploy-check`
```

## 注意事项

### ⚠️ 安全

- 不要让 Claude Code 处理敏感信息
- 审查生成的代码
- 注意 API 密钥和凭证

详见: [安全实践](./security.md)

### ⚠️ 版本控制

- 及时提交代码
- 使用有意义的提交信息
- 创建功能分支

详见: [协作开发](./collaboration.md)

### ⚠️ 测试

- 重要变更必须有测试
- 运行完整测试套件
- 检查测试覆盖率

## 持续改进

### 收集反馈

记录使用过程中的：
- 高效的交互模式
- 常见的问题
- 改进建议

### 优化流程

定期审查：
- 哪些 Slash Commands 最常用
- 哪些任务可以自动化
- 哪些文档需要更新

### 分享经验

- 在团队内部分享技巧
- 贡献到开源项目
- 参与社区讨论

## 下一步

- 深入学习各个最佳实践主题
- 查看[实战案例](../examples/README.md)
- 参考[进阶技巧](../advanced/README.md)
