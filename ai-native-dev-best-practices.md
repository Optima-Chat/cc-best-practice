# AI Native 开发最佳实践

> 徐昊 | Optima AI 创始人
> 3 人团队，10 个月，30 个仓库，116 万行代码

---

## 开场

Optima AI 做跨境电商 AI 平台，核心团队 3 个人，10 个月写了 116 万行代码，30 个仓库，8000 多次 commit。全程用 Claude Code 开发。

今天分享的不是理论，是我们踩了 10 个月坑之后总结出来的实战经验。分两部分：个人怎么用好 Claude Code，以及团队怎么围绕它协作。

---

# 第一部分：个人开发

## 1. 先学习、再做方案、再写代码

不要直接让 Claude Code 写代码。正确的流程是：**学习（1-2h）→ 技术方案 → 审方案 → 写代码**。

比如要做一个 BI 系统，先花 1-2 个小时，从 Claude Code 那里了解行业最佳实践。先上一课，把所有有疑问的地方都问一遍，一直到完全搞明白了，再让它写技术方案。这样你就有充分的知识去判断技术方案是否正确。这个方法可以让你在半天之内，完成一个你根本不会的领域的系统开发，并达到行业最佳实践的水平。

技术方案要做透。在让 Claude Code 动手之前，尽可能确认所有技术细节，做到在我看来没有任何问题。不要很多细节没讨论清楚就让它先实现，然后边测试边改进——这是人类写代码的习惯，人类在写的过程中会思考和发现问题。但 Claude Code 不同，它缺少"边写边思考"的能力，必须把思考前置到方案阶段。

把技术方案做好后，写代码很快——5000 行代码不过 30 分钟的事情。

我花了 1 天时间学了 AWS，纯粹靠跟 Claude Code 之间的文字问答。3 天产出 41 篇文档、32,000 多行内容，覆盖 35 个 AWS 核心知识点。先学习再动手，学的过程本身也产出了可复用的知识库。

