# Issue 与 Project 管理规范

本文是 WebEnvoy 组织级 GitHub 规划规则。产品方向见 [`product-architecture-v1.md`](product-architecture-v1.md)；实时执行状态只以 GitHub 原生对象为准。

## 真相分工

| 载体 | 负责的事实 |
|---|---|
| canonical 规范与 ADR | 产品方向、决策成熟度和变更原因 |
| ROADMAP | 能力阶梯与远期主题 |
| Issue | 本次用户结果、范围和验收 |
| parent/sub-issue/dependency | 分解关系与真实阻塞 |
| Milestone | 一个仓库的有限交付批次 |
| Project | 组织和执行视图 |
| PR/checks/review/main | 已审查、验证和合并的变更 |
| live 或发布证据 | 已证明可用的用户能力 |

不得创建仓内 carrier、影子状态、专用编排 CLI 或第二看板。

## Issue 类型

- `Work Item`：默认类型；一个可独立执行和验收的工作。
- `FR`：承载完整用户或系统成果；可先明确结果、边界和退出信号，接近实施时再创建必要的 Work Item 子项。
- `Phase`：确有多个 FR 时使用的阶段父项。

层级按需使用，不要求普通 Work Item 补空 FR／Phase。标题描述结果，不加 `FR:`、`Work Item:` 或 `Phase:` 前缀。

Work Item 至少写清：用户结果、规范依据、非目标、已知事实与待验证假设、最小消费者、成功／必要拒绝／恢复验收、真实依赖，以及复用或删除项。父项只需说明意图、子项和退出信号。

## 关系与状态

- 原生 parent/sub-issue 表达分解；dependency 只表达“不满足就不能开始或验收”的硬阻塞。
- 跨仓软对齐写在正文，不伪造跨仓 Milestone 归属。
- `completed`：原 Issue 的验收已有可核对证据；Project `Status = Done`。
- `not_planned`：原目标未完成且被替代或取消；Project `Status = Won’t Do`。被替代时链接真实后继；纯取消说明原因，不为整齐虚构后继。
- 延期：Issue 保持 open、退出活跃 Milestone、Project `Status = Backlog`，写明重新进入条件；不得标为 Blocked。
- PR 合并、checks 成功、子项关闭或进度 100% 都不能单独证明父项或业务能力完成。

## Milestone 与 Project

沿用组织 Project：Governance `#6`、WebEnvoy Product `#7`、Lode Capability `#9`；不新建平行 Project。已可见产品目标完整落到 Milestone 和 FR，名称描述交付结果，不机械使用阶段号。V1 必需、待原型验证和条件性后续范围分别说明；远期 Milestone 的 open 状态不表示正在实施，其 FR 保持 Backlog，不虚构日期、容量或上线承诺。

Project 字段沿用 `Status`、`Priority`、`Track`、`Size`、`Risk` 及 GitHub 默认字段。允许自动加入 open Issue；不得配置“任意 Issue close 或 PR merge 即 Done”的无条件自动化。自动化无法区分 close reason 时，由处置者回读并设置 `Done` 或 `Won’t Do`。

## 生命周期

1. 创建或复用 Issue，先核对是否已有等价对象。
2. 为已可见产品结果建立或复用 Milestone／FR；具体 Work Item 仅在接近实施、能明确真实消费者和验收时滚动创建，不预建远期执行树。未进入实施的 FR 保持 Backlog；明确延期项仍遵守退出活跃 Milestone 的规则。
3. 开始前确认最小消费者、验收和真正依赖；Ready 不要求完整未来任务树。
4. PR 绑定真实 Work Item，按风险运行检查并取得 exact-head 独立 review。
5. 合并后回读 `main`、checks、review 和 Issue 验收；再决定 completed、继续 open 或 not_planned。

## Labels

Labels 只表达横切属性；统一 schema 见 [`../labels.yml`](../labels.yml)。类型由 Issue Type 表达，层级由原生关系表达。
