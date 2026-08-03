# 许可证边界

本文档是 WebEnvoy 组织级许可证边界的权威来源。它只记录仓内 `LICENSE`、README 和已确认的组织策略，不是法律意见；具体法律风险需要单独评估。目标 3+1 拓扑不会因为 monorepo 规划而自动改变当前许可证，也不把规划完成写成已完成迁移。

## 当前已确认事实

历史 LICENSE、README 和来源事实仍可由 Core、App、Harbor、Lode source repos 逐一回读；canonical 产品 code、PR 与新的执行 Work Item 已进入 `WebEnvoy/WebEnvoy` 的 Core/App/Harbor modules，Lode 保持独立。下表以各仓 `LICENSE` 和 README 为准；`.github` 与 `research` 当前没有公开许可证声明。

| 仓库 | 许可证 / 可见性 | 当前策略说明 |
|---|---|---|
| [`WebEnvoy/WebEnvoy`](https://github.com/WebEnvoy/WebEnvoy) | AGPL-3.0-only / public | canonical 产品 monorepo；Core 模块承载 API Server、CLI、MCP Server、SDK 和正式任务执行逻辑。 |
| [`WebEnvoy/App`](https://github.com/WebEnvoy/App) | AGPL-3.0-only / public | legacy source/history/backlog/rollback locator；active App 模块位于 monorepo `apps/desktop`，保持 App Shell、Work / Library / Browser 与独立进程边界。 |
| [`WebEnvoy/Harbor`](https://github.com/WebEnvoy/Harbor) | AGPL-3.0-only / public | legacy source/history/backlog/rollback locator；active Harbor 模块位于 monorepo `services/harbor`，承载 Runtime Server、Profile、Execution Identity、Browser Drivers、Evidence 和运行时 API，并保持敏感事实 owner 边界。 |
| [`WebEnvoy/Lode`](https://github.com/WebEnvoy/Lode) | MIT / public | 承载站点知识、能力包、任务封装、模板、fixtures、包格式和版本/失效标记。 |
| [`WebEnvoy/.github`](https://github.com/WebEnvoy/.github) | 无公开许可证 / public | 组织主页、模板、仓库地图、许可证边界和治理文档。 |
| [`WebEnvoy/research`](https://github.com/WebEnvoy/research) | 无公开许可证 / private | 调研、竞品分析、技术比较和决策候选，默认不公开。 |

## 目标 3+1 许可证策略

目标产品 monorepo 是 `WebEnvoy/WebEnvoy`，其中 `Core`、`App`、`Harbor` 三个模块继续处于现有 AGPL-3.0-only 核心边界内。源码导入时必须保留各来源仓的版权声明、许可证文本、历史归属和第三方依赖说明；monorepo 规划不构成重新许可或删除来源历史的授权。

`WebEnvoy/Lode` 继续是独立 MIT 仓库，是 3+1 中唯一不随产品代码 monorepo 迁移的能力资产仓。Lode 的资产、模板和包格式保持 MIT；Core / App / Harbor 只通过明确的 version/hash pin 消费，并由 compatibility CI 验证，不使用 git submodule，不把 `sites/` 拆成新仓库。Lode 不保存账号、Cookie、token、Runtime Session 或未脱敏生产现场。

```text
WebEnvoy/WebEnvoy monorepo
  Core / App / Harbor modules  -> AGPL-3.0-only boundary

WebEnvoy/Lode
  capability / site assets      -> MIT boundary, version/hash pin
```

monorepo 不改变独立模块、独立进程和 owner API 边界，也不允许把 Lode MIT 资产或面向外部集成的公共协议默认复制进 AGPL 核心代码路径。

## AGPL 核心边界

AGPL 仓库承载产品核心能力：

- WebEnvoy App / Console；
- WebEnvoy Core；
- API Server；
- CLI；
- MCP Server；
- Harbor Runtime Server；
- Profile 与 Execution Identity；
- Browser Drivers；
- Evidence Store；
- 正式站点能力执行逻辑。

这些模块不应为了集成便利而复制到宽松许可仓库中。迁移前后都必须保持 Core、App、Harbor 的 owner API 和数据边界，不得用 monorepo 规避许可证或敏感数据约束。

## 宽松许可边界

面向生态集成、跨语言调用和公共协议的内容，未来应优先放入 MIT 或 Apache-2.0 仓库，而不是默认沉入 AGPL 核心仓库。

| 候选仓库 | 可能许可证 | 可能内容 |
|---|---|---|
| `WebEnvoy/contracts` | MIT 或 Apache-2.0 | OpenAPI、JSON Schema、Runtime Contract Schema、Capability Package Schema、错误码、协议定义。 |
| `WebEnvoy/sdk-js` | MIT 或 Apache-2.0 | TypeScript / JavaScript SDK、生成类型、客户端封装。 |
| `WebEnvoy/sdk-python` | MIT 或 Apache-2.0 | Python SDK、生成模型、客户端封装。 |

当前不创建这些仓库，也不在本批次改变任何仓库许可证、可见性、权限或 ruleset。

## 许可证迁移门

任何源码历史导入、发布或 source-object 退役都必须通过组织级[仓库地图迁移门](repository-map.md#迁移门)中的数据与许可证门：确认每个导入文件的来源和许可证、保留历史和 notices、审查第三方依赖，并证明 Profile、credential、Cookie、token、raw DOM/HAR/截图等敏感材料未进入 monorepo 或迁移证据。canonical development entry 已切换，不由该门重新门禁；许可证、历史来源或依赖归属存在歧义时，标记 `needs-decision` 并停止剩余迁移，不用文档推测覆盖冲突。

旧 App/Harbor 仓库仅保留 history/backlog/rollback locator，不是新的开发权威；canonical entry 已切换。若后续导入、发布或运行时验证失败，按 rollback evidence 恢复原许可证载体和 legacy locator；source-object transfer、close、archive/retirement 仍由 [#10](https://github.com/WebEnvoy/.github/issues/10) 单独授权，本批次不执行。

## 当前落地规则

- 现在不新建 `contracts`、`sdk-js`、`sdk-python` 仓库；正式实现 SDK、Client、跨语言 Schema 或公共协议前，先评估是否需要独立宽松许可仓库。
- 不把面向外部集成的客户端库默认放进 AGPL 核心代码路径；共享合同必须先有许可证和发布边界决策。
- Lode 的能力资产、模板和包格式保持 MIT；不使用 submodule，不迁入 `WebEnvoy/WebEnvoy`。
- `research` 保持 private，不放许可证文件；组织 `.github` 的文档也不通过本 PR 添加公开许可证声明。
- 许可证和可见性变更必须先更新本文档，并回读对应仓库的 LICENSE、README 和 GitHub 对象。

## 触发重新评估的情况

出现以下情况时，应重新审视仓库拆分和许可证边界：

- 外部系统需要直接集成 SDK；
- OpenAPI / JSON Schema 开始被多个仓库共同消费；
- MCP client 或轻量 client 需要被第三方项目嵌入；
- Lode 包格式需要作为公共生态协议发布；
- AGPL 对外部集成造成明显阻力；
- monorepo 导入、第三方依赖或历史来源无法证明许可证连续性。