> **案例：** 我们的电商后台 commerce-backend 在开发前，先让 Claude Code 写了一份 1645 行的架构文档（[docs/ARCHITECTURE.md](https://github.com/Optima-Chat/commerce-backend/blob/main/docs/ARCHITECTURE.md)），覆盖系统概览、23 个核心模块、数据模型、API 设计、部署架构。git 历史可以清楚看到：docs 提交在前，代码实现在后。方案做透了，后面写代码就是执行。

---

## 2. 选对工程基础设施

三个关键选择：

**全部用 TypeScript。** TypeScript 是 GitHub 代码量最多的语言，在模型训练集中占比最大，Claude Code 最擅长。更重要的是静态类型检查——用 Claude Code 写代码，最大的问题是你不知道它写的对不对。静态类型检查能排除很多错误，节约很多时间。

**尽量用 Monorepo。** 一个系统功能，比如 BI，把前端、后台、命令行工具、数据上报 SDK 都放到一个 repo 里。原因是共享 Context——Claude Code 在同一个 repo 内可以看到所有相关代码，开发效率最高。拆成多个 repo 后，它无法跨仓库理解上下文，协作成本急剧上升。

**写好 CLAUDE.md。** 每个仓库都应该有一份 CLAUDE.md，它是项目的知识库——架构设计、API 规范、开发约定、生产环境的经验教训，全写在里面。Claude Code 每次启动都会读取这个文件，它决定了 Claude Code 对你项目的理解深度。我们 30 个仓库几乎每个都有 CLAUDE.md，最短 148 行，最长 1062 行。写得越好，Claude Code 的产出质量越高。

> **案例：** 我们的 AI Shell 系统（[optima-ai-shell](https://github.com/Optima-Chat/optima-ai-shell)）是一个典型的 Monorepo，包含 5 个 TypeScript 包：AI CLI 核心、Session Gateway、Web UI、Lambda Handler、AgentCore Adapter。一个 324 行的 [CLAUDE.md](https://github.com/Optima-Chat/optima-ai-shell/blob/main/CLAUDE.md) 统一指导 Claude Code 理解 5 个包之间的关系、认证流程和通信协议。

---

## 3. 让 Claude Code 大量写自动化测试

单元测试、集成测试、API 测试，都往上招呼。并且把运行测试作为 CI 的前序步骤，最大程度确保代码正确性。

我一般的 repo，测试代码行数和功能代码行数差不多。1 万行功能代码，1 万行测试代码。

Claude Code 写的代码你不确定对不对，但测试是验证正确性最可靠的手段。让它写测试的成本极低——它很擅长——但带来的质量保障价值极高。**测试 + 静态类型检查**，是用 Claude Code 开发时确保代码质量的两道防线。

> **案例：** [commerce-backend](https://github.com/Optima-Chat/commerce-backend) 有 443 个测试用例，100% 通过率。覆盖单元测试、API 集成测试、WebSocket 协议测试。其中 WebSocket 集成测试（12KB）验证了真实连接、数据库持久化、JWT 认证和竞态条件处理。

---

## 4. 多开是效率的来源

单开 1 天最多大约 5,000 行代码。多开可以到 30,000 行。

我一般 4 开，多的时候 8 开，同时做 8 个开发任务。

前提是技术方案要做得足够细，Claude Code 才能独立执行，你才能放心多开。**多开本质上是对前置工作质量的兑现**——技术方案越细，多开越安全。

> **案例：** [optima-agent](https://github.com/Optima-Chat/optima-agent) 在 2025 年 11 月 28-30 日三天内产生了 175 次 commit，单日最高 67 次。同期 [optima-ai-shell](https://github.com/Optima-Chat/optima-ai-shell) 的 5 个包（AI CLI、Gateway、Web UI、Lambda、Adapter）也在并行推进不同的 Phase。这就是多开的实际效果。

---

## 5. 要充分理解代码原理

不能因为是 Claude Code 写的代码就不去了解。当代码量上来以后，解决复杂 bug 往往需要人类提供方向性的建议，能大大减少 debug 时间。

特别是，当 Claude Code 分析完代码给出它认为的原因后，你要能判断到底靠不靠谱。不必去看代码，但是要了解原理。

人类是架构师和决策者，Claude Code 是执行者。你不需要知道每一行代码怎么写，但你必须知道系统是怎么运转的。

> **案例：** AI Shell 的 WebSocket 消息出现乱序，Claude Code 分析后认为是网络延迟。但因为我了解系统原理，判断根因是 async 回调——多个消息到达时，`await` 数据库保存的完成顺序不确定，导致发送顺序乱了。给出方向后，Claude Code 很快改成同步发送、异步存储，问题解决。

---

# 第二部分：团队协作

## 6. CLI First：给 AI 和人类设计同一套工具

所有系统能力都优先封装成 CLI 命令。CLI 是 AI 最自然的操作接口——Claude Code 天生会用命令行，但操作 GUI 很难。

我们把电商系统的所有操作封装成了 commerce-cli（16 个模块、111+ 命令），商品管理、订单处理、库存、运费、国际化翻译，全部是命令行。AI 用它来自动运营店铺，开发者用同一套命令来调试和测试。一套工具，两种用户。

这也是我们架构演进的核心教训。早期用 MCP 协议，每个能力都要单独实现一套 AI 接口，维护成本高。后来迁移到 Agent + CLI 架构——AI Agent 直接调用 CLI 命令，新增能力只需要写一个 CLI 命令，AI 和人类立刻都能用。

> **案例：** [commerce-cli](https://github.com/Optima-Chat/commerce-cli) 默认输出 JSON 格式（AI 友好），加 `--pretty` 输出彩色表格（人类可读）。这个设计来自 [Issue #9](https://github.com/Optima-Chat/commerce-cli/issues/9)——我们发现表格的 ANSI 转义码浪费了 50% 以上的 token。改成 JSON 默认后，AI 解析效率大幅提升，人类需要时加个参数就行。

---

## 7. 做团队自己的 Dev Skills

把自己系统的代码、日志、数据库、环境变量，如何访问、查询、设置，都做到 dev-skills 里。这样在开发过程中，Claude Code 可以很方便地访问和操作这些资源。

效果是，很多功能开发、问题修复，Claude Code 就可以全程全自动完成，不需要人为费劲提供信息。

我们做了 [optima-dev-skills](https://github.com/Optima-Chat/optima-dev-skills)，通过 NPM 包分发 6 个自定义 Skill（查日志、查数据库、生成测试 Token、查环境变量、重启服务、读代码），覆盖开发、测试、生产三个环境，团队成员装完包就能用。

> **案例：** 开发者说"Stage 的商品 API 返回 500，帮我看看"。Claude Code 自动调用查日志 Skill 拉取 CloudWatch 日志，发现 Database connection timeout，定位到 Stage RDS 连接配置问题。全程不需要人告诉它怎么连日志系统。

---

## 8. 充分利用 GitHub Issues 和 PR

Claude Code 有很强的使用 GitHub 的能力，开发过程可以走标准的 GitHub 开发流程。

我们现在团队成员之间交流，就是互相发 Claude Code 写的 issue。特别是需要在多个 repo 之间传递信息时，让 A repo 的 Claude Code 直接给 B repo 写 issue。它写 issue 信息完整详实，比人工描述更准确，方便 B repo 修复问题，并且留下记录。

PR 也一样，用来充分记录变更信息和设计意图。GitHub Issues + PR 既是协作工具，也是知识沉淀。Claude Code 擅长写详细的描述，正好弥补了人类懒得写文档的问题。

> **案例：** commerce-cli 的 [Issue #33](https://github.com/Optima-Chat/commerce-cli/issues/33)，Claude Code 分析后发现 CLI 和老的 MCP 接口之间有 23 个命令缺失，写了一份详细的对比矩阵。同一天 [PR #34](https://github.com/Optima-Chat/commerce-cli/pull/34) 就提交了修复——17 个 commit、45 个文件、+4202 行代码。从发现问题到解决问题，一天之内闭环。

---

## 9. 让 Claude Code 做 Code Review

过去 code review 成本高，大家不愿意做。现在让 Claude Code 先审一遍——测试覆盖、逻辑漏洞、安全问题，它都能发现。人再审架构合理性和业务逻辑。两层审下来，质量比过去高很多，成本反而低了。

> **案例：** 我们让 Claude Code 对 commerce-backend 做了一次全面 Code Review，产出了一份 291 行的评审报告（[docs/COMPREHENSIVE_CODE_REVIEW.md](https://github.com/Optima-Chat/commerce-backend/blob/main/docs/COMPREHENSIVE_CODE_REVIEW.md)）。8 个维度打分（API 设计 9/10、安全 7.5/10、性能 6.5/10），发现了 CORS 配置允许所有来源、N+1 查询等具体问题，并给出了分 3 个阶段的修复计划。

---

## 10. 充分利用 Claude Code 的文档能力

Claude Code 写文档详尽、准确、格式规范。不要浪费这个能力。

对内，用它写技术方案、RFC、月报、架构文档，让团队知识沉淀下来。对外，用它写给上下游合作伙伴的技术对接文档、API 说明、问题报告，沟通效果远超人工手写。

过去团队不愿意写文档，是因为成本太高。现在 Claude Code 把这个成本降到几乎为零，文档应该成为开发流程的标配而不是负担。

> **案例：** 我们的系统架构文档（[OPTIMA_COMMERCE_ARCHITECTURE.md](https://github.com/Optima-Chat/optima-hub/blob/main/OPTIMA_COMMERCE_ARCHITECTURE.md)）超过 60KB，覆盖 30 个仓库的完整架构、依赖关系、Mermaid 架构图、用户故事、生产环境部署。这份文档就是 Claude Code 写的，人工来写可能要一周，它几个小时就完成了。

一个附带的好处是新人上手变快了。有了 CLAUDE.md、Dev Skills、充分的文档和测试，新人加入团队后，Claude Code 能帮他理解代码库、熟悉流程、定位问题。过去需要老人带几周，现在大大缩短。

---

## 结尾

以上 10 条是我们 10 个月实战中反复验证过的经验。核心思路就两个：

**对个人**——不要把 Claude Code 当打字机，要把它当一个能力极强但需要清晰指令的工程师。你的工作重心从写代码转向做决策：学习、设计方案、审方案、理解原理。

**对团队**——围绕 AI 重新设计工具链和协作流程。CLI First、Dev Skills、Issues/PR、Code Review、文档，这些不是额外的工作，而是让 AI 效率最大化的基础设施。

起步其实很简单：选一个新项目，用 TypeScript + Monorepo，写好 CLAUDE.md，让 Claude Code 帮你写测试。从这里开始，逐步建立自己团队的 AI Native 开发体系。
