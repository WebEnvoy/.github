# Issue 与 Project 管理规范

本文档是 WebEnvoy 组织级 issue、label、template 和 Project 配置的权威来源。

## 总体原则

- `.github` 维护组织级规范、默认 issue templates、统一 labels 和 Project 工作流；
- 产品仓库维护具体执行 issue；
- `research` 维护调研任务和调研沉淀；
- 不创建 repo-level Projects，统一使用组织级 Project：`WebEnvoy Roadmap`；
- 不使用 Epic，使用 `Phase` 表达阶段性工作块；
- 一个 Phase 可以包含多个 FR，一个 FR 可以包含多个 Task。

## Issue 层级

```text
L0 Project
  WebEnvoy Roadmap

L1 Initiative
  跨仓目标，通常放 WebEnvoy/.github

L2 Phase
  阶段性工作块，放实际负责仓库

L3 FR
  可交付功能需求，放实际负责仓库

L4 Task / Bug / Decision / Research / Docs / Chore
  具体执行项，放实际负责仓库
```

## 层级定义

| 层级 | 用途 | 典型位置 |
|---|---|---|
| Initiative | 跨仓目标、组织级目标、长期建设方向 | `.github` |
| Phase | 阶段性能力域或工作块 | 实际负责仓库 |
| FR | Feature Requirement，可交付功能需求 | 实际负责仓库 |
| Task | 可由一个或少量 PR 完成的实现任务 | 实际负责仓库 |
| Bug | 缺陷修复 | 实际负责仓库 |
| Decision | 需要明确取舍的产品、架构、仓库或许可证决策 | `.github` 或相关仓库 |
| Research | 外部项目、技术路线、产品形态或方案调研 | `research` |
| Docs | 文档变更 | 实际负责仓库 |
| Chore | 仓库设置、自动化、清理和维护事项 | 实际负责仓库 |

## Issue Types

建议组织级启用以下 Issue Types：

```text
initiative
phase
fr
task
bug
decision
research
docs
chore
```

在 GitHub 组织 Issue Types 未完成配置前，issue title 前缀作为兼容标识：

```text
Initiative: <跨仓目标>
Phase: <阶段性工作块>
FR: <功能需求>
Task: <具体任务>
Bug: <缺陷>
Decision: <决策>
Research: <调研>
Docs: <文档>
Chore: <维护>
```

当前组织 Issue Types 已完成配置：默认 `Feature` 已重命名为 `FR`，默认 `Task`、`Bug` 已规范化，并已新增 `Initiative`、`Phase`、`Decision`、`Research`、`Docs`、`Chore`。

## Labels

Labels 只表达横切属性，不表达层级。层级由 sub-issues 表达，类型由 Issue Type 或 title 前缀表达。

Label 分组：

```text
status: needs-triage / accepted / blocked / needs-decision
priority: p0 / p1 / p2 / p3
track: foundation / runtime / capability / product / ecosystem / research / governance
area: core / api / console / cli / mcp / runtime / profile / browser-driver / evidence / lode / capability / schema / docs / infra / governance / research
impact: breaking / public-api / security / data-boundary / licensing
```

统一 label schema 维护在根目录 `labels.yml`。

## Issue Templates

默认 templates 维护在 `ISSUE_TEMPLATE/`：

```text
config.yml
initiative.yml
phase.yml
feature_request.yml  -> FR 表单
bug_report.yml       -> Bug 表单
task.yml
decision.yml
research.yml
docs.yml
chore.yml
```

模板默认添加 `status: needs-triage`，部分模板会额外添加 track / area label。

## Project

组织级 Project：

```text
WebEnvoy Roadmap
```

当前只维护这一个 Project。不同视角通过 field 和 view 表达，不为单个仓库创建 repo-level Project。

## Project 字段

