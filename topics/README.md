# AI Native 开发最佳实践

> 徐昊 | Optima AI 创始人
> 3 人团队，6 个月，30 个仓库，120 万行代码

---

## 开场

Optima AI 做跨境电商 AI 平台，核心团队 3 个人，6 个月写了 120 万行代码，30 个仓库，8000 多次 commit。全程用 Claude Code 开发。

这个代码量和系统复杂度，按传统模式至少需要 20-30 人的团队。我们 3 个人做到了，靠的不是加班，而是从根本上改变了开发方式。

今天分享的不是理论，是我们踩了 6 个月坑之后总结出来的实战经验。分两部分：个人怎么用好 Claude Code，以及团队怎么围绕它协作。

一个核心观点先说在前面：**AI Native 开发最大的变化，是工程师的角色从"写代码的人"变成了"做决策的人"。** 学习、设计方案、审方案、理解原理、搭建工具链——这些才是人类该做的事。写代码交给 AI。

---

# 第一部分：个人开发

## 1. [先学习、再做方案、再写代码](01-learn-plan-code.md)

不要直接让 Claude Code 写代码。正确的流程是：**学习（1-2h）→ 技术方案 → 审方案 → 写代码**。先花时间从 Claude Code 那里学习领域知识，再让它写技术方案，你审完确认没问题后再动手。技术方案做透了，写代码很快——5000 行不过 30 分钟。

## 2. [选对工程基础设施](02-engineering-infrastructure.md)

三个关键选择：**全部用 TypeScript**（Claude Code 最擅长的语言，静态类型检查能排除大量错误）、**尽量用 Monorepo**（共享 Context，开发效率最高）、**写好 CLAUDE.md**（项目知识库，决定了 Claude Code 对你项目的理解深度）。

## 3. [让 Claude Code 大量写自动化测试](03-automated-testing.md)

Claude Code 写的代码你不确定对不对，但测试可以告诉你。让它写测试的成本极低——它很擅长——但带来的质量保障价值极高。我们的 [commerce-backend](https://github.com/Optima-Chat/commerce-backend) 有 1555 个测试用例，100% 通过率。**测试 + 静态类型检查**，是确保代码质量的两道防线。

## 4. [多开是效率的来源](04-parallel-development.md)

单开 1 天最多大约 5,000 行代码，多开可以到 30,000 行。我一般 4 开，多的时候 8 开——一个仓库一个 Claude Code 实例，同时推进多个仓库的任务。前提是技术方案要做得足够细，Claude Code 才能独立执行。**多开本质上是对前置工作质量的兑现。**

## 5. [要充分理解代码原理](05-understand-principles.md)

不能因为是 Claude Code 写的代码就不去了解。当代码量上来以后，解决复杂 bug 往往需要人类提供方向性的建议。你不需要知道每一行代码怎么写，但你必须知道系统是怎么运转的。

---

# 第二部分：团队协作

## 6. [CLI First：给 AI 和人类设计同一套工具](06-cli-first.md)

所有系统能力都优先封装成 CLI 命令——CLI 是 AI 最自然的操作接口。我们把电商系统的所有操作封装成了 [commerce-cli](https://github.com/Optima-Chat/commerce-cli)（19 个模块、155+ 命令），AI 和人类用同一套命令。这也是架构演进的核心教训：从 MCP 协议迁移到 Agent + CLI，新增能力只需写一个命令。

## 7. [做团队自己的 Dev Skills](07-dev-skills.md)

把系统的代码、日志、数据库、环境变量的访问方式都做到 Dev Skills 里。效果是很多功能开发、问题修复，Claude Code 可以全程全自动完成，不需要人为费劲提供信息。

## 8. [充分利用 GitHub Issues 和 PR](08-github-issues-pr.md)

团队成员之间交流，就是互相发 Claude Code 写的 Issue。特别是跨仓库传递信息时，让 A repo 的 Claude Code 直接给 B repo 写 Issue——信息完整详实，比人工描述更准确。PR 也一样，充分记录变更信息和设计意图。

## 9. [让 Claude Code 做 Code Review](09-code-review.md)

Claude Code 先审一遍——测试覆盖、逻辑漏洞、安全问题，它都能发现。人再审架构合理性和业务逻辑。两层审下来，质量比过去高很多，成本反而低了。

## 10. [充分利用 Claude Code 的文档能力](10-documentation.md)

Claude Code 写文档详尽、准确、格式规范。对内写技术方案、架构文档，对外写对接文档、API 说明。过去团队不愿意写文档是因为成本太高，现在成本降到几乎为零，文档应该成为开发流程的标配。

## 11. [用 Claude Code 做 BI 分析](11-bi-analysis.md)

传统 BI 告诉你"DAU 下降了 23%"，Claude Code 能告诉你**为什么**——从用户对话记录等非结构化数据中提炼洞察。定量 + 定性，才是完整的用户理解。**相当于每周做一次全面的用户调研，成本几乎为零。**

---

## 小结

以上 11 条是我们 6 个月实战中反复验证过的经验。核心观点只有一个：**AI Native 开发最大的变化，是工程师从"写代码的人"变成了"做决策的人"。** 写代码不再是瓶颈，做决策才是。

---

# [常见问题与局限](12-faq-and-limitations.md)

踩过的坑、代码质量与长期维护、存量系统怎么用、Claude Code vs Copilot、数据安全（含本地化部署方案）、AI 生成代码的知识产权、供应商依赖——详见[常见问题文档](12-faq-and-limitations.md)。

---

# [合作探讨](13-cooperation.md)

适用场景、Java/Python 团队如何切入、不同级别工程师的切入方式、成本对比、合作提案——详见[合作探讨文档](13-cooperation.md)。
