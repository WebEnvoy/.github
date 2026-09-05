# WebEnvoy Roadmap

本路线图只描述能力阶梯。产品方向和决策状态见 [canonical v1 规范](docs/product-architecture-v1.md)；当前执行状态见 GitHub Milestone、Project、Issue 和 PR。

## 当前批：受管 Runtime 验证

用户或 Agent 能通过最小正式入口创建并重用一个 WebEnvoy 管理的 Profile，完成人工登录、关闭重开、人工接管和一个低风险真实网站任务。

- 有界验证 Camoufox 的长期 Profile、上传下载、有头人工使用和目标站点兼容性；失败结论也可以完成验证任务。
- 复用现有 Profile 存储、授权、控制权、Run、结果和诊断能力，只补真实消费者所需缺口。
- 尽早消费一个小型网站 SKILL；不等待完整账号模型 UI、多实例直播或全形态合同。

## 下一批：Agent 管理多个 Profile

一个 Agent 宿主能在明确 Grant 下创建、查找、启动和停止多个 Profile，显示账号发现、唯一归属冲突、控制权和授权撤销。

- V1 限定单宿主、一个 Profile 一个主实例、一个 Account 一个可运行 Profile。
- 账号绑定清单不是网站白名单；没有网站 SKILL 不阻断通用浏览器。
- 不前置企业 RBAC、多宿主矩阵、复杂 Provider 自动修复或完整 App 工作台。

## 后续能力主题

- 首个网站运营 SKILL：先完成图片上传、必要字段回读，以及页面实际证明支持的一种明确授权 commit 与对账。
- 轻量 App 现场：Activity 投影、同实例观看、人工接管、必要确认和结果处理。
- 更多 Provider、网站、媒体形态和团队能力只在前述真实切片证明需要后展开。

## 规划规则

- 只细化当前批和下一批；后续主题保持路线图级别。
- 只有真实技术或验收阻塞才建立 dependency；共享文件、历史顺序或“先完善”不是硬依赖。
- 普通工作可直接是 Work Item；Phase／FR 仅在确有分解价值时使用。
- 关闭不等于完成，checks 成功不等于业务成功，原型成功也不自动等于正式能力交付。