| 字段 | 类型 | 选项 |
|---|---|---|
| Status | Single select | Inbox, Triage, Ready, In Progress, Blocked, In Review, Done, Won’t Do |
| Priority | Single select | P0, P1, P2, P3 |
| Track | Single select | Foundation, Runtime, Capability, Product, Ecosystem, Research, Governance |
| Target | Single select | P0, P1, P2, P3, Backlog |
| Size | Single select | XS, S, M, L, XL |
| Risk | Single select | Low, Medium, High |
| Start date | Date | - |
| Target date | Date | - |

GitHub Project 默认字段如 Repository、Labels、Milestone、Assignees、Linked pull requests 保持使用。

## 建议视图

| View | 类型 | 用途 |
|---|---|---|
| Inbox / Triage | Table | 新 issue 整理 |
| Roadmap | Roadmap | 按 Track 和 Target 查看计划 |
| Board by Status | Board | 当前执行状态 |
| By Repository | Table | 按仓库查看负载 |
| Initiatives | Table | 组织级 Initiative |
| Decisions | Table | 待决策事项 |
| Research | Table | 调研任务 |

GitHub 当前可通过 API 读取 Project views，但未暴露创建或修改 view 的 GraphQL mutation；因此 views 需要在 GitHub UI 中创建和保存。配置路径：进入 `WebEnvoy Roadmap`，点击 `New view`，按上表创建视图并保存。

## Auto-add workflow

GitHub Project built-in auto-add workflow 需要在 Project UI 中配置。配置路径：进入 `WebEnvoy Roadmap`，打开右上角菜单，进入 `Workflows`，选择 `Auto-add to project`，设置 repository 和 filter 后启用。

推荐 auto-add 过滤条件：

```text
is:issue is:open label:"status: needs-triage"
```

如果当前计划只允许 1 个 auto-add workflow，优先配置 `WebEnvoy/.github`。如果允许 5 个 workflow，则分别配置 `WebEnvoy/WebEnvoy`、`WebEnvoy/Harbor`、`WebEnvoy/Lode`、`WebEnvoy/.github`、`WebEnvoy/research`。

当前 Project 已启用 GitHub 默认 workflows，包括 item added、item closed、pull request merged、pull request linked to issue 和 auto-add sub-issues。它们不替代跨仓 repository auto-add。

## 生命周期

1. 创建 issue：默认 `status: needs-triage`；
2. Triage：补充 Priority、Track、Area、Target、父 issue；
3. 接受：添加 `status: accepted`，Project Status 设为 Ready；
4. 执行：Project Status 设为 In Progress，分配 assignee；
5. 阻塞：添加 `status: blocked`，说明 blocker；
6. 决策：添加 `status: needs-decision`，创建或链接 Decision issue；
7. 完成：PR merge 后关闭 issue，Project Status 设为 Done；
8. 不做：关闭为 not planned，Project Status 设为 Won’t Do，并说明原因。

## 示例

```text
.github#12 Initiative: Harbor Runtime Session v0

  Harbor#21 Phase: Runtime Session API

    Harbor#22 FR: create runtime session
      Harbor#23 Task: define CreateSessionRequest schema
      Harbor#24 Task: implement session create handler
      Harbor#25 Docs: document create session API

    Harbor#26 FR: close runtime session
      Harbor#27 Task: define close behavior
      Harbor#28 Task: release browser resources

  WebEnvoy#18 Phase: Runtime Contract client

    WebEnvoy#19 FR: request Harbor runtime session
      WebEnvoy#20 Task: define RuntimeSession request schema
      WebEnvoy#21 Task: map Harbor errors to Core errors
```

## 维护规则

- 修改 label schema 后必须同步到所有仓库；
- 修改 issue template 前必须确认引用的 label 已存在；
- Project 字段变更必须先更新本文档；
- GitHub UI 中的 Project views 和 built-in auto-add workflow 变更必须同步回本文档；
- 跨仓 Initiative 优先放 `.github`；
- 单仓 Phase / FR / Task 放实际负责仓库；
- research 调研结论采纳后，应转化为 Decision 或 Task，不直接作为正式产品文档来源。
