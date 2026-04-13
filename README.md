# Bruno API Test Demo

一个用 [Bruno](https://www.usebruno.com/) 写的最小可运行的 API 测试集合，配套公众号文章「Postman 越来越臃肿了，我换了个开源的，还能让 AI 帮我写测试」。

调用的是 [DummyJSON](https://dummyjson.com/) 的免费公开 API，无需任何 key，clone 下来直接就能跑。

## 这个 demo 想说明什么

Bruno 把 API 测试存成纯文本 .bru 文件，用 CLI 跑测试。这两件事看起来普通，但放在一起意味着：

**你的 API 测试可以被 Claude Code、Cursor 这类 AI 编程工具直接读、写、跑、调试，不需要任何额外的 MCP server 或插件。**

仓库里的 `06-搜索商品.bru` 和 `07-购物车测试.bru` 这两个文件就是真实演示出来的产物，作者让 Claude Code 一步步生成、运行、自动调试做出来的。

## 跑一下

```bash
# 装 Bruno CLI
npm i -g @usebruno/cli

# 跑全部测试
bru run --env production
```

预期输出：

```
📊 Execution Summary
┌───────────────┬──────────────┐
│ Status        │    ✓ PASS    │
│ Requests      │ 7 (7 Passed) │
│ Tests         │    25/25     │
└───────────────┴──────────────┘
```

跑单个测试：

```bash
bru run 01-用户登录.bru --env production
```

切换环境：

```bash
bru run --env staging
```

输出 JUnit 报告（CI 用）：

```bash
bru run --env production --reporter-junit junit.xml
```

输出 HTML 报告（人看的）：

```bash
bru run --env production --reporter-html report.html
```

## 集合内容

| 文件 | 测试场景 | 演示什么 |
|------|---------|---------|
| 01-用户登录.bru | POST /auth/login | 环境变量、登录后保存 token 给后续用 |
| 02-获取用户信息.bru | GET /auth/me | bearer token 认证 |
| 03-获取用户列表.bru | GET /users | 分页参数、数组遍历断言 |
| 04-创建文章.bru | POST /posts/add | 创建资源、引用上下文变量 |
| 05-错误处理-未授权.bru | GET /auth/me | 故意触发 401 |
| 06-搜索商品.bru | GET /products/search | **AI 生成的测试用例** |
| 07-购物车测试.bru | GET /carts/1 | **AI 生成 + 自动调试修复** |

## 让 AI 帮你写测试的两个真实场景

### 场景一：写新测试

进入这个目录，跟 Claude Code 或 Cursor 说：

```
我刚加了一个搜索商品的接口，帮我在这个 bruno 集合里加一个测试用例
```

它会做三件事：

1. 读一个已有的 .bru 文件理解格式
2. 写一个新的 .bru 文件
3. 跑 `bru run` 验证

`06-搜索商品.bru` 就是这么生成的。

### 场景二：自动调试失败的测试

把 `07-购物车测试.bru` 里的字段名故意改回错的（比如 `total` 改成 `totalPrice`），然后跟 AI 说：

```
帮我跑一下 07-购物车测试.bru，挂了的话自己想办法修
```

它会：

1. `bru run` 看到失败信息
2. `curl` 拉真实接口响应
3. 找到正确的字段名
4. 改 .bru 文件
5. 再跑一遍验证

整个过程不需要你介入。

## CI/CD 集成

`.github/workflows/api-test.yml` 已经写好了一个 GitHub Actions 工作流，每次 PR 自动跑全部接口测试。

```yaml
- name: 安装 Bruno CLI
  run: npm install -g @usebruno/cli

- name: 跑接口测试
  run: bru run --env production --reporter-junit junit.xml
```

## 为什么不是 Postman

Postman 也有 CLI（Newman），但 Postman 的核心数据存在云端、操作靠 GUI、协作靠登录，这三件事每一件都把 AI agent 挡在外面。

Bruno 的核心数据是 .bru 文本文件、操作靠 CLI、协作靠 Git。每一件都正好是 AI 最擅长的那种事。

## 相关链接

- [Bruno 官网](https://www.usebruno.com/)
- [Bruno GitHub](https://github.com/usebruno/bruno)
- [DummyJSON](https://dummyjson.com/)
- 配套文章（公众号「小盒子的技术分享」）

## License

MIT
