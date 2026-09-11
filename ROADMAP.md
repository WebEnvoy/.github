# WebEnvoy Roadmap

本路线图提供产品目标与 GitHub 规划入口。方向和决策状态以 [canonical v1 规范](docs/product-architecture-v1.md) 为准；完成状态只从 Milestone、FR、Project、PR 和产品验收证据回读。

## 已有产品路径

| 产品结果 | Milestone | 成果入口 |
|---|---|---|
| 受管 Profile、Provider 采用验证与首个未提交网站 SKILL | [受管 Runtime 验证 #18](https://github.com/WebEnvoy/WebEnvoy/milestone/18) | [#449](https://github.com/WebEnvoy/WebEnvoy/issues/449)，复用 #450/#451/#452；同实例摘要仍按 [#418](https://github.com/WebEnvoy/WebEnvoy/issues/418) 原验收处理 |
| 单宿主在 Grant 下管理多个 Profile、身份归属、控制与撤销 | [Agent 管理多个 Profile #19](https://github.com/WebEnvoy/WebEnvoy/milestone/19) | [#453](https://github.com/WebEnvoy/WebEnvoy/issues/453) / #454 |
| 页面证明的保存、发布、结果对账与测试内容清理 | [首站写入与对账 #20](https://github.com/WebEnvoy/WebEnvoy/milestone/20) | [#419](https://github.com/WebEnvoy/WebEnvoy/issues/419)、[#422](https://github.com/WebEnvoy/WebEnvoy/issues/422)、[#423](https://github.com/WebEnvoy/WebEnvoy/issues/423) 的原始证据 |

复用已经验证的消费者与事实，不为整理层级重开旧承诺或重复网站动作；父项关闭不替代尚未满足的子项验收。

## V1 可见产品目标

| 产品结果 | Milestone | FR |
|---|---|---|
| 长期管理账号、经营对象、受管数据与环境 | [账号与 Profile 生命周期 #21](https://github.com/WebEnvoy/WebEnvoy/milestone/21) | [账号体系与归属 #469](https://github.com/WebEnvoy/WebEnvoy/issues/469)、[导入迁移与退出 #470](https://github.com/WebEnvoy/WebEnvoy/issues/470)、[Provider 与环境一致性 #471](https://github.com/WebEnvoy/WebEnvoy/issues/471) |
| 从资源与活动出发管理，并观察和接管多个原实例 | [人类控制台与多实例监督 #22](https://github.com/WebEnvoy/WebEnvoy/milestone/22) | [人类控制台 #472](https://github.com/WebEnvoy/WebEnvoy/issues/472)、[原实例观看与接管 #473](https://github.com/WebEnvoy/WebEnvoy/issues/473) |
| App 未启动时也可使用正式 Runtime，并按版本扩展网站知识 | [Agent 正式接入与 SKILL 扩展 #23](https://github.com/WebEnvoy/WebEnvoy/milestone/23) | [单宿主正式入口 #474](https://github.com/WebEnvoy/WebEnvoy/issues/474)、[SKILL 与共享知识 #475](https://github.com/WebEnvoy/WebEnvoy/issues/475)、[不同网站场景验证 #476](https://github.com/WebEnvoy/WebEnvoy/issues/476) |
| 从安装产物开始长期使用，完整证明 V1 产品能力 | [V1 产品交付与完整验收 #24](https://github.com/WebEnvoy/WebEnvoy/milestone/24) | [安装升级与恢复 #477](https://github.com/WebEnvoy/WebEnvoy/issues/477)、[完整 V1 验收 #482](https://github.com/WebEnvoy/WebEnvoy/issues/482) |

[#482](https://github.com/WebEnvoy/WebEnvoy/issues/482) 将规范第 22 章的 35 条验收映射到成果入口，同时覆盖账号/Profile 生命周期、产品安装、资产和原型决策。它是最终验收 FR，不复制各 FR 的实时状态或以主观百分比计量完成度。

这些目标可以复用和交叉验证，不要求按 Milestone 编号全串行交付。只在真实技术或验收事实不满足时建立原生 dependency；例如 #454 的 Provider 前置仍由其原生依赖表达。

Provider 策略以用户选择为产品语义：Camoufox 的首个工程验证事实不构成永久默认或其他 Provider 前置；Obscura 的受管 Profile、显式选择和人机共用现场由 [#511](https://github.com/WebEnvoy/WebEnvoy/issues/511) 在 #471 下有界验证。原型、正式可选、用户默认与完整 V1 分别验收。

## 条件性后续目标

[V1 后续能力的采用评估 #25](https://github.com/WebEnvoy/WebEnvoy/milestone/25) 保留规范已可见的长期方向：

- [多宿主与协作运营 #478](https://github.com/WebEnvoy/WebEnvoy/issues/478)：团队、排期/批次和账号策略的真实需求与采用条件。
- [SKILL 修复与贡献 #479](https://github.com/WebEnvoy/WebEnvoy/issues/479)：完整 overlay/fork、更新冲突、贡献与 Library 体验；V1 最低本地覆盖能力仍由 #475 承载。
- [Provider 自助维护 #480](https://github.com/WebEnvoy/WebEnvoy/issues/480)：自动获取/更新/修复/回滚和 BYOL 价值；V1 检测、诊断和处理入口仍由 #471 承载。
- [更多实例布局与规模 #481](https://github.com/WebEnvoy/WebEnvoy/issues/481)：更丰富布局、四实例网格或更大规模的价值与成本；V1 概览、切换和接管仍由 #473 承载。

这些是有限的采用评估，不是 V1 门槛或已承诺的实现批次。采用后才根据明确产品范围创建执行任务。BOSS 恢复继续由 [#372](https://github.com/WebEnvoy/WebEnvoy/issues/372) 与 [Lode #272](https://github.com/WebEnvoy/Lode/issues/272) 保留原恢复条件，不默认作为第二站点。新媒体形态、自动登记和同账号多 Profile 仍需真实需求或产品决策；明确非目标不会因为建立规划而恢复。

## 滚动执行规则

- 产品目标完整规划到 Milestone 和原生 FR；每项写清结果、范围、退出信号、失败/恢复与非目标，保持 V1、原型和长期目标区分。
- 具体 Work Item 在接近实施时，依据当前代码、真实消费者与验收缺口滚动创建，并使用原生 parent/sub-issue；不预建远期任务树、固定 PR 数量或对象字段。
- 未来 FR 在现有 [Product Project #7](https://github.com/orgs/WebEnvoy/projects/7) 保持 Backlog；Milestone open 不等于正在实施，不虚构日期或上线承诺。
- 先验证会推翻设计的页面/Provider 假设；普通局部技术选择在既定授权内自行决定，真实授权缺口与产品边界变化才需要决策。
- PR/Issue 关闭、checks 通过和原型结论分别按原验收解释，不能相互代替。当前未满足的 V1 必需项不得静默降为后续。
