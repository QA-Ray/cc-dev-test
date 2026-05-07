---
name: cc-dev-test
description: |
  从资深测试工程师视角，给开发在「提测前」过一遍 git diff，按多维度
  给出 must / should / nice 自测清单 + 可执行动作。技术栈无关——自己
  探测 go.mod / package.json / pyproject.toml 等。

  TRIGGER when user asks to: 自测, 提测前自检, 自检, dev self test,
  pretest checklist, 我改了 X 帮我看要测什么. Also from `/cc-dev-test`.
---

# Dev Self-Test (提测前自检)

资深 QA 的脑子，过一遍 diff。目标：把低级 bug 拦在提测前。

## 原则

1. **看「改了什么」不够，要看「可能让什么坏掉」**——grep 反查调用方 / SQL 引用 / route 消费方。
2. **资金 / 状态机 / 鉴权 类改动宁可过度自检**，漏的代价远大于过度的代价。
3. **skill 看不见的东西要明说**——跨仓库 / 运行时配置 / 历史数据 / 外部契约 / 性能基线，列到「盲区」让开发自己评估。
4. **给可粘贴的动作**，不写「应该测试 X」的空话；要么给代码 / curl / SQL，要么删掉这条。

## 11 个思考维度

| 主流程 F · 分支 B · 输入校验 V · 错误传播 E · 鉴权 A · 状态机 S |
| 幂等 I · 并发 C · 数据一致性 D · 性能边界 P · 回归 R |

不适用就跳过，不凑数。

## 自动加码（命中即 must）

- 文件 / 函数名含 `order|trade|balance|withdraw|deposit|position|transfer|settle|fund|asset` → C / I / D / S
- 新增或修改 HTTP / RPC handler → A / V
- 新增 migration / DDL → R / D
- 改了 `auth|permission|role|token` → A
- 改了 cache / outbox / 队列消费 → I + 一致性维度

## 范围

- 默认基线 ladder：`origin/main` → `origin/master` → `origin/develop` → `HEAD~1`
- 三路合并取改动：已 commit + 已 stage + 工作区未 stage
- 仅注释 / 格式化 / 改名 → 直接 `verdict: skip`

## 输出

- **Markdown 报告**：按改动函数分节，每节列适用维度 + 可执行动作 + must/should/nice
- **影响半径段**：grep 反查到的调用方 / SQL / route 消费方
- **盲区段**：明确列出 skill 看不见的影响面
- **末尾 YAML**（`<SELF_TEST_YAML_START>` / `<SELF_TEST_YAML_END>`）：must 清单 + verdict（`must_complete` / `skip` / `framework_missing`）

## 不做

- 不跑测试（开发自己跑）
- 不改源文件
- 不顺手「优化」无关代码
- 不替开发 push / PR

## 与 QA 流水线

本 skill 跑在**开发本地**，是 QA 流水线的前置防线，不替代 `prd-reviewer` / `test-point-generator` / `case-expander`。

## Version

- v1.0 (2026-05-07) — 重写为原则驱动版。从 v0.2 的 300 行精简到 60 行，相信模型。
