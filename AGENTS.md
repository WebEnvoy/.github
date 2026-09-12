# 组织级规划与文档规则

- 产品方向、V1 约束和决策状态只从 `docs/product-architecture-v1.md` 读取；`VISION.md` 与 `ROADMAP.md` 只作入口和能力阶梯，不复制实时状态。
- 用户或 Agent 的真实路径是交付单元。模型、Schema、合同和模块只在服务当前路径时细化；语义独立不等于独立服务、存储或队列。
- 已可见的产品目标完整规划到 Milestone 和 FR，明确 V1、待原型验证与条件性后续范围；具体 Work Item 在接近实施时依据真实消费者和缺口滚动创建。普通小工作可直接使用 Work Item；不补空 Phase、预测性任务或依赖树。
- GitHub Issue、原生 parent/sub-issue/dependency、Milestone、Project、PR、checks、review 与 `main` 是执行真相；不得创建 carrier、影子状态或专用编排 CLI。
- `completed` 只表示该 Issue 的原验收已有证据；`not_planned` 对应 Won’t Do；延期保持 open、进入 Backlog 并退出活跃 Milestone。PR 合并或 checks 通过不自动等于业务完成。
- Provider 接入必须遵守 canonical Qualification Gate。WebEnvoy 只适配、管理、调用和核验 Provider 已有能力，不通过 Harbor、Driver、App、Plugin、SKILL、安装脚本或站点脚本实现、模拟或长期补偿浏览器核心语义，不采用自维护浏览器 fork／内核补丁链。Obscura 在当前愿景完成前不采用，不监控新版本或自动重开。
- 验收分别标注 fixture／mock、真实 Provider、正式安装路径、真实第三方 Agent、真人操作和真实第三方站点；`plugin_verified` 只表示已安装 Plugin + 真实 Agent，接口、CI 或 review 不能冒充用户通过。
- 文档、模板和规划变更不得修改产品代码、用户数据、许可证、仓库权限、分支保护或 required checks，除非对应 Work Item 明确授权。
