---
name: example-skill
description: >-
  在需要输出前后端联调说明、整理接口契约，或核对 METHOD/路径/入参/响应 data 时使用。
  Use when the user asks for API contract notes, integration docs, or 联调说明.

---

# Example Skill · API 联调说明

## Instructions

1. 以仓库内真实 Controller / DTO / VO 为准，**禁止编造**路径与字段。
2. 不确定时先搜索注解（`@RequestMapping`、`@GetMapping` 等），再写文档。
3. 输出保持简短，字段名与项目真实序列化一致（如 `code` / `data` / `msg`）。

## 输出模板

```text
接口：METHOD /完整路径
鉴权：Header: Authorization: Bearer <token>（与同模块一致即可一行带过）
Content-Type：application/json | multipart/form-data

请求参数（Body / Query / Path / 表单）：
- 字段：类型（必填/选填），枚举或日期格式一行说清

成功响应示例：
{ "code": 200, "data": { ... } }

失败：
- 与全局异常处理一致（HTTP 状态 + 业务 msg）
```

## Do / Don't

- Do：对齐同模块最近一个相似接口的分页与命名习惯  
- Don't：把内部 Entity 字段原样当成对外契约（除非现网已如此）  
- Don't：在未读代码时「猜」网关前缀或鉴权头  
