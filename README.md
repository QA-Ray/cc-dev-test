# cc-dev-test

Claude Code skill：开发提测前自检。基于 git diff 用资深 QA 视角过一遍改动，按 11 维度出 must / should / nice 自测清单 + 可执行动作。

技术栈无关——自动探测 `go.mod` / `package.json` / `pyproject.toml` / `Cargo.toml` / `pom.xml`。

## 安装（三选一）

**A. npx 一行装**

```bash
npx skills add QA-Ray/cc-dev-test
```

**B. git clone + 软链**（推荐，`git pull` 自动同步更新）

```bash
git clone https://github.com/QA-Ray/cc-dev-test.git ~/Code/cc-dev-test
ln -sfn ~/Code/cc-dev-test ~/.claude/skills/cc-dev-test
```

**C. tiged 文件快照**

```bash
npx tiged QA-Ray/cc-dev-test ~/.claude/skills/cc-dev-test
```

装完重启 Claude Code session 生效。

## 用法

进入你要自检的 git 仓库，在 Claude Code 里：

```
/cc-dev-test                          # 默认基线 origin/main
/cc-dev-test path=internal/order      # 聚焦某目录
/cc-dev-test base=origin/develop      # 改基线分支
帮我看下这次改动要测什么              # 自然语言触发
```

输出：Markdown 报告（按改动函数分节，每节列适用维度 + 可执行动作 + 优先级）+ 末尾 YAML 汇总。

## 11 个测试维度

| 主流程 F · 分支 B · 输入校验 V · 错误传播 E · 鉴权 A · 状态机 S |
| 幂等 I · 并发 C · 数据一致性 D · 性能边界 P · 回归 R |

不适用就跳过，不凑数。

## 自动加码（命中即 must）

- 文件 / 函数名含 `order|trade|balance|withdraw|deposit|position|fund|asset` → C / I / D / S
- 新增或修改 HTTP / RPC handler → A / V
- 新增 migration / DDL → R / D
- 改了 `auth|permission|role|token` → A
- 改了 cache / outbox / 队列消费 → I + 一致性维度

## 不做

- 不替你跑测试（你自己跑）
- 不改你的源代码
- 不替代 PRD review / QA 用例评审 / 集成测试

## 依赖

Claude Code（CLI / 桌面）。无其他外部依赖（不用 rsvg-convert / Node / Python）。
