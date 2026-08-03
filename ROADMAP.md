# WebEnvoy 路线图

本文是 WebEnvoy 组织级产品路线图。历史实现和 source facts 仍分布在 Core、App、Harbor、Lode 四个产品仓库；canonical 新开发入口是 `WebEnvoy/WebEnvoy` 中的 Core、App、Harbor 三个模块，加上独立的 Lode（3+1）。本文只描述目标状态、阶段阶梯和迁移约束，不维护当前活跃 issue、Work Item、PR 或执行看板。

当前执行状态以 GitHub Milestones、Project、issues 和 PR 为准。GitHub 真相切换的 dry-run inventory、逐项 legacy mapping 和证据快照见 [`docs/github-truth-cutover.md`](docs/github-truth-cutover.md)；该文档不替代 GitHub native readback。

## 组织拓扑与迁移状态

canonical 产品代码、PR 和新的执行 Work Item 入口已经切换到 `WebEnvoy/WebEnvoy` 的 Core、App、Harbor 模块；`WebEnvoy/App` 与 `WebEnvoy/Harbor` 仍保留可读、未归档的 legacy source/backlog/rollback locator，不接受新实现，也不因本批次文档发生 transfer、close 或 archive。目标状态是一个模块化产品 monorepo：Core、App、Harbor 仍保持独立模块、独立进程和 owner API；Lode 继续作为独立 MIT 仓库，通过 version/hash pin 和 compatibility CI 被消费，不使用 git submodule，也不再拆 sites。

文档规划不等于源码历史导入、workspace、CI、Desktop 装配或运行时验收完成。合同与所有权、历史、clean-checkout、运行时装配、数据/许可证、GitHub mapping 和回滚门继续约束后续实现与 source-object 退役；任一门失败、事实不可用或运行验证不稳定，都暂停剩余迁移并保留 legacy locator 与 rollback evidence。canonical development entry 已切换完成，除非 owner 明确批准 rollback，不回退为旧仓默认入口。详细当前/目标映射和证据要求见[组织级仓库地图](docs/repository-map.md)，许可证边界见[组织级许可证文档](docs/licensing.md)，GitHub 执行映射见[Issue 与 Project 管理规范](docs/issue-project-management.md)。

