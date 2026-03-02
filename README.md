# AI Native 开发最佳实践

[![Website](https://img.shields.io/badge/Website-cc--best--practice.optima--ai.biz-00ff88?style=flat-square)](https://cc-best-practice.optima-ai.biz/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue?style=flat-square)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/Optima-Chat/cc-best-practice?style=flat-square&color=yellow)](https://github.com/Optima-Chat/cc-best-practice/stargazers)
[![Last Commit](https://img.shields.io/github/last-commit/Optima-Chat/cc-best-practice?style=flat-square)](https://github.com/Optima-Chat/cc-best-practice/commits/main)
[![Made with Claude Code](https://img.shields.io/badge/Made%20with-Claude%20Code-blueviolet?style=flat-square)](https://claude.ai/claude-code)

> 3 人团队 · 6 个月 · 30 个仓库 · 126 万行代码 · 8,105 次 Commit

基于 Claude Code 的 AI Native 开发实战经验。12 条最佳实践，从个人开发到团队协作的完整方法论。

**[在线阅读 →](https://cc-best-practice.optima-ai.biz/)**

## 这是什么

一份来自真实生产项目的 AI Native 开发指南。不是理论，不是 demo——是一个 3 人团队用 Claude Code 构建完整商业产品的全部经验。

内容覆盖：

| 部分 | 内容 |
|------|------|
| **个人开发** | 先学习再做方案、选对工程基础设施、大量写自动化测试、多开并行、理解代码原理 |
| **团队协作** | 给后端写 CLI、做 Dev Skills、用 GitHub Issues/PR 沟通、AI Code Review、文档能力、BI 分析 |
| **实践建议** | 存量项目怎么切入、新项目 3 人交付 20 人的活、Coding Agent 产品机会 |
| **常见问题** | 踩过的坑、UI 开发难点、CC vs Codex vs Gemini CLI、数据安全、知识产权 |

## 核心观点

- **代码质量**：Claude Code 写的代码在任何情况下都比人写的好。真正依赖方案质量的是业务逻辑的正确性。
- **工程基础设施**：TypeScript + Monorepo + CLAUDE.md + 自动化测试 + CI 门禁，缺一不可。
- **多开是效率关键**：4-8 个 Claude Code 实例并行，一天 50-70 commit 是正常速度。
- **AI 做重复的事，人做贵的事**：方案设计、架构决策、Code Review 留给人；写代码、写测试、写文档交给 AI。

## 技术栈

这个网站本身就是用 Claude Code 写的——单文件 HTML，暗色终端风格，零依赖。

```
web/
├── index.html          # 全部内容，~2500 行
├── favicon.svg         # 终端风格图标
└── images/
    ├── hero.png
    ├── wechat-qr.png
    └── screenshots/    # 实际项目截图
```

## 本地预览

```bash
cd web && python3 -m http.server 8080
# 打开 http://localhost:8080
```

## 部署

托管在 Vercel，push 到 main 自动部署。

```bash
cd web && vercel --prod
```

## 关于作者

**徐昊** · Optima AI 创始人

用 Claude Code 从零构建了完整的跨境电商平台（180+ API、1555 测试）、AI Agent 框架、AI Shell 等产品。

- 产品体验：[www.optima.onl](https://www.optima.onl/)
- 公司主页：[optima-ai.biz](https://optima-ai.biz/)

## License

MIT
