# WebEnvoy Vision

WebEnvoy 是面向矩阵社媒和多店铺运营的 Agent 原生多账号浏览器平台。它统一管理长期隔离的浏览器 Profile、设备环境、账号身份、经营对象、浏览器实例和网站 SKILL，让 Agent 与人类在同一真实浏览器现场中完成可观察、可接管、可核验的网站运营任务。

完整产品方向、五类决策状态、V1 约束和明确非目标见 [WebEnvoy v1 产品与架构方向规范](docs/product-architecture-v1.md)。本页只提供稳定入口，不复制规范内容或 GitHub 执行状态。

## 稳定边界

- `WebEnvoy/WebEnvoy` 是产品 monorepo，Core、Desktop App 与 Harbor 保持独立职责和 owner API。
- `WebEnvoy/Lode` 是独立的 SKILL、AccountSystem 模板和网站知识资产仓。
- App 与 Agent 使用同一 Runtime；App 是人类管理、观看、授权和接管入口，不建设完整 Agent 规划器。
- 所有正式 Profile 由 WebEnvoy 管理；Camoufox 是首个默认 Provider 验证目标，Chrome 是显式兼容 Provider。
- SKILL 决定建议怎样做，Runtime 决定实际提供什么，授权系统决定当前允许什么。
- WebEnvoy 不替用户决定运营策略，不承诺绕过风控、不可检测或不封号。

执行状态只存在于 GitHub 原生规划与交付对象中。
