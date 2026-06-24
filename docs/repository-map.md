# 仓库地图

本文档是 WebEnvoy 组织级仓库地图的权威来源。产品仓库只维护自身职责，不重复维护完整仓库清单。

## 当前仓库

| 仓库 | 可见性 | 许可证 | 职责 |
|---|---:|---|---|
| `WebEnvoy/App` | public | AGPL-3.0-only | WebEnvoy 的统一产品入口和 App Shell，承载 Work、Library、Browser 三个产品域：任务运行、能力资产、运行记录、证据、异常处理，以及 Harbor 提供的 Profile、Runtime Session、Viewer 和人工接管界面。 |
| `WebEnvoy/WebEnvoy` | public | AGPL-3.0-only | WebEnvoy Core 仓库，包含 API Server、Core Runtime、CLI、MCP Server、SDK 和正式任务执行逻辑；该仓库可简称 WebEnvoy Core，不等同于完整 WebEnvoy 产品体系，也不承载统一 App Shell。 |
| `WebEnvoy/Harbor` | public | AGPL-3.0-only | Agent-ready Profile Runtime，包含 Profile、Execution Identity、Runtime Session、Browser Drivers、CDP / VNC、Evidence 和 Runtime API。 |
| `WebEnvoy/Lode` | public | MIT | 站点知识、站点能力、原子动作、任务封装、模板和测试样例资产库。 |
| `WebEnvoy/research` | private | 无公开许可证 | 组织级研究、外部项目调研、横向对比、技术主题和决策候选。 |
| `WebEnvoy/.github` | public | 无公开许可证 | 组织主页、issue / PR 模板、组织级仓库地图、许可证边界和治理文档。 |

## 跨仓关系

```text
WebEnvoy/App
  作为统一人类用户入口
  通过 WebEnvoy API Server 进入 Core Runtime
  通过 Lode 或 WebEnvoy API 提供平台资产、用户个人资产、网站探索、能力草稿、版本管理和失效上报入口
  通过 Harbor Runtime API 呈现 Profile、Runtime Session、Viewer 和人工接管界面

WebEnvoy Core (`WebEnvoy/WebEnvoy`)
  读取 Lode 中的站点知识、站点能力和任务封装
  通过 Harbor 获取 Runtime Session、执行身份、能力事实和证据上下文
  向 Agent、SDK、CLI、MCP 和 WebEnvoy App 返回结构化事实

Harbor
  提供 Profile、Execution Identity、Browser Session、CDP / VNC、Browser Drivers、能力事实和 Evidence
  不理解具体站点业务，不执行 Lode 任务封装

Lode
  提供结构化资产定义、测试样例、版本和失效标记
  不管理 Runtime、Profile、CDP、VNC 或用户私有状态

research
  保存调研材料和决策候选
  不作为正式产品文档或 API Contract 的来源

.github
  保存组织级地图、许可证边界和协作规则
```

## 命名约定

- 统一产品入口使用 `WebEnvoy/App`；
- Core 仓库使用 `WebEnvoy/WebEnvoy`，也称 WebEnvoy Core；
- Runtime 仓库使用品牌名 `WebEnvoy/Harbor`；
- 资产库使用首字母大写 `WebEnvoy/Lode`；
- 研究仓库使用小写 `WebEnvoy/research`；
- 组织配置仓库使用 GitHub 约定名 `WebEnvoy/.github`；
- 不再创建 `webenvoy-*` 前缀仓库，除非未来有明确发布边界。

## 未来可能拆分的仓库

只有当产品边界、部署形态、维护节奏或许可证边界足够清晰时，才考虑新增仓库。

| 候选仓库 | 倾向许可证 | 可能内容 |
|---|---|---|
| `WebEnvoy/contracts` | MIT 或 Apache-2.0 | OpenAPI、JSON Schema、Runtime Contract Schema、Capability Package Schema、错误码和公共协议定义。 |
| `WebEnvoy/sdk-js` | MIT 或 Apache-2.0 | TypeScript / JavaScript SDK、生成类型和客户端封装。 |
| `WebEnvoy/sdk-python` | MIT 或 Apache-2.0 | Python SDK、生成模型和客户端封装。 |
| `WebEnvoy/cloud` | 待定 | 公共 Registry、同步、审核、分发和托管运行能力。 |
| `WebEnvoy/docs` | 待定 | 独立文档站或官网。 |

## 维护规则

- 仓库新增、重命名、可见性或许可证调整，先更新本文档；
- 产品仓库 README 只保留简短关系说明和本文档链接；
- 产品仓库 docs 不维护完整组织仓库表；
- 外部项目调研不放在产品仓库，统一进入 `WebEnvoy/research`。
