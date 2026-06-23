# Issue 与 Project 管理规范

本文档是 WebEnvoy 组织级 issue、label、template 和 Project 配置的权威来源。

## 总体原则

- `.github` 维护组织级规范、默认 issue templates、统一 labels 和 Project 工作流；
- 产品仓库维护具体执行 issue；
- `research` 维护调研材料和调研沉淀，普通调研不进入 issue / Project；
- 不创建 repo-level Projects；每个执行仓库使用一个组织级 Project；
- Issue / Project 只管理可执行、可关闭、需要跟踪的工作项；
- 目标、路线、决策记录、调研沉淀默认进入文档系统，不进入工作队列；
- 模型收敛为 `Phase -> FR -> Work Item`。

## Issue 层级

```text
L0 Project
  对应仓库的 WebEnvoy 组织级 Project

L1 Phase
  阶段性工作块

L2 FR
  可交付功能需求

L3 Work Item
  可执行工作项
```

## 层级定义

| 层级 | 用途 | 典型位置 |
|---|---|---|
| Phase | 阶段性能力域或工作块 | 实际负责仓库；跨仓时加 `scope: cross-repo` |
| FR | Feature Requirement，可交付功能需求 | 实际负责仓库；跨仓时加 `scope: cross-repo` |
| Work Item | 可执行工作项，可覆盖实现、缺陷、文档、维护、调研动作和决策动作 | 实际负责仓库 |

## Issue Types

组织级只启用以下 Issue Types：

```text
Phase
FR
Work Item
```

以下类型不再启用：

```text
Initiative
Decision
Research
Docs
Chore
Bug
Task
```

处理方式：

- 目标 / Initiative：进入 roadmap 文档、positioning 文档或 milestone，不作为 issue type；
- Decision：进入决策文档；若阻塞执行，在相关 issue 上加 `status: needs-decision`；
- Research：进入 `WebEnvoy/research`；若需要执行调研动作，用 `Work Item` 表达；
- Docs / Chore / Bug / Task：统一用 `Work Item` 表达，并通过 `area`、`impact`、正文和验收标准区分。

## Labels

Labels 只表达横切属性，不表达层级。层级由 sub-issues 表达，类型由 Issue Type 表达。

Label 分组：

```text
status: needs-triage / needs-decision
priority: p0 / p1 / p2 / p3
track: foundation / runtime / capability / product / ecosystem / research / governance
area: core / api / console / cli / mcp / runtime / profile / browser-driver / evidence / lode / capability / schema / docs / infra / governance / research
impact: breaking / public-api / security / data-boundary / licensing
scope: cross-repo
```

统一 label schema 维护在根目录 `labels.yml`。

## Issue Templates

默认 templates 维护在 `ISSUE_TEMPLATE/`：

```text
config.yml
phase.yml
feature_request.yml  -> FR 表单
work_item.yml        -> Work Item 表单
```

模板不再默认添加 `status: needs-triage`。创建 issue 时应通过模板完成基本分类；只有信息不完整、归属不清或需要人工分拣时，才手动添加 `status: needs-triage`。

## Project

组织级 Project 模板：

```text
WebEnvoy Project Template
https://github.com/orgs/WebEnvoy/projects/2
```

该 Project 已标记为模板，只用于复制，不承载真实工作项。

当前 Projects：

```text
WebEnvoy Project Template
  https://github.com/orgs/WebEnvoy/projects/2
  用途：模板，不承载真实工作项

WebEnvoy Governance
  https://github.com/orgs/WebEnvoy/projects/6
  仓库：WebEnvoy/.github

WebEnvoy Core
  https://github.com/orgs/WebEnvoy/projects/7
  仓库：WebEnvoy/WebEnvoy

Harbor Runtime
  https://github.com/orgs/WebEnvoy/projects/8
  仓库：WebEnvoy/Harbor

Lode Capability
  https://github.com/orgs/WebEnvoy/projects/9
  仓库：WebEnvoy/Lode
```

`research` 默认不创建 Project。普通调研沉淀在 `WebEnvoy/research` 文档中；只有明确执行动作时，才在相关产品仓库创建 `Work Item`。

## Project 字段

| 字段 | 类型 | 选项 |
|---|---|---|
| Status | Single select | Backlog, Ready, In Progress, Blocked, In Review, Done, Won’t Do |
| Priority | Single select | P0, P1, P2, P3 |
| Track | Single select | Foundation, Runtime, Capability, Product, Ecosystem, Research, Governance |
| Size | Single select | XS, S, M, L, XL |
| Risk | Single select | Low, Medium, High |
| Start date | Date | - |
| Target date | Date | - |

