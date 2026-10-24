# 软件开发中的 AI 范式与 Agent Skills

过去两年，编程助手从「补全一行」变成能读仓库、改多文件、跑命令的 **Agent**。模型在涨，但团队差距更常出在另一件事：**有没有把「在这个项目里怎么干活」写成 Agent 能稳定执行的配置**。

这篇只把范式说清楚：AGENTS.md、Rules、Skills 各管什么；什么时候用模式 A、什么时候用模式 B；文末给最小落地清单和一份可改的 `SKILL.md`。工具路径以 2026 年公开文档为准。

配套：桌面 `Gardner-AI-Skill`（Java 团队定制）可对照着改，不展开成第二篇文章。

---

## 1. 先分清：不是提示词技巧，是仓库级工程

| 层级       | 人在做什么                    | Agent 在做什么        |
| ---------- | ----------------------------- | --------------------- |
| 对话层     | 提需求、审 Diff               | 生成补丁、解释        |
| 仓库配置层 | 写 AGENTS.md / Rules / Skills | 按规范改，少跑偏      |
| 工具层     | 接 MCP、CI、Issue             | 查文档、跑测试、提 PR |

社区把「模型周围那一整套」叫 **Harness Engineering**：不只往上下文塞文档，还包括脚本、子 Agent、外部集成。论文扫了约 2800+ 仓库（[Harness Engineering for Agentic AI Coding Tools](https://arxiv.org/html/2602.14690)）：多数先有 Context Files；**AGENTS.md** 正成跨工具约定；Skills / Subagents 采用率仍低，且 Skills 多数还是静态说明。

**顺序别反着来：**

1. 项目说明书 → **AGENTS.md / Rules**  
2. 专项能力包 → **Agent Skills（SKILL.md）**  
3. 要连外部系统再上 → **MCP / CLI / CI**  
4. 全部进 Git，和代码一起评审  

---

## 2. 范式一：AGENTS.md——给 Agent 的 README

### 2.1 是什么

[AGENTS.md](https://agents.md/) 是开放 Markdown 约定：**专门写给编码 Agent 的项目说明**，和给人看的 `README.md` 分开。

- 规范说明：https://agents.md/  
- 仓库：[agentsmd/agents.md](https://github.com/agentsmd/agents.md/)

动机很直接：README 要短、给人看；Agent 需要**可执行事实**——怎么装依赖、怎么跑单测、哪些目录不能动、安全红线。固定文件名，少让每个工具各搞一套。

### 2.2 建议写什么（好 vs 差）

像「新同事第一天备忘录」：

| 区块 | 好例子                                           | 差例子           |
| ---- | ------------------------------------------------ | ---------------- |
| 命令 | `mvn -pl recycle-web -am compile -DskipTests`    | 「记得编译一下」 |
| 目录 | `*-web` 放 Controller/Biz；`*-service` 放 Mapper | 大段架构史       |
| 约定 | 「Controller 只注入 Biz*Service」                | 「注意代码质量」 |
| 边界 | 「禁止改生产密钥；动结算金额先说明」             | 「小心一点」     |
| 测试 | 「修 Bug 尽量补回归点」                          | 「要有测试」     |

微软技能库里有「生成 AGENTS.md」的 Skill，强调：**已有文件别覆盖、只补缺失、命令必须来自真实仓库**——[microsoft/skills · wiki-agents-md](https://github.com/microsoft/skills/blob/main/.github/plugins/deep-wiki/skills/wiki-agents-md/SKILL.md)。

### 2.3 大仓可以嵌套

Monorepo 子包再放 `AGENTS.md`，**就近优先**。根写全局命令与红线；子包只写差异（前端 `pnpm test` vs 后端 `mvn test`）。

### 2.4 和 CLAUDE.md / .cursorrules 的关系

| 文件                          | 典型工具          | 角色                 |
| ----------------------------- | ----------------- | -------------------- |
| `AGENTS.md`                   | Codex、多工具互通 | 跨工具说明书         |
| `CLAUDE.md` / `.claude/rules` | Claude Code       | Anthropic 常驻记忆   |
| `.cursor/rules/*.mdc`         | Cursor            | Always / Glob / 手动 |
| `.windsurf/rules`             | Windsurf          | Cascade 规则         |

**实践：** 团队规范的「真理」只写一份（建议以 AGENTS.md 为准），再按工具做薄封装，避免三份互相打架。

---

## 3. 范式二：Agent Skills——按需加载的能力包

### 3.1 开放标准

**Agent Skills** 把「某一类任务怎么做」打成可移植、可版本管理的目录。Cursor / Copilot / VS Code / Claude 文档都在对齐。

- Cursor：[Agent Skills](https://cursor.com/docs/skills)  
- GitHub：[About agent skills](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills)  
- VS Code：[Use Agent Skills](https://code.visualstudio.com/docs/agent-customization/agent-skills)

### 3.2 目录长什么样

```text
my-skill/
├── SKILL.md          # 必需：YAML frontmatter + 正文
├── scripts/          # 可选：可执行脚本
├── references/       # 可选：按需长文档
└── assets/           # 可选：模板、样例
```

最小 `SKILL.md`：

```markdown
---
name: my-skill
description: 做什么、何时用。Agent 靠这段决定是否加载。
---

# 标题

## When to Use
- …

## Instructions
- 步骤化、可检查的规则
```

规范建议：`SKILL.md` 控制在约 **500 行**内，细节进 `references/`，别一次性撑爆上下文。

### 3.3 渐进披露（为什么 Skills 比巨型 Rules 省）

1. 启动时 Agent 只看到各 Skill 的 **name + description**  
2. 任务匹配才加载完整 `SKILL.md`  
3. 需要时再读 `references/`、跑 `scripts/`  

所以可以装很多 Skill，不必每个对话都吃完全部说明。

### 3.4 常见发现路径

| 工具           | 项目级 Skills 目录（常见）                                   |
| -------------- | ------------------------------------------------------------ |
| Cursor         | `.cursor/skills/`、`.agents/skills/`（也兼容 `.claude/skills`、`.codex/skills`） |
| Claude Code    | `.claude/skills/`、`.agents/skills/`                         |
| Windsurf       | `.agents/skills/`                                            |
| GitHub Copilot | `.github/skills/`、`.agents/skills/`、`.claude/skills/` 等   |

**想一份配置多工具共用：** 优先放 **`.agents/skills/`**。Cursor 对话里可用 `/技能名` 手动触发。

---

## 4. 模式 A / 模式 B：Rules vs Skills

社区说得很直白（如 [GuildSkills: skills vs rules](https://guildskills.com/learn/skills-vs-rules/)）：

|            | **模式 A：Rules / AGENTS 常驻**   | **模式 B：Skills**               |
| ---------- | --------------------------------- | -------------------------------- |
| 加载时机   | 几乎每次相关对话                  | 任务匹配或手动 `/`               |
| 适合       | 栈、分层、禁止事项、租户/安全红线 | 发版流程、联调文档模板、专项排障 |
| 上下文成本 | 一直占额度                        | 平时只占 description             |
| 反模式     | 很少用的长流程写成 Always Rule    | 「每条请求都要遵守」只写成 Skill |

**一句话：永远为真 → 模式 A；偶尔才做的专业流程 → 模式 B。**

Cursor 对照：[Customizing agents](https://cursor.com/learn/customizing-agents)。

---

## 5. 更大图景：Harness 里还有什么

| 机制                   | 作用                              | 备注                    |
| ---------------------- | --------------------------------- | ----------------------- |
| **MCP**                | 接浏览器、Issue、库文档、内部 API | 工具层                  |
| **Subagents**          | 审查、调研、实现分工              | 采用率仍低于 AGENTS.md  |
| **Hooks / 命令白名单** | 限制危险操作                      | 偏企业管控              |
| **CI 门禁**            | 编译、单测、静态检查              | Agent「做完」的客观标准 |

论文观察：多数仓库还停在「只放 Context Files」。先把 AGENTS.md 写好，投入产出比最高。

---

## 6. GitHub 资源精选

### 6.1 规范与官方

| 资源                        | 链接                                                         | 用途              |
| --------------------------- | ------------------------------------------------------------ | ----------------- |
| AGENTS.md 官网              | https://agents.md/                                           | 格式与动机        |
| agentsmd/agents.md          | https://github.com/agentsmd/agents.md/                       | 规范与示例        |
| Cursor Agent Skills         | https://cursor.com/docs/skills                               | 目录、frontmatter |
| GitHub Copilot Agent Skills | https://docs.github.com/en/copilot/concepts/agents/about-agent-skills | Copilot 侧        |
| VS Code Agent Skills        | https://code.visualstudio.com/docs/agent-customization/agent-skills | VS Code           |
| Harness 论文                | https://arxiv.org/html/2602.14690                            | 采用情况实证      |

### 6.2 Awesome / 集合

| 资源                           | 链接                                                | 用途                |
| ------------------------------ | --------------------------------------------------- | ------------------- |
| VoltAgent awesome-agent-skills | https://github.com/VoltAgent/awesome-claude-skills/ | Skills 索引         |
| microsoft/skills               | https://github.com/microsoft/skills                 | 含 AGENTS.md 生成等 |
| PatrickJS/awesome-cursorrules  | https://github.com/PatrickJS/awesome-cursorrules    | 早期 Rules 写法参考 |

### 6.3 Java 后端可作模板

| 资源                          | 链接                                             | 用途                                               |
| ----------------------------- | ------------------------------------------------ | -------------------------------------------------- |
| yandong2023/java-backend-team | https://github.com/yandong2023/java-backend-team | 通用 Java AGENTS + Skill：最小变更、分层、审查清单 |

Spring / MyBatis / 多模块团队：Fork 后改成自己的分层名（如 `Biz*Service`、`ResultModel`），再放进公司 Git。

---

## 7. 团队落地：四周最小方案

不必一次上齐 Subagent + MCP。

### 第 1 周：AGENTS.md

业务主仓根目录至少写：

1. 项目一句话 + 模块地图  
2. 安装 / 编译 / 单测的**真实命令**（从本仓抄，别编）  
3. 分层与命名（贴你们代码）  
4. Never do（密钥、生产库、无审破坏性命令）  

### 第 2 周：1～3 个 Skills

优先「高频且易跑偏」：

- `java-api-change`：改接口必须出联调说明  
- `bugfix-min-patch`：先定位再最小修复  
- `release-checklist`：发版前命令清单  

每个 Skill：`description` 写清触发词；正文用检查清单，少空话。

### 第 3 周：Rules 收敛

「每次都要」的条款进 Cursor `.cursor/rules` 或 Claude `CLAUDE.md`，与 AGENTS.md 去重。

### 第 4 周：评审机制

- Skill / AGENTS 变更走 PR  
- 每周挑 1 个 Agent 翻车案例，回写到 Skill  

### 安全红线（必须写进 AGENTS）

- 禁止提交密钥、Cookie、生产连接串  
- 禁止未确认的 `push --force`、删库、改生产配置  
- 支付 / 开票 / 租户等高风险域，改前先说明  

---

## 8. 实操：一份最小 SKILL.md

```markdown
---
name: api-contract-note
description: >
  当新增或修改对外 HTTP 接口、VO、路径时使用。
  用于输出前端联调说明，避免只改代码不给契约。
---

# API 契约说明

## Instructions

1. 以 Controller 注解与 VO 为准，禁止编造字段。
2. 同一次回复给出联调块：METHOD、路径、鉴权、入参、成功 data、失败 msg。
3. 分页字段与同模块现有接口对齐。
4. 未改对外契约则明确写「无联调变更」。
```

保存路径：

```text
.agents/skills/api-contract-note/SKILL.md
```

---

## 9. 自检清单（发文前 / 落地前）

发 CSDN 或往业务仓塞配置前，过一遍：

- [ ] AGENTS.md 里的编译 / 测试命令，本机真实跑通过  
- [ ] 「永远为真」的条款在模式 A，不在巨型 Skill 里躺着  
- [ ] 联调 / 发版 / 排障类流程做成模式 B Skill，`description` 能被触发词命中  
- [ ] Skills 优先放 `.agents/skills/`，多工具能共用  
- [ ] 密钥、生产操作写进 Never do  
- [ ] 翻车案例有没有回写进 Skill（没有就下周补）  

本周就能做的三步：业务仓加 AGENTS.md → 加 1 个联调类 Skill → 放进 `.agents/skills/`。

---

## 10. 小结

1. AI 编程的差距，正在从「谁模型新」转向「谁把仓库配成 Agent 友好」。  
2. AGENTS.md = 常驻说明书；Skills = 按需手册；MCP = 外接工具箱。  
3. 永远为真 → 模式 A；偶尔才用 → 模式 B。  
4. 从 GitHub 抄结构，从自己仓库抄命令与分层——不要反着来。  
5. 配置也要 PR：翻车回写 Skill，比再开一个会员更实在。  

---

## 参考链接

- https://agents.md/  
- https://github.com/agentsmd/agents.md/  
- https://cursor.com/docs/skills  
- https://docs.github.com/en/copilot/concepts/agents/about-agent-skills  
- https://code.visualstudio.com/docs/agent-customization/agent-skills  
- https://arxiv.org/html/2602.14690  
- https://github.com/VoltAgent/awesome-claude-skills/  
- https://github.com/microsoft/skills  
- https://github.com/yandong2023/java-backend-team  
- https://github.com/PatrickJS/awesome-cursorrules  
- https://guildskills.com/learn/skills-vs-rules/  
- https://cursor.com/learn/customizing-agents  

---

*成稿：2026-10-24 · CSDN 发布用；工具路径变更以各官方文档为准。*
