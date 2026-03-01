# 选对工程基础设施

> 第 2 点展开 | AI Native 开发最佳实践

---

## 为什么基础设施的选择这么重要

很多团队开始用 AI 写代码，第一反应是"让 Claude Code 帮我写 Python 吧""每个微服务一个 repo 挺好的""CLAUDE.md 是什么？先不管，以后再说"。

这些选择，单独看每一个都不算错。但组合起来，它们会决定你用 Claude Code 的天花板在哪里。

我们 3 个人、30 个仓库、120 万行代码的效率，不只是因为"会用 Claude Code"，更是因为在工程基础设施上做对了三个选择：**TypeScript、Monorepo、CLAUDE.md**。

这三个选择是相互叠加的。TypeScript 给了类型安全，Monorepo 给了上下文共享，CLAUDE.md 给了项目知识。三者一起，Claude Code 的产出质量和开发效率会呈指数级提升。

单独用其中一个，有改善。三个一起用，质变。

---

## TypeScript：让 AI 写的代码有静态检查

### 为什么选 TypeScript 而不是 Python

先说结论：我们 30 个仓库，全部用 TypeScript。前端、后端、CLI、SDK、Lambda、基础设施脚本，无一例外。

很多人会问：Python 不行吗？Python 也是主流语言，Claude Code 也很擅长啊。

Python 当然行。但在 AI Native 开发的场景下，TypeScript 有几个关键优势：

**第一，训练数据量最大。** TypeScript 是 GitHub 上代码量最多的语言之一。模型训练集中 TypeScript 代码的占比非常高，这意味着 Claude Code 对 TypeScript 的理解最深、生成的代码质量最高、出错率最低。这不是理论推测，是我们 6 个月实际体验的结论。同样的需求，用 TypeScript 描述给 Claude Code，一次性通过率明显高于其他语言。

**第二，静态类型检查是刚需。** 这一点怎么强调都不过分。

用 Claude Code 写代码，最大的挑战是什么？是你不知道它写的对不对。

5000 行代码 30 分钟就生成了，但里面有没有 bug？类型有没有搞错？接口有没有对齐？如果是 Python 这样的动态类型语言，你只能通过运行时才能发现问题。而 TypeScript 的静态类型检查，能在编译阶段就捕获大量错误。

举个例子：Claude Code 写了一个函数，接收的参数类型是 `string`，但调用方传了一个 `number`。Python 不会报错，直到运行到那一行才可能出问题，甚至某些场景下不会出问题但产生隐蔽的逻辑错误。TypeScript 编译器直接告诉你：类型不匹配，请修复。

**类型检查是 AI 代码质量的第一道防线。** 配合测试，就是两道防线。这两道防线能帮你拦截绝大部分问题。

**第三，前后端同一种语言。** 我们的系统前端用 React（TypeScript），后端用 Node.js（TypeScript），CLI 用 TypeScript，SDK 用 TypeScript。一种语言打通所有层。Claude Code 在一个 Monorepo 里看到的是同一种语言，类型定义可以共享，接口定义可以复用。前端定义了一个 `Product` 类型，后端直接 import 来用，保证前后端数据结构完全一致。

如果前端用 TypeScript、后端用 Python，光是维护两边的类型定义就够你头疼的了。Claude Code 也很难跨语言保持一致性。

**第四，生态丰富。** NPM 是世界上最大的包管理器，几乎什么功能都有现成的库。Claude Code 对这些库的使用方式非常熟悉，因为训练数据中有大量的使用示例。

### 一个现实考量

我知道很多团队现有的技术栈是 Python、Java、Go。我不是说这些语言不能用 Claude Code，它们当然能用。

但如果你要 **新起一个项目**，并且打算深度使用 AI Native 开发方式，我强烈建议用 TypeScript。它和 Claude Code 的配合度是最高的。

如果你的团队是 Python 背景，也不用太焦虑。Python 加上 Type Hints 和 mypy 做静态检查，也能获得部分好处。关键是 **静态类型检查** 这个理念，无论用什么语言都适用。

---

## Monorepo：让 Claude Code 看到完整的上下文

### 为什么 Monorepo 对 AI 开发如此重要

Monorepo 的好处，传统开发中已经有很多讨论了。但在 AI Native 开发中，它的重要性被放大了 10 倍。

原因很简单：**Claude Code 只能看到当前仓库里的代码。**

这句话的含义是什么？如果你把一个系统拆成 5 个 repo——前端一个、后端一个、CLI 一个、SDK 一个、基础设施一个——那 Claude Code 在写前端代码的时候，看不到后端的 API 定义。在写 CLI 的时候，看不到 SDK 的接口。在写 SDK 的时候，看不到后端的数据模型。

结果就是，它写的代码经常和其他模块对不上。接口不一致、类型不匹配、约定不统一。你要花大量时间手动对齐，或者把 A repo 的信息复制粘贴到 B repo 的上下文里，非常低效。

但如果把这 5 个模块放到一个 Monorepo 里，Claude Code 自然而然就能看到所有相关代码。它在写前端组件的时候，可以直接去看后端的 API handler，确保请求格式和响应格式完全一致。在写 CLI 的时候，可以直接引用 SDK 的类型定义。在写 SDK 的时候，可以直接参考后端的数据模型。

