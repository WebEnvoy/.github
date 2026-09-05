# 组织级规划与文档规则

- 产品方向、V1 约束和决策状态只从 `docs/product-architecture-v1.md` 读取；`VISION.md` 与 `ROADMAP.md` 只作入口和能力阶梯，不复制实时状态。
- 用户或 Agent 的真实路径是交付单元。模型、Schema、合同和模块只在服务当前路径时细化；语义独立不等于独立服务、存储或队列。
- 只展开当前批和下一批。普通小工作可以直接使用 Work Item；不得为了层级完整补空 Phase／FR 或预测性依赖树。
- GitHub Issue、原生 parent/sub-issue/dependency、Milestone、Project、PR、checks、review 与 `main` 是执行真相；不得创建 carrier、影子状态或专用编排 CLI。
- `completed` 只表示该 Issue 的原验收已有证据；`not_planned` 对应 Won’t Do；延期保持 open、进入 Backlog 并退出活跃 Milestone。PR 合并或 checks 通过不自动等于业务完成。
- 文档、模板和规划变更不得修改产品代码、用户数据、许可证、仓库权限、分支保护或 required checks，除非对应 Work Item 明确授权。
