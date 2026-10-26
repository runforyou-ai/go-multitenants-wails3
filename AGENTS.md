# 开发约定

本文件约束 Agent 与人类贡献者的**实现方式**。产品形态与能力范围见 `docs/`，勿把产品说明写回本文件。

## 硬约束

- 公共纯函数优先 `github.com/runforyou-ai/support`。
- 业务与运行日志只用 `log/slog`；控制台按行纯文本。
- 外部组件仅依赖 **Postgres**。缓存用 `github.com/runforyou-ai/cache`、异步/定时任务用 `github.com/runforyou-ai/jobq`，均使用其 **Postgres 驱动**。
- 对象存储、邮件等通过**部署配置**（yaml / 环境）接入。
- 每表必须有 `created_at`、`updated_at`；列顺序紧跟主键（`id` 或 `name`）之后，并挂 `set_updated_at()` 触发器；model 字段顺序与迁移一致。
- 新迁移：`YYYYMMDDHHMM_描述.sql`；建表迁移带中文 `COMMENT`；不在迁移里堆业务外键 / `CHECK`（与公司现有库表习惯一致，除非本仓另有明文规定）。
- 与库或外部 IO 相关的行为用 **Postgres 集成测试**：放在 `internal/integrationtest/`（或按端划分的等效目录）；`TEST_POSTGRES_*`（至少 `HOST`），库名以 `_test` 结尾，未设则 Skip。无 IO 的纯函数允许表驱动单测。不写页面元素是否存在、文案是否渲染之类的前端/E2E 探测测试。
- Wails3 客户端工程与构建链路**必须保留在仓库中且可构建**；发版策略可不发布安装包，但不得从仓库拆除客户端能力。桌面/移动能力见 `docs/clients.md`；原生层不承载业务 API、不持久化账号令牌。
- 开源 / 商业双轨用构建标签或 `edition` / 目录隔离；商业代码不得无门闸泄漏进开源默认产物。**开源构建不含移动推送**（推送仅商业版）。客户端打包写入默认 API 地址；**开源版可换服**，非开源写死默认服。
- **不使用**管理 API 契约生成器（无 contractgen / 无生成路由源）；HTTP 手写。

## 分层

实现落地后目录名可微调，依赖方向必须清晰：

```
cmd/                         # 无业务
internal/direct/             # 调 actions、映射错误（若需要）
internal/api/                # HTTP 手写：路由、会话/JWT、上传等
internal/actions/<域>/       # 不得 import api/direct
internal/model/  internal/domain/
internal/integrationtest/
apps/ops-web/                # 运营后台
apps/tenant-web/             # 租户后台；Wails 壳加载同一前端工程（一个 Vite 应用）
```

- `model` / `domain` 不依赖上层。
- Action 不依赖传输层。
- 原生 Wails / `internal/native` 不承载业务 API、不持久化账号令牌。
- 平台运营身份与租户成员身份在数据上分离表达（用户表 + 平台授权 + 租户成员）；禁止用单一全局 `admin|user` 充当多租户权限模型。

## 配置边界

- 基础设施（监听、Postgres、日志、对象存储、邮件、商业版移动推送等）用部署配置表达；平台可运营项进平台 settings（或等效），租户可配置项进租户配置，二者勿混进基础设施 yaml。
- **开发阶段**以环境变量（如 `.env`）为主加载配置。
- **生产部署**以 yaml 配置文件为主；同名环境变量可覆盖 yaml 中的对应项。
- 配置加载顺序实现为：先读 yaml（若存在），再用环境变量覆盖；开发无 yaml 时允许仅环境变量启动。

## 认证（实现约束）

- 运营后台：Cookie 会话（Set-Cookie 等手写即可）。
- 租户 Web / 客户端：JWT Bearer；claim 须能确定用户与租户。须支持 **refresh（或等效）**，会话呈**滑动窗口**：在过期前有活动则续期，避免「固定绝对过期」导致临近过期一次活动只剩极短有效期。改密、禁用、停用租户后既有凭证失效。
- 租户对外业务 API：租户级 API 凭证，与上述登录凭证分离；禁止用平台 Cookie 或用户 JWT 冒充租户调用凭证。
- 鉴权须能区分平台与租户上下文，禁止只用含糊的 `user|admin` 表达多租户授权。

## HTTP API 形态

- 管理类成功：直接业务 JSON 或 204，不包 `{ data }`。
- 管理类失败：`{ "message": "...", "fields": { ... } }`（`fields` 可选）；无单独业务 `code`。
- 500 的 `message` 对前端固定通用文案，细节只打 slog。
- 对外业务协议（如 `/v1`）错误保持该协议原生形状。

## 前端

### 运营后台（ops-web）

- shadcn + Tailwind v4 + Refine / refine-ui；优先官方写法。
- **禁止自拟 UI 尺寸与样式**：Dialog / Sheet / 按钮 / 表单等用组件默认（勿随意加 `sm:max-w-md`、自定义高度、另搞一套间距体系）；仅官方文档或本仓已有先例才覆盖 className。

### 租户后台（tenant-web）

- shadcn（new-york）+ Radix + Tailwind v4；数据/表单用 TanStack Query/Table、RHF + Zod。
- 布局以 shadcn `sidebar-01` 为起点，勿另起平行布局体系。
- 上传用 Uppy，走对象存储分片上传；勿再引入第二套上传栈。
- 富文本编辑用 TipTap（脚手架以 demo 页示范接入）；勿平行引入第二套编辑器。不要求流式渲染能力。
- **与 Wails：同一个 Vite 前端工程**，Web 部署与桌面/移动壳加载同一应用；用 `platform` 区分环境。不要做成两个独立 Vite 应用，也不要「共享包 + 两套页面树」。

### 通用

- 包管理使用 **pnpm**（ops-web / tenant-web / 客户端所载前端均如此）。
- 运营端与租户端为两套应用；允许换主题与业务页，禁止擅自更换框架选型。
- i18n 中英文同步；用户可见文案进词条，禁止硬编码散落（演示除外）。

## 构建与任务

- 以本仓 Taskfile / `wails3 task`（或落地后的等价入口）为准；不要为同一件事维护互相漂移的多套脚本。
- 客户端构建使用平台 Task，架构通过 `ARCH` 等参数传递；Wails Go 模块、前端 runtime、CLI 版本锁定一致。

## 验证

- 业务与权限等行为用集成测试覆盖（真实 Postgres）。
- 原生能力在目标平台手工或平台任务验收。
