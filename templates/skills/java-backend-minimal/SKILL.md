---
name: java-backend-minimal
description: >-
  通用 Java / Spring 后端改代码约定：分层、最小 Diff、统一返回与业务异常。
  Use when implementing or reviewing Spring Boot / microservice Java changes.

---

# Java Backend Minimal Skill

> 通用模板。复制到业务仓后，把包名、模块名、编译命令改成你的项目。

## When to use

- 新增 / 修改 Controller、编排 Service、持久化层
- Code review：分层、兼容性、高风险区
- 要求「对齐现有写法、少重构」时

## Layering（按你仓库改）

```text
Controller  → 编排层（Biz / Application Service）
编排层      → 领域/持久化 Service、Feign、工具
持久化      → Entity / Mapper / Repository
```

硬性建议：

- Controller 不写复杂业务、不直接注入 Mapper（除非仓库已有先例且需对齐）
- 返回统一响应体；业务失败用统一业务异常（中文 msg 贴项目语气）
- 分页、转换器用法与同模块最近接口一致

## Workflow

1. 先搜一条最接近的完整链路当蓝本  
2. 只改需求范围；禁止顺带重构无关文件  
3. 改完在相关模块编译（写真实命令，例如 `mvn -pl <module> -am compile -DskipTests`）  
4. 若动了对外 HTTP，按 `example-skill` 输出联调说明  

## High-risk（先说明再改）

- 鉴权 / 权限  
- 金额、结算、支付状态机  
- 多租户 / 数据权限  
- 对外 API 与消息兼容性  

## Checklist

- [ ] 对齐同类代码分层  
- [ ] 统一返回 / 异常  
- [ ] 无密钥进仓库  
- [ ] 对外接口有联调说明（若适用）  
- [ ] 编译或关键路径已验证  
