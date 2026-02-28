# 做团队自己的 Dev Skills

> 第 7 点展开 | AI Native 开发最佳实践

---

## 什么是 Skills

先说清楚 Skills 是什么。很多人知道 CLAUDE.md，但 Skills 是另一个维度的东西。

CLAUDE.md 是你项目的知识库，Claude Code 每次启动都会加载它。但正因为每次都加载，它必须保持精简——你不可能把所有东西都塞进去，否则上下文窗口浪费在了一些当前任务根本用不到的信息上。

Skills 不一样。Skills 是**按需加载的知识文件**。Claude Code 只在相关的时候才会激活它们。比如你说"帮我查一下 Stage 的日志"，它才会去加载查日志的 Skill；你说"帮我跑个 SQL"，它才加载查数据库的 Skill。平时不会占用上下文。

打个比方：CLAUDE.md 是你随身带的笔记本，里面记着最重要的事情。Skills 是你办公桌抽屉里的操作手册，需要的时候才拿出来翻。

这个区分很重要。我见过有些团队把 CLAUDE.md 写到几千行，里面什么都有——日志怎么查、数据库怎么连、环境变量在哪里。结果每次启动 Claude Code，它光读这些信息就耗掉大量上下文，真正干活的空间反而少了。正确的做法是：**CLAUDE.md 放架构、规范、约定这些每次都需要的东西，操作类的指南放到 Skills 里按需加载。**

---

## 我们做了什么

