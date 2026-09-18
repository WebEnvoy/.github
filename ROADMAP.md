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
| 长期管理账号、经营对象、受管环境与隐身质量 | [账号、长期环境与隐身质量 #21](https://github.com/WebEnvoy/WebEnvoy/milestone/21) | [账号体系与归属 #469](https://github.com/WebEnvoy/WebEnvoy/issues/469)、[导入迁移与退出 #470](https://github.com/WebEnvoy/WebEnvoy/issues/470)、[Provider 与环境一致性 #471](https://github.com/WebEnvoy/WebEnvoy/issues/471)、[质量规格 #567](https://github.com/WebEnvoy/WebEnvoy/issues/567) |
| 不依赖 App 监督、接管、交还和停止多个原实例 | [无 App 的监督与人工控制 #22](https://github.com/WebEnvoy/WebEnvoy/milestone/22) | [App 冻结归口 #472](https://github.com/WebEnvoy/WebEnvoy/issues/472)、[原实例监督与接管 #473](https://github.com/WebEnvoy/WebEnvoy/issues/473)、[实施项 #569](https://github.com/WebEnvoy/WebEnvoy/issues/569) |
| 通过 CLI／上游入口消费完整 Runtime 和可执行站点 SKILL | [CLI／上游接入、完整 Runtime 与可执行站点 SKILL #23](https://github.com/WebEnvoy/WebEnvoy/milestone/23) | [集成入口 #474](https://github.com/WebEnvoy/WebEnvoy/issues/474)、[站点 SKILL #475](https://github.com/WebEnvoy/WebEnvoy/issues/475)、[跨站验证 #476](https://github.com/WebEnvoy/WebEnvoy/issues/476)、[Runtime #497](https://github.com/WebEnvoy/WebEnvoy/issues/497) |
| 从无 App 正式安装开始长期使用，完整证明修订后 V1 | [无 App 发行与 V1 完整验收 #24](https://github.com/WebEnvoy/WebEnvoy/milestone/24) | [安装升级与恢复 #477](https://github.com/WebEnvoy/WebEnvoy/issues/477)、[完整 V1 验收 #482](https://github.com/WebEnvoy/WebEnvoy/issues/482)、[S0 #561](https://github.com/WebEnvoy/WebEnvoy/issues/561) |

[#482](https://github.com/WebEnvoy/WebEnvoy/issues/482) 将规范第 22 章的现行验收项映射到成果入口，同时覆盖账号/Profile 生命周期、产品安装、资产和原型决策。它是最终验收 FR，不复制各 FR 的实时状态或以主观百分比计量完成度。

当前先审查 [S0 #561](https://github.com/WebEnvoy/WebEnvoy/issues/561) 的产品基线修订。该修订接受前，CLI、受管站点脚本、主动 Network 与受控视觉的新语义仍是规划目标，不表示当前接口已经支持。S1—S6 和 W1—W3 已登记为后续 Backlog，不因登记自动开工。

这些目标可以复用和交叉验证，不要求按 Milestone 编号全串行交付。只在真实技术或验收事实不满足时建立原生 dependency；例如 #454 的 Provider 前置仍由其原生依赖表达。

Provider 策略以用户选择为产品语义：Camoufox 的首个工程验证事实不构成永久默认或其他 Provider 前置；Obscura 已由 [#511](https://github.com/WebEnvoy/WebEnvoy/issues/511) 明确 `not_planned`，不再验证或跟踪。通用选择与新建默认由 [#516](https://github.com/WebEnvoy/WebEnvoy/issues/516) 承接；Provider 资格和 WebEnvoy 不补浏览器核心能力的边界以 canonical 第 9 章为准。

## 条件性后续目标

[V1 后续能力的采用评估 #25](https://github.com/WebEnvoy/WebEnvoy/milestone/25) 保留规范已可见的长期方向：

- [更多上游与宿主集成 #478](https://github.com/WebEnvoy/WebEnvoy/issues/478)：必要连接、授权与控制协作的真实需求；不默认扩张经营策略、内容排期或运营审批。
- [SKILL 修复与贡献 #479](https://github.com/WebEnvoy/WebEnvoy/issues/479)：完整 overlay/fork、更新冲突、贡献与 Library 体验；V1 最低本地覆盖能力仍由 #475 承载。
- [Provider 自助维护 #480](https://github.com/WebEnvoy/WebEnvoy/issues/480)：自动获取/更新/修复/回滚和 BYOL 价值；V1 检测、诊断和处理入口仍由 #471 承载。
- [更多实例布局与规模 #481](https://github.com/WebEnvoy/WebEnvoy/issues/481)：Desktop App 专属布局产品化冻结；实际容量、画面和性能需求回到 #497/#473，重启需新的限定产品决定。

这些是有限的采用评估，不是 V1 门槛或已承诺的实现批次。采用后才根据明确产品范围创建执行任务。BOSS 恢复继续由 [#372](https://github.com/WebEnvoy/WebEnvoy/issues/372) 与 [Lode #272](https://github.com/WebEnvoy/Lode/issues/272) 保留原恢复条件，不默认作为第二站点。新媒体形态、自动登记和同账号多 Profile 仍需真实需求或产品决策；明确非目标不会因为建立规划而恢复。

## 滚动执行规则

- 产品目标完整规划到 Milestone 和原生 FR；每项写清结果、范围、退出信号、失败/恢复与非目标，保持 V1、原型和长期目标区分。
- 具体 Work Item 在接近实施时，依据当前代码、真实消费者与验收缺口滚动创建，并使用原生 parent/sub-issue；不预建远期任务树、固定 PR 数量或对象字段。
- 未来 FR 在现有 [Product Project #7](https://github.com/orgs/WebEnvoy/projects/7) 保持 Backlog；Milestone open 不等于正在实施，不虚构日期或上线承诺。
- 先验证会推翻设计的页面/Provider 假设；普通局部技术选择在既定授权内自行决定，真实授权缺口与产品边界变化才需要决策。
- PR/Issue 关闭、checks 通过和原型结论分别按原验收解释，不能相互代替。当前未满足的 V1 必需项不得静默降为后续。
