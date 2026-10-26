# 文档索引

本目录定义 `go-multitenants-wails3` 作为公司多租户 SaaS 脚手架的**产品与架构规格**。实现细节（具体表名、库版本号等）可随代码演进，但不得违背此处原则。

## 建议阅读顺序

1. [architecture.md](./architecture.md) — 整体模块、依赖边界、客户端与双轨发行  
2. [identity-and-tenancy.md](./identity-and-tenancy.md) — 身份、租户、登录与权限  
3. [tech-stack.md](./tech-stack.md) — 技术栈冻结（含双前端）  
4. [capabilities.md](./capabilities.md) — 基础能力清单  
5. [clients.md](./clients.md) — 桌面 / 移动常用原生能力  
6. [development-plan.md](./development-plan.md) — 开发与 PR 计划（分批交付）  

## 实例关系（说明性）

基于本脚手架的产品实例（历史或规划）仅作对照，**不**要求本文规定迁移步骤：

- 带客户端的完整实例（类 luway-cloud）  
- 不发布客户端的服务端实例（类 ai-gateway）  

二者共用同一仓库能力；差异在业务域与**发版是否附带客户端产物**。
