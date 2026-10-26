# 技术栈冻结

框架层尽量固定，产品可换视觉风格与业务页面。未列出的选型实现阶段补充，但不得无故引入第二套平行栈。

## 1. 后端

| 项 | 选型 |
|----|------|
| 语言 | Go |
| 主库 | Postgres（外部组件仅此项） |
| 通用函数 | [`support`](https://github.com/runforyou-ai/support) |
| 缓存 | [`cache`](https://github.com/runforyou-ai/cache)，Postgres 驱动 |
| 异步 / 定时任务 | [`jobq`](https://github.com/runforyou-ai/jobq)，Postgres 驱动 |
| 桌面 / 移动壳 | Wails3 |

分层与工程约定以本仓库 `AGENTS.md` 为准。管理 API **不使用**契约生成器，HTTP 手写。

## 2. 运营后台（ops-web）

| 项 | 选型 |
|----|------|
| UI | shadcn + Tailwind CSS v4 + **Refine / refine-ui** |
| 定位 | 平台治理、CRUD、开户、代登录入口等 |

允许：主题、Logo、业务菜单与页面定制。  
不允许：另起一套与 Refine 并行的运营后台框架（除非整仓升级规格）。

## 3. 租户后台（tenant-web）

| 项 | 选型 |
|----|------|
| 原语 | shadcn（new-york）+ Radix + Tailwind CSS v4 |
| 数据 / 表单 | TanStack Query、TanStack Table、React Hook Form + Zod |
| 布局起点 | shadcn `sidebar-01`（`npx shadcn@latest add sidebar-01`） |
| 上传 | **Uppy** → 对象存储；**分片上传**（对齐同类产品实践即可） |
| 富文本 | **TipTap**（脚手架提供 demo 页示范接入；不要求流式渲染） |

**与 Wails：同一个 Vite 应用**（`apps/tenant-web`），浏览器部署与桌面/移动壳都加载这一份前端；用 `platform` 区分 `web | desktop | mobile`。不是两个独立 Vite 工程，也不是「共享 UI 包 + 两套页面入口」。

允许：视觉风格、业务页面。  
不允许：租户端再引入另一套完整后台框架替代上述原语栈。  
ops-web 与 tenant-web 仍是两个前端应用；二者之间最多共享设计 tokens 等薄层。

## 4. 上传与对象存储

- 文件上传：Uppy + 对象存储分片上传。  
- 对象存储 endpoint、桶、密钥等为部署配置，与 Postgres、邮件同类。  

## 5. 客户端（Wails3）

- 标准写法与签名发布见 Task / [clients.md](./clients.md)。  
- 加载 **tenant-web 同一 Vite 应用**；`platform` 区分环境。  
- 原生层不承载业务 API、不持久化账号令牌。  
- 打包写入默认 API 地址；开源 edition 客户端可换服，非开源写死默认服。  
- 移动推送：**仅商业 edition**；开源构建不含推送。  

## 6. 包管理

前端（ops-web、tenant-web、Wails 所载前端）统一使用 **pnpm**。

## 7. i18n

ops-web 与 tenant-web 均预留国际化（至少中英）。  
