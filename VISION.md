# WebEnvoy Vision

WebEnvoy 是面向第三方 Agent 和上游自动化系统的浏览器基础设施。它以长期受管身份、隔离环境和可验证的隐身质量为基础，提供浏览器管理、页面语义、受控视觉与网络操作、人工介入、结果核验和恢复，并通过站点 SKILL 复用知识与确定性执行。

完整产品方向、五类决策状态、V1 约束和明确非目标见 [WebEnvoy v1 产品与架构方向规范](docs/product-architecture-v1.md)。本页只提供稳定入口，不复制规范内容或 GitHub 执行状态。

## 稳定边界

- `WebEnvoy/WebEnvoy` 是产品 monorepo，Core、Desktop App 与 Harbor 保持独立职责和 owner API。
- `WebEnvoy/Lode` 是独立的 SKILL、AccountSystem 模板和网站知识资产仓。
- CLI、API、Plugin 与可信用户入口使用同一 Runtime、授权、Run 和结果；Desktop App 专属工作台、布局与独立发行产品化冻结。
- 冻结 App 不冻结人类授权、监督、接管、交还、撤权、停止和恢复；这些能力必须有不依赖 App 的正式路径。
- 所有正式 Profile 由 WebEnvoy 管理；Provider 由用户选择，产品推荐、用户新建默认和 Profile 实际绑定相互分离。Provider 先通过 canonical Qualification Gate；WebEnvoy 不实现或长期补偿缺失的浏览器核心语义。Obscura 在当前愿景完成前明确不采用。
- 站点 SKILL 统一承载 references、确定性 scripts、assets、任务分流、输出验证与修复；正式支持按具体可执行任务声明。
- 上游负责经营策略、内容排期与跨系统编排；WebEnvoy 负责正确身份、受控操作、真实结果与恢复，不承诺绕过风控、不可检测或不封号。

执行状态只存在于 GitHub 原生规划与交付对象中。
