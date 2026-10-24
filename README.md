# AI Agent Skills Guide

软件开发中的 AI 范式：**AGENTS.md / Rules / Skills / MCP**，含模板、资源清单与团队落地步骤。

> 中文长文 → [`docs/ai-paradigm-zh.md`](./docs/ai-paradigm-zh.md)  
> 同步发文：CSDN（同文）  
> License：MIT

---

## 这仓库是干什么的

AI 写代码已从「聊天补全」变成「仓库级 Agent」。真正拉开差距的，往往不是模型本身，而是：

1. 有没有给 Agent 一份**项目说明书** → `AGENTS.md` / Rules  
2. 有没有把专项流程做成**按需技能包** → Agent Skills（`SKILL.md`）  
3. 需要连外部系统时再上 → MCP / CI  

本仓库用公开文档 + 最小模板，帮你把这套范式落到自己的业务仓。

---

## 仓库结构（建议）

```text
ai-agent-skills-guide/
├── README.md                 # 本文件
├── LICENSE
├── docs/
│   └── ai-paradigm-zh.md     # 完整中文长文（与 CSDN 同源）
├── templates/
│   ├── AGENTS.md             # 根目录说明书模板
│   └── skills/
│       └── example-skill/
│           └── SKILL.md      # 最小 Skill 示例
└── examples/                 # 可选：团队定制包
```

---

## 三分钟上手

1. 复制 [`templates/AGENTS.md`](./templates/AGENTS.md) 到你业务仓根目录，改成**真实**编译/测试命令。  
2. 复制 [`templates/skills/`](./templates/skills/) 到 `.agents/skills/`（Cursor / Claude / Copilot 等多工具可读）。  
3. 在 Cursor / Claude Code / Copilot 里让 Agent「按 AGENTS.md 改代码」。  

### 选型一句话

| 内容                     | 放哪里               |
| ------------------------ | -------------------- |
| 几乎每次都为真的规范     | Rules / AGENTS.md    |
| 偶尔才用的专业流程       | Skills（`SKILL.md`） |
| 查库、查文档、连外部系统 | MCP                  |

---

## 必藏链接

| 资源                     | 链接                                                |
| ------------------------ | --------------------------------------------------- |
| AGENTS.md 规范           | https://agents.md/                                  |
| agents.md 仓库           | https://github.com/agentsmd/agents.md/              |
| Cursor Skills 文档       | https://cursor.com/docs/skills                      |
| Awesome Claude Skills    | https://github.com/VoltAgent/awesome-claude-skills/ |
| Microsoft Skills         | https://github.com/microsoft/skills                 |
| Java 后端团队 Skill 示例 | https://github.com/yandong2023/java-backend-team    |
| Harness Engineering 论文 | https://arxiv.org/html/2602.14690                   |

---

## 模板预览

### `templates/AGENTS.md`（粘贴到业务仓根目录后改）

```markdown
# AGENTS.md

## Project
- 一句话说明本仓库做什么

## Commands
- Install: …
- Build: …
- Test: …
- Lint: …

## Architecture
- 模块 / 分层约定（写真实路径）

## Code style
- 与仓库现有风格一致的硬性规则

## Never
- 提交密钥 / .env
- 强推 main/master
- 未说明就改结算金额、权限、生产配置

## PR
- 先说明改动范围与验证方式
```

### 最小 Skill（`templates/skills/example-skill/SKILL.md`）

```markdown
---
name: example-skill
description: >-
  在需要输出前后端联调说明、或整理接口契约时使用。
  Use when the user asks for API contract notes or 联调说明.
---

# Example Skill

## Instructions
1. 以仓库内真实 Controller / VO 为准，禁止编造路径。
2. 输出：METHOD、完整路径、鉴权、入参、成功 `data` 形态。
3. 保持简短，字段与项目序列化一致。
```

---

## 团队四周 MVP（可照抄）

| 周   | 做什么                                     |
| ---- | ------------------------------------------ |
| W1   | 业务仓加 `AGENTS.md`（命令必须能跑通）     |
| W2   | 加 1 个高频 Skill（如「接口联调说明」）    |
| W3   | 统一放到 `.agents/skills/`，全员同一套     |
| W4   | 复盘：哪些 Rules 太长、哪些 Skill 从未触发 |

---

## 贡献 / 使用

- 欢迎 Issue / PR：补链接、补模板、修过时路径。  
- 第三方仓库版权归原作者；本仓模板默认 **MIT**。  
- 长文细节与对照表见：[docs/ai-paradigm-zh.md](./docs/ai-paradigm-zh.md)

---

## Star / 互链

若本文对你有用，欢迎 Star。CSDN 发文时可互链：

```text
https://github.com/chenruijieCSDN/ai-agent-skills-guide
```
