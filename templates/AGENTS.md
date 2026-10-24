# AGENTS.md

> 复制到业务仓库根目录后，把占位符改成**真实**命令与路径。

## Project

- 一句话说明本仓库做什么（产品 / 服务边界）
- 主要技术栈：（例如 Java 17 + Spring Boot / Node + Nest / …）

## Commands

把下面换成你仓库里能跑通的命令：

```bash
# Install
# …

# Build
# …

# Test
# …

# Lint / Format
# …
```

## Architecture

用真实目录描述分层，例如 Java 微服务常见写法：

```text
*-web        → Controller、Biz*Service、vo、converter
*-service    → entity、Mapper、IService 实现
*-api/feign  → 对外 API 契约（若有）
```

规则示例（按你项目改）：

- Controller 只做入参校验与调用编排层，不写 SQL
- 编排层调用持久化 Service / Feign，不直接堆页面逻辑到 Mapper
- 对外返回用统一响应体；业务失败用统一业务异常

## Code style

- 对齐仓库内**已有同类代码**，禁止顺带大重构
- 命名、空值判断、日志风格与当前文件一致
- 新增依赖前先说明理由

## Security & Never

- 不提交密钥、`.env`、生产配置、真实客户数据
- 不 force-push `main` / `master`
- 未说明前不改：鉴权、金额/结算、权限、多租户隔离、对外 API 兼容性

## High-risk areas（动之前先说明）

- 登录 / Token / 权限
- 支付、结算、金额计算
- 数据隔离（租户 / 组织 / 数据权限）
- 网关路由、全局过滤器、公共 starter

## PR / 交付

- 说明改动范围与验证方式（编译 / 单测 / 手工接口）
- 若变更对外 HTTP：附简短联调说明（方法、路径、鉴权、入参、成功 `data`）
- 默认最小 Diff；正确性高于「看起来高级」

## When unsure

- 先搜索同类实现，再动手
- 需求有歧义时先问 1 个关键问题
