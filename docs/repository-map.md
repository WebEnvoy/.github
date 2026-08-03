# 仓库地图

本文档是 WebEnvoy 组织级仓库地图、产品 3+1 拓扑和迁移边界的权威来源。它区分当前多仓事实与目标单工作区；产品仓库只维护自身职责，不重复维护完整组织仓库清单。当前仓库、许可证和 GitHub 对象的事实以可回读的仓库文件和 GitHub 链接为准，不把规划完成写成代码迁移完成。

## GitHub 真相切换快照（dry-run）

canonical 产品 code、PR 和新的执行 Work Item 入口已切换到 [`WebEnvoy/WebEnvoy`](https://github.com/WebEnvoy/WebEnvoy)，其中 `services/harbor` 和 `apps/desktop` 分别是 Harbor 与 App 模块；`WebEnvoy/App`、`WebEnvoy/Harbor` 仅是可读、未归档的 legacy source/history/backlog/rollback locator，不接受新实现。Lode 继续是独立 MIT 能力资产仓，不能迁入或改作 runtime runner。

本批次的逐项 inventory（`.github` 4、Core 13、App 17、Harbor 14、Lode 3 个 open issues 及 App PR #291）、Core #339–#347 closeout、PR/artifact provenance、repo flags、rollback 和 `WebEnvoy/.github#10` explicit-confirmation gate 见 [`docs/github-truth-cutover.md`](github-truth-cutover.md)。App/Harbor 未完成 issue 的 target URL 统一记为 `deferred / not-created`，Core/Lode/.github 目标写为同仓 identity；只有 API 真不可用才写 `unavailable`，native 空数组写 `none`，不推断、不转移、不关闭、不归档。

## 当前多仓事实

源代码历史仍分别保存在 Core、App、Harbor 和 Lode 仓库；canonical 新开发入口已经是 `WebEnvoy/WebEnvoy`。`WebEnvoy/App` 与 `WebEnvoy/Harbor` 保持 legacy 可读、未归档、backlog locator 和 rollback locator，不接受新实现；source issue/Project 的 transfer、close、archive/retirement 仍受 #10 gate 约束。

| 仓库 | 可见性 | 许可证 | 当前职责 |
|---|---:|---|---|
| [`WebEnvoy/WebEnvoy`](https://github.com/WebEnvoy/WebEnvoy) | public | AGPL-3.0-only | canonical 产品 monorepo；Core 模块包含 API Server、Core Runtime、CLI、MCP Server、SDK 和正式任务执行逻辑。 |
| [`WebEnvoy/App`](https://github.com/WebEnvoy/App) | public | AGPL-3.0-only | legacy source/history/backlog/rollback locator；active App 模块位于 monorepo `apps/desktop`，承载 Work、Library、Browser、运行记录、证据、异常处理和 Harbor 运行现场界面。 |
| [`WebEnvoy/Harbor`](https://github.com/WebEnvoy/Harbor) | public | AGPL-3.0-only | legacy source/history/backlog/rollback locator；active Harbor 模块位于 monorepo `services/harbor`，包含 Profile、Execution Identity、Runtime Session、Browser Drivers、CDP / VNC、Evidence 和 Runtime API。 |
| [`WebEnvoy/Lode`](https://github.com/WebEnvoy/Lode) | public | MIT | 站点知识、站点能力、原子动作、任务封装、模板和测试样例资产库；`sites/` 仍在此仓库内，不再拆出独立站点仓。 |
| [`WebEnvoy/.github`](https://github.com/WebEnvoy/.github) | public | 无公开许可证 | 组织主页、issue / PR 模板、仓库地图、许可证边界和治理文档。 |
| [`WebEnvoy/research`](https://github.com/WebEnvoy/research) | private | 无公开许可证 | 组织级研究、外部项目调研、横向对比和决策候选，不是产品运行时来源。 |

## 目标产品 3+1 拓扑

目标拓扑是一个产品 monorepo 加一个独立能力资产仓库：

```text
WebEnvoy/WebEnvoy  (AGPL-3.0-only，模块化产品 monorepo)
  ├── Core   → Task / Run / Result / Admission / owner API
  ├── App    → Work / Library / Browser 用户入口
  └── Harbor → Profile / Runtime Session / Provider / Evidence owner API

WebEnvoy/Lode  (MIT，独立能力资产仓库)
  └── sites、capability / workflow package、schema、fixtures、post-check、registry
```

`Core`、`App`、`Harbor` 在目标 monorepo 中仍是独立模块、独立进程和独立 owner API；monorepo 不等于单进程、共享状态机或共享 truth source。App 只通过 Core / Harbor owner API 消费事实，不直连实现；Core 不接管 Harbor 运行现场；Harbor 不判断任务业务成功；Lode 不选择 Runtime Session，也不保存真实账号状态。

Lode 保持独立 MIT 仓库，消费方通过明确的版本和内容哈希（version/hash pin）锁定，并由 compatibility CI 验证兼容性。Lode 不使用 git submodule，`sites/` 不再拆成多个仓库；平台资产与用户个人资产的边界仍由 Lode 自己维护。

[`WebEnvoy/WebEnvoy` 的跨仓架构文档](https://github.com/WebEnvoy/WebEnvoy/blob/main/docs/architecture/cross-repo-architecture.md)继续记录 Core、App、Harbor、Lode 的运行时职责和 truth source；本文件只负责组织级仓库形态、迁移门和 GitHub 映射。迁移实现 PR 必须让该架构文档与目标模块边界同步，但本批次不修改产品仓库。

## 当前到目标的映射

| 当前来源 | 目标位置 | 状态与约束 |
|---|---|---|
| `WebEnvoy/WebEnvoy` Core | monorepo `Core` 模块 | 目标保留 Core owner API 和运行真相；本 PR 不导入源码。 |
| `WebEnvoy/App` | monorepo `App` 模块 | 旧仓仅作可读 legacy source/backlog/rollback locator；新实现统一进入 monorepo，且必须保留 App Shell 与独立进程边界。 |
| `WebEnvoy/Harbor` | monorepo `Harbor` 模块 | 旧仓仅作可读 legacy source/backlog/rollback locator；新实现统一进入 monorepo，且不得带入 Profile、credential、Cookie、token 或未脱敏运行现场。 |
| `WebEnvoy/Lode` | 独立 `WebEnvoy/Lode` | 不迁入 monorepo、不使用 submodule；只通过 version/hash pin 和 compatibility CI 消费。 |

目标拓扑仍不表示当前已经完成源码历史导入、workspace、CI、Desktop 装配或仓库退役；canonical development entry 已切换完成。源 issue/Project 的 transfer、close、archive/retirement 仍须通过 #10 的单独授权门。

## 跨模块职责与数据流

以下关系中的 `Core`、`App`、`Harbor` 是模块名称；历史 source 仍分别由上表的独立仓库承载，新的实现入口已统一到 monorepo。

```text
User / Agent / CLI / MCP / SDK / App
  -> Core owner API
  -> Core task path
      -> Lode: capability / workflow / schema / checks
      -> Harbor owner API: runtime facts / session / snapshot / evidence
  -> Core result envelope / run record
  -> App: run viewer / evidence view / recovery UI
```

- Core 拥有 Task、Run、Result Envelope、Run Record、Admission 和公共任务入口；不拥有浏览器 Profile、Runtime Session 内部细节、站点知识或 App UI 状态。
- Harbor 拥有 Profile、Execution Identity、Runtime Session、Provider facts、Snapshot、RefMap、Evidence refs 和 Viewer / handoff facts；不拥有任务业务成功、Lode schema 或 Core Run Record。
- Lode 拥有 capability / workflow package、输入输出 schema、fixtures、post-check、asset registry、版本和失效标记；不拥有浏览器会话、真实账号状态、Core admission 或 App UI 状态。
- App 拥有 Work、Library、Browser 用户表面、用户意图和展示状态；不复制 Core、Harbor 或 Lode 的状态机和持久化真相。

## 迁移门

后续源码导入、workspace、CI、运行时装配和 source-object 退役只在下列门全部通过、证据绑定同一迁移 head 并由 owner 明确确认后推进；canonical development entry 已切换，不由这些门重新门禁。任一门失败或事实不可用，都暂停剩余迁移并保留 legacy locator 与 rollback 路径。

| 门 | 必须回读的证据 | 失败条件 |
|---|---|---|
| 合同与所有权门 | Core / App / Harbor owner API、模块边界、Lode 消费合同和禁止跨界清单 | 发生 truth source、API、状态机或敏感数据边界冲突；migration PR 混入行为重构。 |
| 历史门 | 源仓 commits、branches、tags、release、issue / PR 关联、作者和许可证来源映射 | 历史、来源或开放 GitHub 对象无法完整映射，或无法解释差异。 |
| clean-checkout 门 | 全新 checkout 的依赖安装、按路径 CI、Core/App/Harbor 构建与测试、Desktop 装配和 Lode compatibility CI | 不能从干净工作区复现构建、测试、打包或 owner API smoke；依赖未锁定；需要 submodule。 |
| 运行时装配门 | Core、App、Harbor 仍以独立进程启动，owner API smoke、版本兼容和失败回滚演练 | App 直连实现、模块合并为单进程、运行事实无法归属 owner，或 packaged runtime 验证失败。 |
| GitHub mapping 门 | issues、sub-issues、milestones、labels、Projects、PR、默认分支和组织入口的映射与 head 回读 | 任何执行真相、关系、入口或权限映射缺口；未能证明新旧对象对应关系。 |
| 数据与许可证门 | 各源仓 LICENSE / README、历史归属、第三方依赖、敏感数据边界和 Lode MIT 资产范围 | 许可证或来源歧义、Profile / credential / Cookie / token / raw DOM/HAR/截图进入迁移材料。 |
| 回滚门 | 旧 App/Harbor branch、release、入口、ruleset 和恢复步骤的可用性；切换前后 readback 和差异记录 | 无法恢复原入口、旧仓 truth 或发布路径；回滚验证失败。 |

通过迁移门后仍需单独完成可回滚发布和 GitHub mapping 的 owner 确认，才能授权 source-object 退役或后续源码导入；本文件和本批次 PR 不执行源码迁移、transfer、close 或 archive。

## 失败、暂停与回滚

- 合同、历史、clean-checkout、运行时装配、数据/许可证、GitHub mapping 或回滚门任一未通过时，保留 monorepo canonical entry、legacy locator 与 rollback path；不把文档状态写成源码迁移或 source-object 退役完成。
- GitHub、仓库、许可证或关键架构事实暂时不可读时暂停更新，不用推测补齐缺口，并记录 `needs-decision` 或待补证据。
- 文档 PR 自身校验失败时，只回滚本次四份文档改动，恢复原地图、许可证和治理入口；不改产品代码或仓库设置。
- 后续源码导入、发布或运行时验证失败时，保留 monorepo 与 legacy locator 的差异和证据，并按记录的 rollback target 演练恢复；旧仓在明确 source-object 退役授权前不归档。

## 延后条件（Deferred）

- 源码导入、单一 workspace、按路径 CI、Desktop 装配和运行时行为变更，延后到合同、历史、clean-checkout 和运行时装配证据齐备的后续 Work Items。
- App/Harbor legacy source issue/Project 的 transfer、close、archive/retirement，延后到 GitHub mapping、可回滚发布、readback、rollback evidence 和 owner 明确确认全部完成之后；canonical development entry 不再延后。
- `contracts`、SDK、skills 或独立 `sites` 仓库只有在外部发布、独立 ACL、部署或许可证边界形成真实需求后再评估；本批次不创建。
- 行为重构、真实写入、敏感数据迁移和 Core/Harbor truth source 合并不属于本路线和本批次。

## 命名约定

- 目标产品 monorepo 和 Core owner API 使用 `WebEnvoy/WebEnvoy`；`Core`、`App`、`Harbor` 是其模块名，不再用仓库名暗示单进程。
- 统一用户入口模块使用 `App`，Runtime 模块使用 `Harbor`，能力资产仍使用 `WebEnvoy/Lode`。
- 组织配置仓库使用 `WebEnvoy/.github`，研究仓库使用 `WebEnvoy/research`。
- 不创建 `webenvoy-*` 前缀仓库，不创建独立 `sites` 仓库；只有出现明确发布、ACL、部署或许可证边界时，才重新评估新仓库。

## 未来可能拆分的仓库

只有当产品边界、部署形态、维护节奏或许可证边界足够清晰时，才考虑新增仓库。当前不创建以下候选仓库：

| 候选仓库 | 倾向许可证 | 可能内容 |
|---|---|---|
| `WebEnvoy/contracts` | MIT 或 Apache-2.0 | OpenAPI、JSON Schema、Runtime Contract Schema、Capability Package Schema、错误码和公共协议定义。 |
| `WebEnvoy/sdk-js` | MIT 或 Apache-2.0 | TypeScript / JavaScript SDK、生成类型和客户端封装。 |
| `WebEnvoy/sdk-python` | MIT 或 Apache-2.0 | Python SDK、生成模型和客户端封装。 |
| `WebEnvoy/cloud` | 待定 | 公共 Registry、同步、审核、分发和托管运行能力。 |
| `WebEnvoy/docs` | 待定 | 独立文档站或官网。 |

## 维护规则

- 仓库新增、重命名、可见性或许可证调整，先更新本文档并回读对应 GitHub 对象。
- 修改目标 3+1 拓扑、迁移门、回滚条件或跨模块边界时，先在 [Phase #5](https://github.com/WebEnvoy/.github/issues/5) / [FR #6](https://github.com/WebEnvoy/.github/issues/6) / [Work Item #8](https://github.com/WebEnvoy/.github/issues/8) 的组织级 PR 中记录，并说明受影响仓库。
- 产品仓库 README 只保留简短关系说明和本文档链接；产品仓库 docs 不维护完整组织仓库表。
- 外部项目调研不放在产品仓库，统一进入 `WebEnvoy/research`。