旧 App/Harbor source issue、Project 的 transfer、close、archive/retirement 仍受 [WebEnvoy/.github#10](https://github.com/WebEnvoy/.github/issues/10) 的 explicit-confirmation gate 约束：必须先有 W9/W10 的 release/readback/rollback、snapshot、source mapping、repo flags 和 owner 明确确认；canonical development entry 已切换，本路线图和本批次文档不执行 transfer、close、archive、delete 或默认入口设置变更。

## 目标状态

WebEnvoy 的目标是让用户、Agent、自动化程序和上游系统通过统一任务入口复用稳定的网站能力，并让每次网页操作任务都可准入、可执行、可记录、可验证、可恢复、可对账。

用户和上游系统最终不需要理解 3+1 内部结构，而是能完成这些工作：

- 在 Work 中提交网站任务、查看运行状态、读取结构化结果、打开证据和处理异常。
- 在 Library 中找到、安装、锁定、更新、上报和修复网站能力。
- 在 Browser 中管理 Profile、Runtime Session、Viewer、接管和 provider 状态。
- 通过 API、CLI、MCP 和 SDK 获得与 App 一致的任务结果、失败原因、证据引用和恢复入口。

为支撑这些产品能力，目标状态包括：

- `WebEnvoy/WebEnvoy` 目标 monorepo 中的 Core 模块提供统一 API Server、Core Runtime、Run Record、Result Envelope、Admission 和统一授权策略合同。
- `WebEnvoy/WebEnvoy` 目标 monorepo 中的 Harbor 模块提供 Profile、Execution Identity、Runtime Session、Provider facts、Snapshot、RefMap、Evidence refs 和 Viewer / handoff facts。
- `WebEnvoy/Lode` 继续提供 capability package、workflow package、schema、fixtures、post-check、asset registry、版本和失效标记，并保持独立 MIT 仓库。
- `WebEnvoy/WebEnvoy` 目标 monorepo 中的 App 模块提供 Work、Library、Browser 三个用户表面，展示上游事实并发送用户意图，不复制上游 truth。

最终产品应满足：

- API、CLI、MCP、SDK 和 App 共用同一条 Core 任务路径。
- 任务围绕连续执行身份、环境一致性和风险状态准入；资源或状态不满足时可以停止、接管和记录。
- 页面现场优先压缩为低噪音结构化上下文、evidence refs 和 result envelope，不要求调用方默认消费完整 DOM、截图或原始请求响应。
- 账号、Cookie、token、完整 DOM、完整请求响应、用户业务内容和未脱敏执行现场默认不上传；平台资产与用户个人资产分离。
- Core 模块不直接拥有浏览器现场、站点知识或 App UI 状态。
- Harbor 模块不判断任务业务成功，不拥有 Lode schema 或 Core Run Record。
- Lode 不选择具体 Runtime Session，不保存真实账号状态或生产运行现场。
- App 模块不直接执行能力、不写 Run Record、不绕过 Harbor 操作 Runtime Session。

## 产品化原则

路线图按产品闭环推进，不按仓库或合同清单横向铺满。每个阶段都必须留下用户、Agent 或上游系统能感知的可用切片，即使范围很窄。

- 优先做纵向闭环：一个能力、一个运行身份、一次运行记录、一个结果封装、一个证据引用和一个用户入口，比同时定义所有合同更重要。
- 统一协议是早期骨架，不是后期集成任务；`task`、`run`、`result`、`evidence` 和 `action request` 的最小语义必须先被公共入口验证。
- App 不等到后期才出现；早期可以只读、只支持本地连接或只覆盖单个能力，但必须持续验证用户能不能理解任务、结果、证据和异常。
- 写侧能力必须先有最小 unknown outcome、证据引用和对账语义；完整恢复体验可以后置，安全事实不能后置。
- 首批能力选择低风险读能力或 validate-only 写能力，用来验证模型，不用复杂真实提交任务证明平台完整性。
- 已收敛研究成果要进入阶段完成标准；路线图不从零开始，也不把外部项目整体搬进 WebEnvoy。
- 生态扩展只由已经跑通的产品闭环拉动，不提前把 hosted runtime、marketplace、团队同步或 crawler 写成默认交付前提。
- App 的用户验收以能否完成工作、理解业务结果和处理异常为准，不以 runtime 状态、refs 或日志数量为准。
- Work、Browser、Library 是 App 的三个业务域；Settings 只承载偏好、全局授权默认值和诊断。
- App、CLI、MCP、API、SDK 和 Agent 共用 Core 任务与授权路径。授权采用全局默认、任务覆盖和单次确认，不在各入口或站点技能中复制确认流程；“审批”只保留给未来真实多人组织流程。
- Evidence、Run Record、Snapshot、RefMap 和 source trace 默认是后台运行记录；只有来源追溯、故障诊断或用户主动查看时才进入产品表面。
- Provider 管理必须覆盖从检测到安装、更新、修复和启动验证的恢复闭环，不能止于下载链接或错误文本。
- UI 实施前必须经过用户场景、对象模型、信息架构和低保真旅程验收；不得从临时 fixture 或技术状态字段反推最终界面。

## 阶段阶梯

阶段阶梯描述 WebEnvoy 从当前多仓过渡态到目标 3+1 拓扑的产品和架构成熟度。它不是 GitHub Milestone，也不指定近期 issue 顺序。每个阶段都应同时回答：用户能做什么、系统记录什么、模块与 Lode 的边界如何保持清楚。

### 阶段一：用户任务与吸收边界

首个真实用户任务、非目标、3+1 边界和研究吸收边界稳定下来，后续工作不会各自发明平行路线，也不会按空白项目从零铺平台。

达到该阶段时：

- 首个真实用户任务明确为低风险只读网站任务：在受控运行现场打开目标页面或账号环境，提取结构化信息，返回结果封装、证据引用和失败原因。
- 首个写侧边界明确为 validate-only、draft 或 preview，不默认真实提交；真实写入必须等待符合统一策略的明确授权，以及幂等、post-check、unknown outcome 和对账入口的最小语义成立。
- 跨仓 architecture 说明当前多仓到目标 3+1 的角色、truth source、数据流和禁止跨界。
- 首个端到端用户任务旅程说明 Work、Library、Browser、Core、Harbor 和 Lode 各自承担什么。
- 研究吸收边界明确：CloakBrowser provider baseline、Snapshot / RefMap、Run Record / evidence、capability package / schema / fixtures 和 handoff / recovery 作为机制吸收方向。
- 源码复用只允许裁剪小而清晰的模块或协议种子，例如旧 WebEnvoy、Syvert、OpenCLI、BrowserSkill 和 bb-browser；外部 UI shell、hosted 平台、browser binary 和 runtime 主链不整体搬迁。
- MVP 非目标明确：完整 hosted browser、marketplace、crawler queue、benchmark task contract、通用 browser agent loop 和反检测成功率承诺不进入早期产品合同。
- 方向性 ADR 记录关键取舍，pending decision 集中索引。
- 总 ROADMAP 和各仓 AGENTS 约束路线图、GitHub Milestone、功能需求和工作项的关系。
- 单仓文档不创建与总路线冲突的平行路线图。

### 阶段二：最小统一协议

WebEnvoy 先稳定最小公共协议骨架，让 App、API、CLI、MCP 和 SDK 不各自定义任务、运行、结果、证据和动作语义。

达到该阶段时：

- Core 的最小 `task`、`run`、`result`、`admission`、统一授权、状态机和 `evidence ref` 语义被定义为公共 spine，而不是一次性铺满全部字段。
- Harbor 的最小 `runtime facts`、`session ref`、`snapshot ref`、`evidence ref` 和 `viewer ref` 语义能被 Core 引用。
- Lode 的最小 `capability package`、`schema`、`fixtures`、`resource requirements` 和 `post-check` 语义能被 Core 准入和校验。
- 至少一个机器入口和一个用户入口消费同一套最小协议；入口可以很窄，但不能各自发明字段真相。
- App 早期只展示上游事实和发送用户意图，不复制 Core、Harbor 或 Lode 的状态机。

### 阶段三：可信可引用运行现场

用户能打开受控浏览器现场，理解当前 Profile、Runtime Session、Provider、Viewer 和页面引用状态；系统能把页面现场压缩为可引用、低噪音的上下文。

达到该阶段时：

- Harbor 围绕首个 provider baseline 提供最小 Profile、Execution Identity、Runtime Session 和 Provider facts。
- Provider 采用、依赖、许可、binary 边界和运行 smoke 有明确评估结论，不把 provider claim 写成成功率承诺。
- Snapshot、RefMap、indexed element、locator fallback 和 evidence provenance 有最小可消费形态。
- Viewer、session refs 和基本 handoff facts 可以被 App 展示、被 Core 记录。
- Core 不直接拥有浏览器现场，Lode 不保存真实账号状态，App 不绕过 Harbor 操作 Runtime Session。

### 阶段四：最小只读任务闭环

一个低风险读能力可以通过公共入口进入 Core，在受控运行现场执行，并返回结构化结果、证据引用或结构化失败。

达到该阶段时：

- 一个低风险读能力可以从 App 或公共 API 进入 Core，并得到结构化结果或结构化失败。
- Core 能围绕该能力完成最小 admission、Run Record、Result Envelope、失败分类和 evidence refs。
- Harbor 能提供该任务所需的 runtime facts、Snapshot / RefMap、session refs 和证据引用。
- Lode 能提供该任务所需的最小 capability package、输入输出 schema、fixtures 和 post-check。
- App 至少能展示一次运行的状态、结果、失败原因和证据引用。

### 阶段五：只读能力产品化

首批网站经验从一次性脚本或提示词，变成用户能在 Library 中理解、安装、锁定、测试、失效和修复的只读能力资产。

达到该阶段时：

- Lode 只读能力资产有稳定生命周期、版本、fixtures、回归检查、post-check 和失效标记。
- Core 可以按能力版本准入、执行、记录、归因和返回规范化结果。
- Harbor 提供足够的 runtime facts、Snapshot / RefMap 和 evidence refs 支撑能力验证。
- App Library 能让用户浏览、安装、锁定、上报和修复首批只读能力资产。
- 探索、失败归因、站点变化、修复草稿、测试和版本更新能反哺 Lode 资产。

### 阶段六：写前验证闭环

WebEnvoy 可以准备写操作、生成预览、说明动作范围并请求授权确认，但默认不执行真实提交。

达到该阶段时：

- Lode 能声明 validate-only、draft 或 preview 写能力，以及预期变更、resource requirements、post-check 和失败分类。
- Core 能生成 action request、授权要求和结构化预览，不把预览当作已提交结果。
- Harbor 能提供页面引用、表单状态、输入目标和证据引用，支撑用户理解将要发生的写操作。
- App 能展示写前预览、目标、动作范围、授权确认和用户可取消入口。
- 写前验证失败、页面变化或用户取消都能进入 Run Record，而不是消失在 UI 状态里。

### 阶段七：受控写入闭环

首批低风险真实写操作可以在明确授权、运行记录、幂等和对账语义下执行。

达到该阶段时：

- Lode 能声明真实写能力的 action boundary、idempotency key、write operation ref、post-check 和修复线索。
- Core 能记录 accepted、running、terminal、unknown outcome、write operation ref、authorization decision 和 post-check 事实。
- Harbor 能提供写入前后证据、控制权事实和页面现场 provenance。
- App 能让用户确认授权、执行、查看业务结果、理解 unknown outcome，并进入对账或人工处理；详细运行记录按需展开。
- 真实写入仍限制在首批低风险能力内，不因阶段成立而开放任意网站提交。

### 阶段八：可恢复多步读写工作流

任务运行不只返回成功或失败，还能让用户在跨页面、多步骤、读写混合、异常、人工接管、未知结果和对账场景下理解现状并继续处理。

达到该阶段时：

- Core Run Record 能记录多步任务的 accepted、running、terminal、unknown outcome、recovery、handoff 和 reconciliation 事实。
- Harbor 能提供 viewer、handoff、control ownership、session continuity 和 evidence provenance。
- Lode 能声明多步 workflow package、step-level post-check、failure classification 和修复线索。
- App 能让用户观察、接管、恢复、停止、重试或对账，而不复制上游状态机。
- 人工接管、验证码、登录、页面变化和资源不满足都能被归类到明确的运行事实。

### 阶段九：日常产品与多入口稳定

用户可以通过 Work、Library、Browser、API、CLI、MCP 和 SDK 日常使用同一套能力模型，而不需要理解 3+1 内部结构。

达到该阶段时：

- Work 表面覆盖任务提交、业务结果、授权确认、恢复和对账入口；运行状态与证据按需进入详情或诊断。
- Library 表面覆盖能力浏览、安装、更新、锁定、草稿、修复、上报和版本策略。
- Browser 表面覆盖 Profile、Runtime Session、Viewer、接管、Provider facts 和连接状态。
- Settings 能表达本地 API、Harbor、Lode 资产来源、数据目录、权限和隐私策略。
- API、CLI、MCP、SDK 和 App 共用同一条 Core 任务路径；错误、运行记录、权限、授权和恢复入口语义一致。

### 阶段十：生态与协作扩展

WebEnvoy 可以扩展到更多 provider、更多资产来源、团队协作、可选同步和外部集成，而不破坏核心边界。

达到该阶段时：

- Harbor 支持多 provider 和远程 session，但仍只输出 runtime facts。
- Lode 支持平台资产、用户 overlay、fork、draft 和可选同步。
- Core 保持 provider-neutral 和 capability-version-aware。
- SDK、MCP、CLI 和外部集成继续消费同一套公共合同。
- Benchmark、crawler、hosted runtime 和 marketplace 都不越界变成默认产品合同。

## 更新规则

- 本文只维护稳定路线，不维护当前活跃 issue、Work Item、PR 或执行看板。
- 当前执行状态以 GitHub Milestones、Project、issues 和 PR 为准。
- GitHub Milestone 只承载当前 1-3 个可交付阶段，不承载全部远期设想。
- 功能需求（FR）issue 表达用户可见或系统可验证的能力增量。
- 工作项（Work Item）issue 是可由一个 PR 完成的最小执行单元。
- 新建功能需求或工作项前，先确认它属于当前活跃 Milestone；不属于则回到本文或 backlog。
- 仓库级 `ROADMAP.md` 可以作为本文的仓库级投影，指导本仓 Milestone 规划，但不能新增跨仓阶段、重定义目标状态或覆盖本文边界。
- 除仓库级 `ROADMAP.md` 外，单仓 planning 文档只能解释本仓如何服务当前活跃 Milestone，不能新增跨仓 Milestone。
- 不允许在单仓创建与本文冲突的平行路线图。
- 规格文档只服务当前或下一个活跃 Milestone，不提前铺满远期设计。
- 产品 3+1 拓扑、许可证边界、迁移门、回滚条件和旧仓过渡态的组织级事实，以本仓 [`docs/repository-map.md`](docs/repository-map.md)、[`docs/licensing.md`](docs/licensing.md) 和 [`docs/issue-project-management.md`](docs/issue-project-management.md) 为准；产品代码或仓库级路线图不得单独宣告迁移完成。
- 修改目标状态、阶段阶梯或跨仓边界时，优先在 `WebEnvoy/.github` 发起 PR，并说明受影响仓库。
