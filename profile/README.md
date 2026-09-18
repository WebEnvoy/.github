# WebEnvoy

产品方向、V1 约束与决策状态以 [WebEnvoy v1 产品与架构方向规范](../docs/product-architecture-v1.md) 为准。

> **Canonical product entry**：产品代码、PR 与新的执行 Work Item 统一进入 [`WebEnvoy/WebEnvoy`](https://github.com/WebEnvoy/WebEnvoy) 的 `apps/desktop`、`packages/*` 与 `services/harbor` 模块。`WebEnvoy/App` 与 `WebEnvoy/Harbor` 仍保留历史、backlog locator 和 rollback，未归档且不接受新的实现；canonical development entry 已切换，#10 只对 legacy source issue/Project 的 transfer、close、archive/retirement 要求 explicit confirmation。Core、App、Harbor 保持独立进程和 owner API；Harbor 独占 credential、cookie、token、profile、raw DOM、HAR、screenshot，Lode/站点策略/结果归一化不进入 Harbor，Lode 继续独立 MIT version/hash pin。

真实网站不应该只靠 Agent 现场摸索，也不应该只靠一堆随时失效的浏览器脚本。

WebEnvoy 把长期浏览器身份、页面语义、受控视觉与网络操作、站点知识、确定性执行、结果核验和恢复组织成可集成能力，让第三方 Agent、上游系统和可信用户共享同一真实现场与结果。

## 核心方向

完整组织级愿景见 [WebEnvoy Vision](https://github.com/WebEnvoy/.github/blob/main/VISION.md)。

WebEnvoy 重点解决真实网站能力使用中的几个关键问题：

- 通过持续浏览器身份和环境一致性，降低登录异常、验证码和访问受限等风险；
- 把网页现场压缩成低噪音上下文，减少截图、DOM、日志和重复探索带来的成本；
- 将网站入口、页面状态、操作路径、资源需求和验证逻辑沉淀为可复用能力资产；
- 将采集、发布、修改、删除和提交等读写结果归一成可校验、可追溯、可消费的公共结果；
- 让每次任务具备运行记录、证据引用、失败归因、人工接管和后续对账能力；
- 通过正式 CLI、API 和已安装 Plugin 使用同一能力，不要求 Desktop App 承载运行前提。

## 核心仓库

- [`App`](https://github.com/WebEnvoy/App)：legacy source/history/backlog/rollback locator；active App 模块位于 [`WebEnvoy/WebEnvoy/apps/desktop`](https://github.com/WebEnvoy/WebEnvoy/tree/main/apps/desktop)。现有代码与历史设计保留，App 专属工作台、布局和独立发行产品化冻结。
- [`WebEnvoy`](https://github.com/WebEnvoy/WebEnvoy)：canonical 产品 monorepo；Core 模块负责 API Server、Core Runtime、能力准入、资源匹配、Run Record、结果封装和失败归因。
- [`Harbor`](https://github.com/WebEnvoy/Harbor)：legacy source/history/backlog/rollback locator；active Harbor 模块位于 [`WebEnvoy/WebEnvoy/services/harbor`](https://github.com/WebEnvoy/WebEnvoy/tree/main/services/harbor)，负责 Profile、执行身份、Runtime Session、Viewer、人工接管、provider 能力事实和运行证据。
- [`Lode`](https://github.com/WebEnvoy/Lode)：站点 SKILL 与共享定义资产库，负责 references、确定性 scripts、assets、任务声明、输出契约、验证材料、来源和版本。
- [`.github`](https://github.com/WebEnvoy/.github)：组织级文档、仓库地图、许可证边界、issue / PR 模板和协作规则。

## 文档

- [仓库地图](https://github.com/WebEnvoy/.github/blob/main/docs/repository-map.md)
- [许可证边界](https://github.com/WebEnvoy/.github/blob/main/docs/licensing.md)
