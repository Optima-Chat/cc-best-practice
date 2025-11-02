# 项目结构优化

合理的项目结构能让 Claude Code 更好地理解你的代码库。

## 为什么项目结构很重要

Claude Code 需要理解：
- 文件之间的关系
- 代码的组织方式
- 项目的架构模式

良好的结构能：
- 提高 Claude Code 的理解准确度
- 减少上下文切换
- 加快文件定位速度

## 推荐的目录结构

### Next.js / React 项目

```
my-app/
├── .claude/                    # Claude Code 配置
│   ├── commands/              # 自定义命令
│   │   ├── review.md
│   │   ├── test.md
│   │   └── deploy.md
│   └── CLAUDE.md              # 项目规范说明
├── app/                       # Next.js App Router
│   ├── (auth)/               # 路由分组
│   ├── api/
│   └── layout.tsx
├── components/                # React 组件
│   ├── ui/                   # 基础 UI 组件
│   ├── features/             # 功能组件
│   └── layouts/              # 布局组件
├── lib/                      # 工具库
│   ├── api/                  # API 客户端
│   ├── utils/                # 工具函数
│   └── hooks/                # 自定义 Hooks
├── store/                    # 状态管理
│   ├── slices/
│   └── index.ts
├── types/                    # TypeScript 类型
│   ├── models/
│   └── api/
├── styles/                   # 样式文件
├── public/                   # 静态资源
├── __tests__/               # 测试文件
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── .env.example             # 环境变量示例
├── .gitignore
├── package.json
├── tsconfig.json
└── README.md                # 项目说明
```

### Node.js 后端项目

```
api-server/
├── .claude/
│   ├── commands/
│   └── CLAUDE.md
├── src/
│   ├── config/              # 配置文件
│   ├── controllers/         # 控制器
│   ├── middleware/          # 中间件
│   ├── models/              # 数据模型
│   ├── routes/              # 路由定义
│   ├── services/            # 业务逻辑
│   ├── utils/               # 工具函数
│   ├── types/               # 类型定义
│   └── index.ts             # 入口文件
├── tests/
│   ├── unit/
│   ├── integration/
│   └── fixtures/
├── scripts/                 # 脚本文件
│   ├── seed.ts             # 数据库种子
│   └── migrate.ts          # 迁移脚本
├── docs/                    # 文档
│   ├── api/                # API 文档
│   └── architecture.md     # 架构说明
├── .env.example
├── docker-compose.yml
├── Dockerfile
├── package.json
└── tsconfig.json
```

### Python 项目

```
my-python-project/
├── .claude/
│   ├── commands/
│   └── CLAUDE.md
├── src/
│   └── my_package/
│       ├── __init__.py
│       ├── core/
│       ├── models/
│       ├── services/
│       └── utils/
├── tests/
│   ├── unit/
│   ├── integration/
│   └── conftest.py
├── docs/
├── scripts/
├── .env.example
├── .gitignore
├── requirements.txt
├── setup.py
├── pyproject.toml
└── README.md
```

## CLAUDE.md 配置

在项目根目录创建 `.claude/CLAUDE.md`：

```markdown
# 项目规范

## 项目概述
这是一个电商平台的前端项目，使用 Next.js 14 和 TypeScript。

## 技术栈
- Next.js 14 (App Router)
- TypeScript (严格模式)
- Tailwind CSS
- Zustand (状态管理)
- React Query (数据获取)

## 代码规范

### 命名约定
- 组件文件使用 PascalCase: `UserProfile.tsx`
- 工具函数使用 camelCase: `formatCurrency.ts`
- 常量使用 UPPER_SNAKE_CASE: `API_BASE_URL`

### 代码风格
- 优先使用函数式组件
- 使用 const 而非 let
- 避免使用 any 类型
- 导入顺序: React → 第三方库 → 本地模块

### 组件结构
```typescript
// 1. 类型定义
interface Props {}

// 2. 组件实现
export function Component({ }: Props) {
  // 2.1 Hooks
  // 2.2 状态
  // 2.3 副作用
  // 2.4 事件处理
  // 2.5 渲染
}
```

## 文件组织

### 组件分类
- `components/ui/`: 纯 UI 组件 (Button, Input)
- `components/features/`: 业务组件 (ProductCard)
- `components/layouts/`: 布局组件 (Header, Footer)

### API 路由
- RESTful 风格
- 使用 TypeScript 类型定义请求/响应

## Git 规范

### 提交信息格式
```
<type>(<scope>): <subject>

<body>
```

类型 (type):
- feat: 新功能
- fix: Bug 修复
- refactor: 重构
- docs: 文档更新
- test: 测试相关
- chore: 构建/工具变更

### 分支策略
- main: 生产环境
- develop: 开发环境
- feature/*: 功能分支
- hotfix/*: 紧急修复

## 测试要求

### 测试覆盖
- 工具函数: 100%
- 组件: 80%+
- API 路由: 100%

### 测试类型
- 单元测试: Jest + Testing Library
- E2E 测试: Playwright

## 性能要求

### 打包体积
- 首屏 JS < 200KB
- 单个页面 < 100KB

### 性能指标
- Lighthouse 性能分数 > 90
- FCP < 1.5s
- LCP < 2.5s

## 部署流程

1. 本地测试通过
2. 创建 PR
3. Code Review
4. CI 通过
5. 合并到 develop
6. 自动部署到测试环境
7. QA 验证
8. 部署到生产

## 常用命令

- 开发: `npm run dev`
- 构建: `npm run build`
- 测试: `npm test`
- Lint: `npm run lint`
- 类型检查: `npm run type-check`

## 注意事项

- 敏感信息使用环境变量
- API 密钥不要提交到代码库
- 图片优化后再使用
- 定期更新依赖
```

