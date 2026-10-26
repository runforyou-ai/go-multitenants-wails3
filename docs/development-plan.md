# 开发与 PR 计划

把脚手架从「仅有规格」推进到「可作为 Template 使用」。每项只写**目标与大致范围**；常识性细节不必在此展开。

能力见 [capabilities.md](./capabilities.md)、[clients.md](./clients.md)。测试以 **Postgres 集成测试** 为主（无 IO 纯函数单测可以有）。

---

## 总览

```text
PR0  规格入库
PR1  仓库骨架与工具链（含健康检查占位）
PR2  配置加载（env / yaml）
PR3  数据模型与迁移（含禁用/停用、API 凭证、2FA、Webhook 等）
PR4  平台 Cookie 认证 + 安装引导
PR5  租户 JWT（refresh / 滑动续期）+ 开户
PR6  平台 / 租户 RBAC
PR7  ops-web 空壳
PR8  tenant-web 空壳（同一 Vite，供 Wails 复用）
PR9  代登录 + 审计 + 敏感操作二次验证
PR10 租户 API 凭证
PR11 2FA（完整）
PR12 Webhook 出站（完整）
PR13 cache + jobq
PR14 邮件（找回密码等）
PR15 对象存储 + 分片上传
PR16 Docker 部署
PR17 Wails 桌面一期（打包写入默认服；开源换服随 edition）
PR18 移动端一期；推送仅商业 edition
PR19 客户端更新 / 签名 / 分发
PR20 开源 · 商业 edition 门闸
PR21 模板打磨（含 TipTap demo 等）
```

可并行：PR7 ∥ PR8；PR11 ∥ PR12；PR13 ∥ PR14；客户端在 JWT 与 tenant-web 就绪后推进。

---

## PR0 — 规格入库

`docs/`、`AGENTS.md`、根 README 合入。

**状态：** 进行中。

---

## PR1 — 仓库骨架与工具链

空服务、Task / pnpm、基础 CI、`.env.example`；健康检查可先占位。

---

## PR2 — 配置加载

开发 env 为主、生产 yaml 为主、环境变量覆盖。

---

## PR3 — 数据模型

User / Tenant / 平台授权 / 租户成员；用户禁用、租户停用；API 凭证、2FA、Webhook 等相关模型。

---

## PR4 — 平台 Cookie 与安装

首装、平台管理员、登录 / 登出 / me。

---

## PR5 — 租户 JWT 与开户

access + refresh（或等效）；**滑动窗口续期**。代建、可选注册；禁用/停用/改密后旧凭证失效。

---

## PR6 — RBAC

平台与租户两套角色命名空间下的门闸。

---

## PR7 — ops-web 空壳

Refine 运营壳：登录、布局、i18n。

---

## PR8 — tenant-web 空壳

租户壳 + JWT；**一个 Vite 应用**，供 Web 与 Wails 加载。

---

## PR9 — 代登录、审计、二次验证

运营代登录（临时令牌）；审计；敏感操作二次验证。

---

## PR10 — 租户 API 凭证

租户级对外调用凭证的管理与校验（与登录分离）。

---

## PR11 — 2FA

完整双因素认证（绑定、校验、关闭等），不是空槽位。

---

## PR12 — Webhook 出站

完整出站 Webhook（配置、投递、重试/记录等按常规做法），不是空槽位。

---

## PR13 — cache 与 jobq

Postgres 驱动接入；可演示的异步/定时任务。

---

## PR14 — 邮件

部署级邮件；找回密码等。

---

## PR15 — 对象存储与分片上传

上传协议 + Uppy。

---

## PR16 — Docker

镜像与 Compose（或等效）。

---

## PR17 — 桌面一期

可构建；打包写入默认 API 地址；[clients.md](./clients.md) 一期桌面能力。开源换服随 edition 门闸，细节实现时定。

---

## PR18 — 移动一期与商业推送

移动一期能力；**推送仅商业 edition**。

---

## PR19 — 更新、签名与分发

清单、签名、分发页。

---

## PR20 — edition 门闸

开源默认不含商业模块与推送；开源客户端可换服；非开源客户端写死默认服。

---

## PR21 — 模板打磨

换皮说明、检查清单；TipTap 富文本 demo 等。

---

## 约定

1. 单 PR 主题单一；先合依赖方。  
2. 合入 `main` 的迁移不改写历史。  
3. 不做页面 DOM / 文案探测类测试。  
4. 不含迁入其它业务仓，不含具体业务域产品。  

---

## 里程碑

| 里程碑 | PR | 含义 |
|--------|-----|------|
| M0 规格 | PR0 | 可按文档开工 |
| M1 身份与治理 API | PR1–PR6、PR10–PR12 | 登录、RBAC、凭证、2FA、Webhook 等 |
| M2 双 Web | PR7–PR9 | 运营 / 租户浏览器可用 |
| M3 平台能力 | PR13–PR16 | 任务、邮件、上传、Docker |
| M4 客户端一期 | PR17–PR19 | 桌面/移动一期 + 更新分发 |
| M5 Template | PR20–PR21 | edition + 打磨 |
