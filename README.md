# go-multitenants-wails3

公司内部 **Go + React 多租户 SaaS 脚手架**（基于 Wails3 扩展）。目标是多数产品共用同一套身份、双工作台、平台能力与可选客户端交付；具体业务（网关、协作等）在租户配置域上扩展。

> 仓库由规格起步，实现按文档迭代。权威约定见 `docs/`。

## 文档

| 文档 | 内容 |
|------|------|
| [docs/README.md](./docs/README.md) | 文档索引与阅读顺序 |
| [docs/architecture.md](./docs/architecture.md) | 模块划分、依赖、客户端、开源/商业 |
| [docs/identity-and-tenancy.md](./docs/identity-and-tenancy.md) | 租户、用户、登录、RBAC、开户 |
| [docs/tech-stack.md](./docs/tech-stack.md) | 后端与双前端技术栈冻结 |
| [docs/capabilities.md](./docs/capabilities.md) | 基础能力清单 |
| [docs/clients.md](./docs/clients.md) | 桌面 / 移动常用原生能力 |
| [docs/development-plan.md](./docs/development-plan.md) | 开发与 PR 计划 |

## 定位（一句话）

- **运营后台**与**租户后台**框架固定，产品可换风格与业务页。  
- **Wails3 客户端能力始终保留在仓库与构建链路中**；发版可不发布安装包。  
- **外部组件仅依赖 Postgres**（缓存、任务队列使用 PG 驱动）。  

## 相关库

- [`support`](https://github.com/runforyou-ai/support) — 通用函数  
- [`cache`](https://github.com/runforyou-ai/cache) — 缓存（支持 Postgres）  
- [`jobq`](https://github.com/runforyou-ai/jobq) — 异步任务与定时任务（支持 Postgres）  

## 状态

规格与 PR 计划已定；实现按 [docs/development-plan.md](./docs/development-plan.md) 推进（当前为 PR0 文档阶段）。
