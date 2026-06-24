# WebEnvoy

真实网站不应该只靠 Agent 现场摸索，也不应该只靠一堆随时失效的浏览器脚本。

WebEnvoy 把账号环境、站点知识、网页读写、结果归一和运行证据组织成可复用能力，让人类用户、Agent 和上游系统安全、高效、稳定地完成采集、发布、修改、验证和恢复任务。

## 核心方向

完整组织级愿景见 [WebEnvoy Vision](https://github.com/WebEnvoy/.github/blob/main/VISION.md)。

WebEnvoy 重点解决真实网站能力使用中的几个关键问题：

- 通过持续浏览器身份和环境一致性，降低登录异常、验证码和访问受限等风险；
- 把网页现场压缩成低噪音上下文，减少截图、DOM、日志和重复探索带来的成本；
- 将网站入口、页面状态、操作路径、资源需求和验证逻辑沉淀为可复用能力资产；
- 将采集、发布、修改、删除和提交等读写结果归一成可校验、可追溯、可消费的公共结果；
- 让每次任务具备运行记录、证据引用、失败归因、人工接管和后续对账能力。

## 核心仓库

- [`App`](https://github.com/WebEnvoy/App)：统一人类用户入口，承载 Work、Library、Browser 三个产品域。
- [`WebEnvoy`](https://github.com/WebEnvoy/WebEnvoy)：WebEnvoy Core，负责 API Server、Core Runtime、能力准入、资源匹配、Run Record、结果封装和失败归因。
- [`Harbor`](https://github.com/WebEnvoy/Harbor)：Agent-ready Profile Runtime，负责 Profile、执行身份、Runtime Session、Viewer、人工接管、provider 能力事实和运行证据。
- [`Lode`](https://github.com/WebEnvoy/Lode)：能力资产库，负责站点知识、站点能力、原子动作、任务封装、模板、测试样例、输出契约、资源需求和版本资产。
- [`.github`](https://github.com/WebEnvoy/.github)：组织级文档、仓库地图、许可证边界、issue / PR 模板和协作规则。

## 文档

- [仓库地图](https://github.com/WebEnvoy/.github/blob/main/docs/repository-map.md)
- [许可证边界](https://github.com/WebEnvoy/.github/blob/main/docs/licensing.md)

