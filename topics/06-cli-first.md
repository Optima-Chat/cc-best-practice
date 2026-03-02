# 给你的后端写个 CLI

> 第 6 点展开 | AI Native 开发最佳实践

---

## 核心问题：Claude Code + API vs Claude Code + CLI

让 Claude Code 测一遍 API 端点，远比让它跑一条 CLI 命令慢得多，也更容易出错。

**Claude Code + API（7 步，慢且易错）：**

1. 获取 auth token
2. 拼 Authorization header
3. 构造 JSON body
4. 拼完整 URL + query params
5. 发送 curl/fetch 请求
6. 解析返回的 JSON
7. 处理错误码和异常

每一步都可能出问题。

**Claude Code + CLI（1 步，快且可靠）：**

```bash
optima product create --title "Test" --price 29.99
```

一行命令，直接出结果。

测 API 要处理什么？认证 token、请求头、请求体格式、URL 拼接、错误码解析——每一步都可能出问题。但 CLI 就是一行命令、一段输出，Claude Code 天然擅长。

---

## CLI 让 Claude Code 能随意组合测试

有了 CLI，你可以直接在 Claude Code 里按任意需要组合功能来测试。不需要写测试脚本，不需要配 Postman，想测什么直接说。

```bash
# 创建商品 → 设置库存 → 创建订单 → 全流程验证
optima product create --title "Test" --price 29.99
optima inventory set --sku SKU-001 --quantity 100
optima order create --items '[{"sku":"SKU-001","qty":1}]'
optima order list --status pending --pretty
```

同样的流程如果走 API，Claude Code 需要处理认证、拼 curl、解析返回值，每一步都可能卡住。**CLI 把这些全部封装了。**

---

## 案例：commerce-cli

[commerce-cli](https://github.com/Optima-Chat/commerce-cli) 是我们给 commerce-backend 写的完整 CLI 封装：19 个模块、280+ 条命令，覆盖商品、订单、库存、物流、退款、国际化等全部操作。

写 CLI 本身也是 Claude Code 最擅长的事——纯逻辑、纯文本、不涉及 UI，一次性通过率非常高。我们的 CLI 模块基本都是 Claude Code 一次写完的。

---

## 额外收益：CLI 可以直接给 AI Agent 用

我们最初写 CLI 是为了给 Claude Code 的 Skills 调用，替代 MCP 协议。但发现这套 CLI 同时解决了三个场景：

1. **开发测试** — Claude Code 快速验证功能
2. **人工运营** — 运营人员在终端操作
3. **AI Agent 自动化** — Agent 直接调用 CLI 命令

一套命令，三种用户。

---

## 小结

**写个 CLI 永远不亏。** Claude Code 写得快、测得准、还能直接复用给 AI Agent。