**上下文共享，是 Monorepo 在 AI Native 开发中最大的价值。**

### 什么应该放到一个 Repo 里

我的原则是：**功能上紧密相关的模块，放到一个 repo。**

比如一个 BI 系统：
- BI 前端（React Web UI）
- BI 后端（API Server）
- BI 数据处理（ETL Pipeline）
- BI CLI 工具（命令行管理工具）
- BI SDK（给其他系统调用的 SDK）

这些都应该放在一个 repo 里。因为它们共享数据模型、共享 API 定义、共享业务概念。拆开了，每个 repo 都要维护自己的一份定义，不一致几乎是必然的。

那什么不应该放到一起？完全独立的业务系统。比如 BI 系统和用户管理系统，业务上没有强关联，可以分成两个 repo。

### 案例：optima-ai-shell

我们的 AI Shell 系统（[optima-ai-shell](https://github.com/Optima-Chat/optima-ai-shell)）是一个典型的 Monorepo。一个仓库里包含 5 个 TypeScript 包：

1. **ai-cli** —— 命令行客户端，用户直接使用的入口。处理用户输入、调用后端 API、展示结果。
2. **session-gateway** —— 会话网关，管理 AI 对话的会话状态、消息路由、上下文管理。
3. **web-ui** —— Web 界面，提供浏览器端的交互界面，和 ai-cli 共享同一个后端。
4. **lambda-handler** —— AWS Lambda 函数，处理异步任务、Webhook、定时触发。
5. **agentcore-adapter** —— 适配器层，对接 AWS Bedrock AgentCore，封装模型调用。

这 5 个包之间有密切的依赖关系。ai-cli 调用 session-gateway 的 API，session-gateway 调用 agentcore-adapter 的接口，lambda-handler 和 session-gateway 共享消息格式定义，web-ui 和 ai-cli 共享同一套 API 客户端代码。

如果拆成 5 个 repo，Claude Code 在改 session-gateway 的 API 时，根本不知道 ai-cli 和 web-ui 是怎么调用它的。改了接口不改调用方，一定会出问题。

放到一个 Monorepo 里，Claude Code 可以一次性改完所有相关代码。改了 API 定义，同时更新所有调用方。这种跨模块的一致性维护，在多 repo 模式下几乎不可能自动完成。

### Monorepo 的工程实践

用 Monorepo 不意味着代码堆在一起不分层。我们用 TypeScript 的 workspace 功能来管理多包结构：

- 每个包有自己的 `package.json`、`tsconfig.json`
- 包之间通过 workspace 引用，不发布到 NPM
- 根目录有统一的 lint、test、build 配置
- 每个包可以独立构建、独立部署

这种结构既保持了代码的组织性，又让 Claude Code 能看到完整的上下文。

---

## CLAUDE.md：项目的知识库

### 什么是 CLAUDE.md

CLAUDE.md 是一个放在项目根目录的文件。Claude Code 每次启动的时候都会读取它。

你可以理解为：**这是你给 Claude Code 的项目说明书。** 它决定了 Claude Code 对你这个项目的理解深度。

没有 CLAUDE.md，Claude Code 只能靠读代码来理解你的项目。代码能告诉它"是什么"，但很难告诉它"为什么"——为什么选了这个架构、为什么用了这个命名约定、为什么这个地方要绕一下。

有了 CLAUDE.md，你把这些"为什么"都写下来了。Claude Code 每次启动都能获得这些上下文，写出的代码就更符合你项目的风格和约定。

### 里面写什么

一份好的 CLAUDE.md 通常包含这些内容：

**1. 项目概览**
- 这个项目是做什么的，一两句话说清楚
- 核心的技术栈是什么
- 目录结构是怎样的

**2. 架构设计**
- 系统的整体架构，关键组件和它们的关系
- 数据流是怎么走的
- 关键的设计决策和背后的原因

**3. 开发约定**
- 命名规范（文件名、变量名、函数名怎么命名）
- 代码风格（用不用分号、缩进是 2 空格还是 4 空格）
- Git 工作流（分支怎么命名、commit message 格式）
- API 设计规范（RESTful 约定、错误码规范）

**4. 常见陷阱和经验教训**
- 生产环境踩过的坑，怎么避免
- 已知的技术债务，暂时为什么不修
- 某个模块为什么设计得"不太优雅"，背后的权衡是什么

**5. 开发和部署流程**
- 怎么启动本地开发环境
- 怎么运行测试
- 怎么部署到各个环境
- 环境变量怎么配置

**6. 重要的业务逻辑**
- 核心业务流程的描述
- 关键的状态机和状态转换
- 特殊的业务规则和边界条件

### 我们的实际案例

我们 30 个仓库几乎每个都有 CLAUDE.md。来看几个真实例子：

**[commerce-cli](https://github.com/Optima-Chat/commerce-cli)（660 行）：** 这是我们电商 CLI 工具的 CLAUDE.md，覆盖了 16 个命令模块的完整说明、输出格式约定（结构化 vs --pretty）、错误处理规范、国际化策略、测试要求。660 行，内容非常详尽。

**[commerce-backend](https://github.com/Optima-Chat/commerce-backend)（521 行）：** 电商后台的 CLAUDE.md，覆盖数据库模型、API 路由表、中间件链、认证授权流程、第三方服务集成（Stripe、ShipStation、Cloudflare R2）。

**optima-ai-shell（324 行，[查看原文](https://github.com/Optima-Chat/optima-ai-shell/blob/main/CLAUDE.md)）：** AI Shell 的 CLAUDE.md，覆盖 5 个包的架构关系、消息协议格式、认证流程（Cognito → JWT → API Gateway）、WebSocket 通信协议、部署流程。

最短的 CLAUDE.md 是 148 行，最长的是 1062 行。**行数不是目标，信息密度才是。** 但通常来说，一个有一定复杂度的项目，CLAUDE.md 低于 200 行可能信息量不够。

### 核心原则：隐性知识显性化

CLAUDE.md 的本质是什么？是把 **隐性知识显性化**。

每个项目都有大量的隐性知识——那些没写在文档里、只存在于核心开发者脑子里的东西：

- "这个 API 之所以设计成这样，是因为历史原因，当时 Stripe 的 webhook 格式有个 bug"
- "这个模块不要轻易改，它的逻辑很绕但是已经在生产上稳定运行了 6 个月"
- "数据库的 `status` 字段虽然定义了 5 个值，但 `PENDING_REVIEW` 这个状态目前没在用，是为将来预留的"
- "测试环境的数据库和生产环境的 schema 有微小差异，注意 `created_at` 字段的时区"

这些知识，如果不写下来：
- Claude Code 不知道，写出来的代码可能踩坑
- 新人加入团队也不知道，要靠老人口口相传
- 老人离开了，知识就丢了

写到 CLAUDE.md 里，三个问题同时解决。

### CLAUDE.md 也是新人的入职文档

这是一个意外的好处。

我们发现，CLAUDE.md 写好了之后，不只是 Claude Code 受益，人类新成员加入团队的时候也受益巨大。

传统的团队，新人上手通常需要老人带几周。看代码、问问题、理解系统、踩坑、再问、再理解。这个过程很长，也很消耗老人的时间。

有了 CLAUDE.md，新人看完这份文档，对项目的整体架构、核心模块、开发约定、常见陷阱就有了一个全面的了解。再配合充分的代码注释和测试用例，上手速度大大加快。

更重要的是，新人可以用 Claude Code 来帮他理解不懂的部分。"帮我解释一下这个模块的认证流程""这个中间件做了什么""这个测试用例在验证什么场景"。Claude Code 结合 CLAUDE.md 的上下文和代码本身，能给出非常准确的解释。

**一份好的 CLAUDE.md，既是 AI 的知识库，也是人的入职手册。** 一次投入，双重回报。

### CLAUDE.md 的维护

CLAUDE.md 不是写一次就完事的。它需要跟着项目一起演进。

我的做法是：每次做了重要的架构变更、加了新模块、踩了新坑、改了开发约定，都让 Claude Code 更新 CLAUDE.md。成本很低，就是一句"把我们刚才做的改动更新到 CLAUDE.md"。

不要追求完美。先有一份 200 行的版本，比没有强 100 倍。随着项目推进，慢慢补充、优化就好。

---

## 三个选择的叠加效应

最后说一下为什么这三个选择要放在一起讲，因为它们的效果是叠加的。

单独用 TypeScript——有类型检查，能拦截一部分错误。好。

加上 Monorepo——类型定义跨模块共享，Claude Code 能看到完整上下文，一致性大大提升。更好。

再加上 CLAUDE.md——Claude Code 不仅能看到代码，还能理解架构意图、开发约定、历史决策。它写出来的代码不只是语法正确、类型正确，还符合你项目的风格和设计理念。最好。

这就是为什么这三个选择是"工程基础设施"而不是"工程技巧"。基础设施是地基。地基打好了，上面盖什么都稳。地基没打好，上面再怎么努力也事倍功半。

---

## 给想要开始的团队的建议

**1. 新项目直接上 TypeScript + Monorepo + CLAUDE.md。** 没有历史包袱，一步到位。

**2. 老项目逐步迁移。** 不需要把整个系统重写。可以先从新模块开始用 TypeScript，先把 CLAUDE.md 写起来。Monorepo 的迁移成本相对高一些，可以在下次项目重构或新系统时采用。

**3. CLAUDE.md 最容易开始。** 不需要改任何代码，今天就可以写。花 1-2 个小时，让 Claude Code 帮你写一份当前项目的 CLAUDE.md。从项目概览、架构设计、开发约定开始，后续慢慢补充。

**4. 观察效果。** 有了 CLAUDE.md 之后，你会明显感觉 Claude Code 的产出质量提升了。它不再问你"这个项目用什么框架"这种基础问题，而是直接按照你的约定写代码。这种体验提升，几个小时就能感受到。

三个选择，投入都不大，但回报是持续的、累积的。越早做，收益越大。