GitHub Project 默认字段如 Milestone、Repository、Labels、Assignees、Parent issue、Sub-issues progress 和 Linked pull requests 保持使用。

`Target` 字段不再使用。发布批次由 GitHub Milestone 管理。

## Milestone 管理

Milestone 用于仓库内发布批次和交付范围，不用于组织级路线、长期目标、Track 分组或优先级。

推荐命名：

```text
v0.1-runtime-session
v0.1-runtime-contract
v0.1-capability-schema
v0.1-browser-driver
v0.1-governance
```

不推荐命名：

```text
P0
P1
Now
Next
Backlog
Runtime
Foundation
```

只有当某仓库有明确发布或交付批次时才创建 milestone。

## Project 收录规则

所有通过组织模板创建的 issue 都应进入对应仓库的 Project：

```text
Phase
FR
Work Item
```

原因：当前 issue type 已经只保留工作型 issue，Project 应作为完整执行面；Roadmap 等视图通过过滤控制显示粒度。

每个仓库 Project 配置一个 auto-add workflow：

```text
repository: 当前 Project 对应的仓库
filter: is:issue is:open
```

## 建议视图

| View | 类型 | 用途 |
|---|---|---|
| Backlog | Table 或 Board | 需求池，Status = Backlog |
| Roadmap | Roadmap 或 Table | 只看 Phase / FR，必要时按 Milestone、Track、Priority 查看 |
| Board by Status | Board | 全部工作项的执行看板 |
| By Milestone | Table | 按发布批次查看工作范围 |
| Phases | Table | 只看 Phase |
| FRs | Table | 只看 FR |
| Work Items | Table | 只看 Work Item |
| Cross-Repo | Table | 只看 `scope: cross-repo` |

不再创建固定的 `Inbox / Triage`、`Initiatives`、`Decisions`、`Research` 视图。

GitHub 当前可通过 API 读取 Project views，但未暴露创建或修改 view 的 GraphQL mutation；因此 views 需要在 GitHub UI 中创建和保存。创建新的仓库 Project 时，应优先从 `WebEnvoy Project Template` 复制，避免手工重复配置字段和 views。

## 自动化

每个仓库 Project 都应配置 GitHub Project auto-add workflow：

```text
repository: 当前 Project 对应的仓库
filter: is:issue is:open
```

原因：当前启用的 issue types 只有 `Phase / FR / Work Item`，所有 issue 都是工作型 issue。

推荐保留或配置低风险自动流转：

```text
Item added -> Status = Backlog
Issue closed -> Status = Done
PR merged -> Status = Done
```

不自动流转：

```text
Ready
In Progress
Blocked
Won’t Do
```

这些状态需要人为判断。

## 生命周期

1. 创建 issue：通过模板选择 `Phase / FR / Work Item`；
2. 加入 Project：自动或手动进入对应仓库 Project，默认 Status = Backlog；
3. 准备执行：信息完整后，Status 设为 Ready；
4. 执行：开始处理后，Status 设为 In Progress；
5. 阻塞：Status 设为 Blocked，并说明 blocker；如果是决策阻塞，加 `status: needs-decision`；
6. Review：PR、文档或方案进入 review 后，Status 设为 In Review；
7. 完成：PR merge 或 issue close 后，Status 设为 Done；
8. 不做：关闭为 not planned，Status 设为 Won’t Do，并说明原因。

## 示例

```text
Phase: Runtime Contract v0
labels: scope: cross-repo

  FR: WebEnvoy requests Harbor runtime session
    Work Item: define RuntimeSession request schema
    Work Item: map Harbor errors to WebEnvoy Core errors

  FR: Harbor exposes Runtime Session API
    Work Item: define RuntimeSession lifecycle states
    Work Item: implement create session endpoint
```

## 维护规则

- 修改 label schema 后必须同步到所有仓库；
- 修改 issue template 前必须确认引用的 label 已存在；
- Project 字段变更必须先更新本文档；
- GitHub UI 中的 Project views 和 built-in auto-add workflow 变更必须同步回本文档；
- 跨仓工作使用 `scope: cross-repo`，不使用单独的 Cross-Repo issue type；
- 普通调研沉淀在 `WebEnvoy/research`；采纳后转化为 Phase / FR / Work Item；
- 决策记录进入文档；执行中被决策阻塞的 issue 使用 `status: needs-decision` 标记。