## 命名规范

### 文件命名

**组件文件**
```
✅ UserProfile.tsx
✅ ProductCard.tsx
❌ userProfile.tsx
❌ product-card.tsx
```

**工具文件**
```
✅ formatCurrency.ts
✅ validateEmail.ts
❌ FormatCurrency.ts
❌ validate_email.ts
```

**测试文件**
```
✅ UserProfile.test.tsx
✅ formatCurrency.test.ts
❌ UserProfile.spec.tsx
```

### 目录命名

```
✅ components/
✅ user-profile/
❌ Components/
❌ userProfile/
```

## 代码组织原则

### 1. 单一职责

每个文件只做一件事：

```typescript
// ✅ 好 - 单一职责
// utils/formatCurrency.ts
export function formatCurrency(amount: number): string {
  return new Intl.NumberFormat('zh-CN', {
    style: 'currency',
    currency: 'CNY',
  }).format(amount);
}

// ❌ 不好 - 混杂多个功能
// utils/helpers.ts
export function formatCurrency() {}
export function validateEmail() {}
export function parseDate() {}
export function makeApiCall() {}
```

### 2. 就近原则

相关的文件放在一起：

```
components/
└── UserProfile/
    ├── UserProfile.tsx       # 组件
    ├── UserProfile.test.tsx  # 测试
    ├── UserProfile.module.css # 样式
    ├── useUserProfile.ts     # 专用 Hook
    └── types.ts              # 类型定义
```

### 3. 按功能分组

而不是按类型：

```
✅ 好 - 按功能
features/
├── auth/
│   ├── LoginForm.tsx
│   ├── SignupForm.tsx
│   ├── authApi.ts
│   └── authStore.ts
└── products/
    ├── ProductList.tsx
    ├── ProductCard.tsx
    ├── productsApi.ts
    └── productsStore.ts

❌ 不好 - 按类型
src/
├── components/
│   ├── LoginForm.tsx
│   └── ProductList.tsx
├── apis/
│   ├── authApi.ts
│   └── productsApi.ts
└── stores/
    ├── authStore.ts
    └── productsStore.ts
```

## README 最佳实践

项目 README 应该包含：

```markdown
# 项目名称

简短的项目描述

## 功能特性

- 功能 1
- 功能 2

## 技术栈

- Next.js 14
- TypeScript
- Tailwind CSS

## 快速开始

\`\`\`bash
# 安装依赖
npm install

# 配置环境变量
cp .env.example .env.local

# 启动开发服务器
npm run dev
\`\`\`

## 项目结构

\`\`\`
app/          # Next.js 应用
components/   # React 组件
lib/          # 工具库
\`\`\`

## 开发指南

### 代码规范

请查看 `.claude/CLAUDE.md` 了解详细的代码规范。

### 提交规范

使用 Conventional Commits 格式。

### 测试

\`\`\`bash
npm test
\`\`\`

## 部署

\`\`\`bash
npm run build
\`\`\`

## 贡献

请阅读 [CONTRIBUTING.md](./CONTRIBUTING.md)

## 许可证

MIT
```

## 环境变量管理

### .env.example

```bash
# 数据库
DATABASE_URL=postgresql://user:password@localhost:5432/db

# API
NEXT_PUBLIC_API_URL=https://api.example.com
API_SECRET_KEY=your-secret-key

# 第三方服务
STRIPE_SECRET_KEY=sk_test_xxx
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_test_xxx

# 功能开关
NEXT_PUBLIC_FEATURE_NEW_UI=false
```

### 使用说明

在 README 中说明：

```markdown
## 环境变量配置

1. 复制示例文件:
   \`\`\`bash
   cp .env.example .env.local
   \`\`\`

2. 填写实际的值

3. 重启开发服务器
```

## 文档组织

```
docs/
├── README.md              # 文档索引
├── getting-started.md     # 入门指南
├── architecture.md        # 架构设计
├── api/                   # API 文档
│   ├── auth.md
│   └── products.md
├── guides/                # 开发指南
│   ├── components.md
│   └── state-management.md
└── deployment.md          # 部署文档
```

## 与 Claude Code 配合

### 清晰的文件路径

使用 `文件路径:行号` 引用代码时，结构清晰的项目更容易定位：

```
"修改 components/features/ProductCard/ProductCard.tsx:45 的价格显示逻辑"
```

### 便于搜索

良好的命名让搜索更高效：

```
"在 services/ 目录下找到所有 API 调用"
"查找 utils/ 中的日期格式化函数"
```

### 上下文理解

清晰的结构帮助 Claude Code 理解代码关系：

```
"参考 ProductCard.tsx 的实现，创建一个 UserCard 组件"
```

## 最佳实践检查清单

- [ ] 项目有清晰的目录结构
- [ ] 创建了 `.claude/CLAUDE.md`
- [ ] README 包含完整的项目信息
- [ ] 文件命名遵循一致的规范
- [ ] 代码按功能而非类型组织
- [ ] 有 `.env.example` 示例文件
- [ ] 文档结构清晰完整
- [ ] Git 提交信息规范

## 下一步

- 学习[性能优化技巧](./performance.md)
- 了解[协作开发](./collaboration.md)最佳实践
- 查看[实战案例](../examples/README.md)