我们做了一个叫 [optima-dev-skills](https://github.com/Optima-Chat/optima-dev-skills) 的项目，里面包含 6 个自定义 Skill，覆盖了日常开发中最常见的运维和调试需求。

这 6 个 Skill 分别是：

### 1. logs（205 行）—— 查看服务日志

这个 Skill 教会 Claude Code 怎么通过 AWS CloudWatch 查看我们各个服务的日志。

它覆盖三个环境：CI（持续集成）、Stage（预发布）、Production（生产）。每个环境的 CloudWatch Log Group 名称、区域、查询语法，全部写在 Skill 里。

使用场景：你跟 Claude Code 说"帮我看看 Stage 的 commerce-backend 最近 30 分钟有没有报错"，它会自动激活这个 Skill，构造正确的 CloudWatch Logs Insights 查询，拉取日志，分析错误信息，给你总结。

你不需要记住 Log Group 叫什么名字，不需要知道 CloudWatch 的查询语法，甚至不需要登录 AWS 控制台。

### 2. query-db（211 行）—— 查询数据库

这个 Skill 让 Claude Code 能够对我们的数据库执行 SQL 查询。

重点是**它会自动管理 SSH 隧道**。我们的数据库都在 VPC 内网，不能直接连接，必须通过跳板机建立 SSH 隧道。这个过程对人来说就很烦——先 ssh 到跳板机，建隧道，连数据库，查完了还要关隧道。但 Skill 里写清楚了完整的步骤，Claude Code 自动处理，你只需要说"帮我查一下 Stage 数据库里某个商品的数据"。

同样覆盖 CI、Stage、Production 三个环境，每个环境的跳板机地址、数据库连接串、SSH 密钥路径，都在 Skill 里配好了。

### 3. generate-test-token（331 行）—— 生成测试 Token

这是 6 个 Skill 里最长的一个，331 行。

为什么需要这个？因为我们是电商系统，几乎所有 API 都需要认证。Claude Code 要测试一个 API，首先得有一个合法的 OAuth Token。

这个 Skill 教 Claude Code 完成整个流程：注册一个测试账号、走 OAuth 认证流程、拿到 Access Token。它甚至知道怎么处理不同环境的认证服务地址、Client ID、Redirect URI。

有了这个 Skill，Claude Code 测试 API 的时候再也不需要你手动去拿 Token 了。它自己注册、认证、拿 Token、调 API，全程自动。

### 4. show-env（185 行）—— 查看环境变量

我们用 Infisical 管理环境变量（类似于 HashiCorp Vault 但更轻量）。这个 Skill 教 Claude Code 怎么通过 Infisical CLI 查看各个服务、各个环境的配置。

使用场景：Claude Code 在排查问题的时候，可能需要确认某个环境变量的值对不对——数据库连接串是否正确、第三方 API Key 有没有过期、Feature Flag 开了没有。有了这个 Skill，它直接去查，不用你帮它查。

### 5. use-commerce-cli（64 行）—— 使用 commerce-cli

这个最短，只有 64 行，因为它主要是把 commerce-cli 的使用方式介绍给 Claude Code。让它知道可以通过 CLI 来操作电商系统的各种资源——商品、订单、库存、运费等。

### 6. read-code（207 行）—— 读取其他仓库的代码

这个 Skill 让 Claude Code 能够跨仓库读取代码。我们有 30 个仓库，Claude Code 在 A 仓库工作的时候，可能需要看 B 仓库的某段代码来理解接口或数据结构。

它通过 GitHub API 直接读取代码文件，不需要你手动把 B 仓库的代码贴过来。

---

## 分发机制：一行命令装好

Skills 怎么分发给团队成员？这是一个很实际的问题。

我们的做法是**通过 NPM 包分发**。

```bash
npm install -g @optima-chat/dev-skills@latest
```

一行命令搞定。

技术上的实现很简单：NPM 包的 `postinstall` hook 会自动把 6 个 Skill 文件复制到 `~/.claude/` 目录下。Claude Code 启动时会扫描这个目录，发现有可用的 Skills 就会在合适的时候激活它们。

这个设计的好处是：

- **安装简单。** 不需要手动配置，装完就能用。
- **更新简单。** 我们更新了 Skill 内容，团队成员只需要重新 `npm install` 最新版本。
- **一致性。** 所有人用的都是同一份 Skill，不会出现"我机器上能用但你那边不行"的问题。
- **版本管理。** NPM 天然支持版本管理，需要的话可以回退。

---

## 实战案例："Stage 的商品 API 返回 500"

说一个真实的使用场景，让大家感受一下 Dev Skills 在实际开发中的效果。

开发者跟 Claude Code 说了一句话：

> "Stage 的商品 API 返回 500，帮我看看。"

就这一句话。接下来发生了什么：

**第一步：Claude Code 激活 logs Skill。**

它知道要排查 API 错误，首先得看日志。logs Skill 告诉它 Stage 环境的 commerce-backend 日志在哪个 CloudWatch Log Group，用什么查询语法。它自动构造一个 CloudWatch Logs Insights 查询，拉取最近 30 分钟的错误日志。

**第二步：分析日志，发现错误。**

日志里清楚地写着：`Database connection timeout`。数据库连接超时。

**第三步：进一步排查，激活 show-env Skill。**

Claude Code 判断可能是数据库连接配置的问题。它自动激活 show-env Skill，通过 Infisical 查看 Stage 环境的数据库相关配置。

**第四步：定位根因。**

发现连接池的 `max_connections` 设置过小，在流量高峰期连接数不够用，导致新请求拿不到连接就超时了。

**第五步：给出修复建议。**

Claude Code 建议调整 `max_connections` 的值，并给出了具体的配置修改方案。

整个过程，开发者只说了一句话。Claude Code 自己查日志、查配置、分析原因、给出方案。**全程全自动。**

如果没有 Dev Skills，这个过程是什么样的？开发者需要：

1. 手动登录 AWS 控制台
2. 找到正确的 CloudWatch Log Group（你得记住它叫什么）
3. 写 Logs Insights 查询语句
4. 看到 Database connection timeout 后，再去 Infisical 查配置
5. 对比配置和实际负载，判断问题

中间每一步都可能因为记不住某个名字、某个路径而卡住。特别是新人——他可能连 CloudWatch 怎么用都不知道。

有了 Dev Skills，**Claude Code 就像一个记住了所有操作细节的资深工程师**，随叫随到。

---

## 对团队管理意味着什么

说到这里，我想跟在座的管理者多聊几句。因为 Dev Skills 的意义远不止提高效率。

### 机构知识的编码化

每个团队都有"机构知识"（Institutional Knowledge）——怎么查日志、怎么连数据库、怎么拿测试 Token、某个环境的特殊配置。这些知识通常存在于资深工程师的脑子里，靠口口相传。

问题是：人会离职、会忘记、会忙得顾不上教新人。

Dev Skills 把这些机构知识**编码成了 AI 可以执行的指令**。不是写在文档里让人去读（人也不一定读），而是直接变成 Claude Code 的能力。知识不再依赖于某个人，而是变成了团队的基础设施。

### 新人上手时间的急剧缩短

传统模式下，一个新人加入团队，光熟悉环境就要好几天：各种工具的安装、各种服务的连接方式、各种权限的申请。然后还要老人带着走一遍开发、调试、排查问题的流程。

有了 Dev Skills：

```bash
npm install -g @optima-chat/dev-skills@latest
```

一行命令。新人装完这个包，他的 Claude Code 就具备了和资深工程师一样的操作能力。他可以查日志、查数据库、生成测试 Token、查环境变量——所有这些操作，Claude Code 都会帮他完成。

新人不需要记住 CloudWatch 的 Log Group 叫什么，不需要知道 SSH 隧道怎么建，不需要搞清楚 OAuth 的完整流程。这些细节 Claude Code 全知道。新人只需要描述他想做什么，Claude Code 就能执行。

这对于大型组织来说意义重大。你们有多少时间花在带新人上？有多少资深工程师的时间被消耗在回答"这个日志怎么查""那个数据库怎么连"上？

### AI 真正自主运行的前提

很多人用 Claude Code，效果不好，觉得"AI 不行"。但实际原因往往是：**AI 缺少执行任务所需的信息和工具**。

你让 Claude Code 排查一个线上问题，但它不知道怎么查日志、不知道数据库在哪里、不知道环境变量是什么值。它只能反过来问你，你再手动去查，把结果贴给它。这种模式下，AI 只是一个需要你不断喂信息的对话框，效率并不高。

Dev Skills 解决的就是这个问题。**它给了 AI 自主获取信息的能力。** 有了 Skills，Claude Code 不再需要你一步一步地喂信息，它自己知道去哪里查、怎么查。这才是 AI 真正能自主运行的前提。

---

## 怎么开始做自己团队的 Dev Skills

如果你想在自己的团队试一试，建议从这几个方面入手：

**第一，列出高频操作。** 你的团队日常开发中最常做的操作是什么？查日志？查数据库？看配置？连测试环境？把最高频的 5-10 个操作列出来。

**第二，写 Skill 文件。** 每个 Skill 就是一个文本文件，内容是操作步骤和相关信息。不需要什么特殊格式，用自然语言写清楚就行。关键信息包括：要用什么命令、环境有哪些、每个环境的参数是什么、常见的注意事项。

**第三，选择分发方式。** 我们用的是 NPM 包，因为我们是 TypeScript 技术栈，团队都装了 Node.js。如果你们用 Python，可以用 pip 包；如果用其他语言，搞一个简单的安装脚本也行。核心是让团队成员能一行命令装好。

**第四，持续维护。** Skills 不是写完就不管了。环境变了、工具升级了、新增了服务，都要同步更新 Skills。好在维护成本很低——就是改改文本文件。

---

## 小结

Dev Skills 的本质，是把"怎么操作"这件事从人的记忆里搬到 AI 的能力里。

对个人来说，它让你少记很多东西，少做很多重复操作。
对团队来说，它让机构知识不再依赖于某个人，新人能快速上手。
对 AI 来说，它是从"需要人喂信息的对话框"变成"能自主解决问题的助手"的关键一步。

一个 NPM 包，6 个 Skill 文件，一千多行文本。投入极小，回报极大。
