# WebEnvoy v1 产品与架构方向规范

> **文档状态：** v1.5 产品与架构方向规范（实施基线、上游原版组合、任务页协作与私有补丁退役）
> **适用范围：** WebEnvoy 的产品定位、核心对象、权限模型、Profile 与账号管理、Provider 策略、Agent 接入、App、Harbor、Core、Lode／SKILL 的职责边界，以及 V1 实施与验收范围。
> **不包含：** 对当前仓库实现状态、历史 PR、历史架构质量或完成度的评价。
> **重要说明：** 本规范描述目标方向与 V1 约束，不表示相关能力已经完成实现。
> **2026-09-12 修订：** 在 v1.3 的 Provider Qualification Gate、用户选择和职责边界上，冻结供应方原版浏览器／驱动组合、任务页与原生焦点分离、人机交还后的可信重观察，以及旧私有补丁绑定退役规则。WebEnvoy 不改写供应方程序、资源、驱动 bundle 或行为；v1.3 及更早附件只作历史背景。
> **2026-09-14 实施基线对齐：** V1 Browser Runtime 的十二类最低结果及其规范来源由 WebEnvoy [Browser Runtime 能力规格第 4 节](https://github.com/WebEnvoy/WebEnvoy/blob/main/docs/specs/browser-runtime-capabilities-v1.md#4-v1-十二类能力最低结果矩阵)提供执行索引；它不替代本规范、FR、Work Item 或验证证据，也不把当前未验 Provider／平台升级为支持。

---

## 0. 文档约定

### 0.1 决策状态

本文使用五类状态，避免把长期原则、首版取舍和技术假设混为一谈。

| 状态 | 含义 |
|---|---|
| **已确认原则** | 产品长期应遵守的边界。除非产品定位发生根本变化，否则不应随实现便利而改变。 |
| **V1 收敛约束** | 为降低首版复杂度而采用的明确限制。真实业务证明有必要时，可以在后续版本重新设计。 |
| **待原型验证** | 产品价值方向明确，但技术质量、兼容性、成本或可维护性尚未得到足够证明。 |
| **长期目标** | 值得保留的演进方向，但不进入 V1 必须完成的范围。 |
| **明确非目标** | 当前主动排除，防止产品重新扩张为其他类型的平台。 |

### 0.2 规范性用语

- **必须**：强制要求。
- **不得**：明确禁止。
- **应**：默认应遵守，偏离时需要有具体理由。
- **可以**：允许，但不是实现或验收前提。
- **建议**：实施顺序或产品体验建议，不等同于永久架构约束。

---

# 1. 产品定位

## 1.1 一句话定位

> **WebEnvoy 是面向矩阵社媒和多店铺运营的 Agent 原生多账号浏览器平台。它统一管理长期隔离的浏览器 Profile、设备环境、账号身份、经营对象、浏览器实例和网站 SKILL，让 Agent 与人类在同一真实浏览器现场中完成可观察、可接管、可核验的网站运营任务。**

WebEnvoy 不是单纯的指纹浏览器，也不是单纯的浏览器自动化框架。它由四部分共同构成：

```text
WebEnvoy
  = 长期受管的浏览器身份与 Profile
  + Agent 可调用的浏览器及环境管理能力
  + 可复用的网站 SKILL 与共享知识资产
  + 人类可管理、观看、授权和接管的 App
```

## 1.2 目标用户

WebEnvoy 主要服务以下用户：

- 需要运营多个社媒账号、创作者账号、店铺账号的个人或团队；
- 需要使用真实登录状态完成网站任务的 Agent；
- 需要长期维护账号环境，而不是每次创建一次性浏览器会话的用户；
- 需要同时运行多个隔离账号环境，并能随时查看、暂停和人工接管的运营人员；
- 需要把网站经验沉淀为可安装、可版本化、可修复 SKILL 的开发者和维护者。

## 1.3 核心场景

V1 重点围绕以下场景设计：

- 管理多个社媒、店铺、创作者后台账号；
- 为每个账号维护长期、独立、可复用的浏览器环境；
- Agent 在指定 Profile 中使用真实登录态完成读取、编辑、上传、发布、修改等运营工作；
- 人类在 App 中查看 Agent 正在使用哪个账号、Profile、经营对象和实例；
- 用户在必要时接管同一实例，处理登录、验证码、复杂编辑或异常；
- 将网站入口、账号识别、经营对象识别、页面语义、操作流程、验证方式和恢复方式沉淀为 SKILL；
- 从账号、Profile、实例和活动四个角度管理运营资源。

## 1.4 核心价值

WebEnvoy 的差异化不应停留在“可以启动多个浏览器”或“提供 MCP”。

它需要同时交付：

1. **长期身份连续性**：同一 Profile 跨重启继续使用同一套环境、存储和账号身份。
2. **明确隔离**：不同 Profile 的 Cookie、存储、下载、设备环境和控制权不会混淆。
3. **Agent 原生管理**：Agent 不仅能操作网页，还能在授权范围内管理环境、Profile、账号体系、实例和 SKILL。
4. **网站知识复用**：同一个网站和场景不需要每次重新探索。
5. **人机共用现场**：人和 Agent 使用的是同一个真实实例，不是两份相似但独立的页面。
6. **结果可信**：系统能区分成功、失败、尚未完成和结果未知，不因断线或重连重复写入。
7. **本地资源所有权**：用户的 Profile、登录状态和浏览器数据由 WebEnvoy 在本地管理，不依赖外部托管 Profile。

---

# 2. 总体产品原则

## 2.1 已确认原则

1. 所有正式可运行的 Profile 必须由 WebEnvoy 创建或迁入后管理。
2. 外部 Profile 只能作为导入来源，不作为长期挂载资源。
3. Profile 权限完全来自显式配置，不根据名称、标签、用途或历史行为推断。
4. 一个 Profile 可以包含多个不同账号体系的账号。
5. 同一个 Profile 内，严格禁止存在同一账号体系的两个独立账号。
6. 账号与店铺、主页、频道等经营对象必须分开建模。
7. Provider 程序的来源与 Profile 数据的所有权是两回事。
8. Profile 创建时绑定 Provider；运行时不得静默切换 Provider。
9. 跨 Provider 或不兼容版本变更必须通过显式迁移完成。
10. App 展示和操作的必须是原浏览器实例，不得重新加载 URL 冒充镜像。
11. App 与 Agent 必须使用同一套 Runtime、管理接口和状态。
12. Agent 必须能够在授权范围内管理环境，而不只是操作网页。
13. 网站知识以标准 Agent SKILL 为主要载体。
14. AccountSystem 等跨 SKILL 共享资产必须独立复用，不能埋在单个任务 SKILL 内形成多份真相。
15. SKILL、Runtime 能力和授权必须是三个不同层次。
16. 连接状态、实例状态、控制状态、Run 状态和外部业务结果必须分离。
17. WebEnvoy 不承诺不可检测、不封号或绕过平台安全机制。
18. 自然交互必须服务真实任务，不得演变为养号、虚假互动或无意义行为伪装。
19. Provider 是用户选择：新增 Provider 增加选择，不替换现有 Provider；项目工程优先级、产品推荐、用户新建默认偏好和 Profile 实际绑定必须分离。
20. Provider 的协议、启动和可接受能力差异可以由 Driver 适配或准确展示；核心浏览器能力缺失不得包装为普通兼容任务。
21. WebEnvoy 可以适配、管理、约束、组合、观察和验证 Provider 已经具备的浏览器能力，但“适配”只包括供应方正式配置、参数和协议的调用、WebEnvoy 自有 Driver 的协议／语义转换、权限检查、环境 bundle 选择、生命周期、等待、结果与恢复；不得改写供应方程序、资源、驱动实现或 bundle，也不得通过 Harbor、Driver、App、Plugin、SKILL、安装脚本或站点脚本实现、模拟或长期补偿 Provider 缺失的浏览器核心语义。只改 JS、只读路径、复制后改写、副本 bundle、安装时补丁和运行时 monkey-patch 同样属于改写，不因范围很小或不改变页面表面而获准；也不得以自维护浏览器 fork 或内核补丁链作为当前愿景的交付路线。供应方正式发布的浏览器／驱动组合是该供应方的正式组合，不得冒称为未经供应方支持的通用 Mozilla 浏览器。

## 2.2 V1 收敛约束

1. 一个账号最多归属一个正式、可运行的 Profile。
2. 一个 Profile 同一时间最多运行一个浏览器主实例；实例内可以存在多个窗口或标签页。
3. 普通 Profile 复制只复制环境模板，不复制账号绑定和登录状态。
4. 新发现账号不会自动建立正式绑定。
5. 控制权先按实例粒度管理，不在 V1 引入标签页级复杂租约。
6. 首期优先支持一个主要 Agent 宿主。
7. App 任务管理收敛为活动监督、待处理事项、人工接管和结果查看，不建设完整任务编排平台。
8. 多实例界面先实现单实例主视图和多实例概览，再根据原型结果增加分屏或网格。
9. Camoufox 保留首个工程验证对象及其已验证范围；这项工程顺序不阻止由真实 Work Item 约束范围的其他 Provider 验证，也不开放无边界横向集成。
10. 任务页引用与原生选中／前台焦点事实分离；读、列举和诊断不得为了补焦点事实而激活页面，人机交还后默认回到交还前的可信任务页重新观察。

## 2.3 待原型验证

1. Camoufox 是否达到推荐 Provider 的稳定性和人工使用标准。
2. Camoufox 多实例的 CPU、内存和长期稳定性。
3. Camoufox Profile 环境配置跨重启、跨版本是否保持一致。
4. 供应方正式发布且版本固定的 Camoufox／Playwright 组合是否满足首批社媒和店铺场景。
5. macOS、Windows、Linux 下的实例观看和输入转发方案。
6. 多实例实时画面的刷新率、资源消耗和交互延迟。
7. Agent 安装、更新和修复 Provider 的安全流程。
8. CloakBrowser BYOL 是否值得作为长期可选 Provider 维护。
9. Camoufox 是否适合长时间人工浏览，而不只是短时接管。
10. 用户自定义 AccountSystem 的身份识别覆盖范围。

## 2.4 长期目标

- 完整 Provider 检测、安装、更新、修复和回滚体验；
- 多 Agent 宿主的统一安装与连接；
- SKILL overlay、fork、修复草稿和贡献流程；
- 更丰富的多实例布局；
- 团队协作、排期和批量运营能力；
- 在满足严格约束后，重新评估同一账号多 Profile；
- 可配置的新账号自动登记策略；
- 更完整的 Library 工作台。

## 2.5 明确非目标

V1 不以以下内容为目标：

- 通用自主 Browser Agent；
- 自建大模型对话产品；
- Hosted Browser 或 Browser-as-a-Service；
- 外部 Profile 直接挂载；
- 同账号多 Profile 并行运行；
- 任意 Provider 热切换；
- 多种浏览器内核真正嵌入 Electron；
- 大型可视化工作流编排器；
- 完整运营排期和批处理平台；
- 复杂组织 RBAC 和多级审批；
- SKILL Marketplace；
- 自动养号、虚假互动和批量账号注册；
- 验证码绕过；
- 无限重试；
- 保证不可检测或保证不会封号；
- 为规避平台规则而自动轮换指纹、代理或身份环境。
- 修改供应方浏览器或驱动程序、资源、CSS／Juggler／JS、副本 bundle，或以安装时补丁、运行时 monkey-patch 维持核心语义。
- 本轮纠偏不建立无补丁移植的自动升级或自动下载更新平台；跨窗口无缝续接、统一拖放等未有界的现场编排也不构成本轮验收。

---

# 3. 核心对象模型

## 3.1 `AccountSystem`：账号体系

账号体系表示多个域名、产品和后台共享同一登录身份的关系。

例如：

```text
Google 账号体系
  ├── accounts.google.com
  ├── gmail.com
  ├── drive.google.com
  ├── youtube.com
  └── studio.youtube.com
```

“同一站点”不得简单等同于单个域名。

### 公共模板

WebEnvoy 或社区可以提供 AccountSystem 公共模板，包含：

- 稳定 ID；
- 显示名称；
- 相关域名、产品和后台入口；
- 登录入口；
- 可选的账号识别方法；
- 已知的共享登录关系；
- 模板来源和版本。

### 用户本地定义

用户导入模板后，必须形成自己的本地定义。用户可以：

- 修改名称；
- 增删域名或产品；
- 增加自有后台；
- 修改识别规则；
- 从零创建新体系；
- 拒绝或选择性合并公共模板更新。

运行时必须以用户本地定义为准。公共模板更新不得静默覆盖本地修改。

### 变更检查

合并、拆分或修改 AccountSystem 时，系统必须检查是否导致：

- 同一 Profile 出现同体系两个账号；
- 一个账号被推导为归属多个可运行 Profile；
- 既有 Account 发生歧义；
- 现有业务任务的账号要求失去明确归属。

发生冲突时，系统必须要求显式处理，不得自动删除绑定或清理 Cookie。

---

## 3.2 `Account`：账号

Account 表示某个 AccountSystem 中的一个身份。

```text
Account
  = AccountSystem
  + 稳定账号标识
  + 用户可识别名称
  + 身份观测与验证状态
```

账号标识必须区分：

- 用户声明；
- 系统观测；
- 当前实例验证；
- 历史验证。

系统不得仅凭昵称、展示名或模糊文本自动合并账号。

### V1 唯一归属约束

**[V1 收敛约束]**

> 一个 Account 最多归属一个正式、可运行的 Profile。

这项约束用于简化：

- Agent 选择 Profile；
- 权限解释；
- 登录状态展示；
- 并发控制；
- Provider 迁移；
- 故障恢复；
- 用户理解。

它只约束 WebEnvoy 管理的环境，不表示用户不能在其他设备或软件中登录同一账号。

---

## 3.3 `BusinessTarget`：经营对象

BusinessTarget 是账号有权管理的实际业务资源，例如：

- 店铺；
- 主页；
- 频道；
- 组织；
- 广告账户；
- 商品空间；
- 内容空间；
- 项目；
- 创作者主体。

一个 Account 可以管理多个 BusinessTarget。

任务必须能够明确绑定：

```text
AccountSystem
→ Account
→ Profile
→ Instance
→ BusinessTarget
→ Operation
```

对于写入任务，系统必须同时核对：

1. 当前实际登录 Account；
2. 当前页面的 BusinessTarget。

“账号正确”不等于“店铺或主页正确”。

---

## 3.4 `Profile`：长期浏览器身份容器

Profile 不是单纯的数据目录，而是一套长期环境和账号归属。

```text
Profile
  ├── ProviderBinding
  ├── ProfileData
  ├── EnvironmentConfiguration
  ├── PermissionPolicy
  ├── AccountBindings
  ├── BusinessTargets
  ├── InstanceHistory
  └── RecoveryState
```

### Profile 与账号的硬约束

```text
一个 Profile
  ├── 可以包含 Google 账号 A
  ├── 可以包含社媒平台账号 B
  ├── 可以包含店铺平台账号 C
  └── 不得包含第二个 Google 账号 D
```

同一个 Profile 在同一个 AccountSystem 中最多绑定一个独立 Account。

### 日常 Profile 与 Agent Profile

用户日常使用的浏览器环境也必须作为同类 WebEnvoy Profile 管理。

- 可以人工启动；
- 可以在授权范围内供 Agent 使用；
- 使用相同的权限、实例、账号和环境模型；
- “日常”“工作”“Agent 专用”只能作为标签；
- 标签不得产生隐含权限。

---

## 3.5 `ProviderExecutable` 与 `ProviderBinding`

### ProviderExecutable

表示浏览器程序本身。

ProviderExecutable 可以：

- 由用户预先安装；
- 由 WebEnvoy 协助下载和验证；
- 位于系统标准安装目录；
- 独立于 Profile 存在；
- 按供应方规则单独更新；
- 由供应方以正式浏览器／驱动组合发布并提供来源、版本和完整性线索。

WebEnvoy 可以核对、选择和调用供应方的正式组合，但不得把供应方程序、资源或驱动 bundle 复制后改写，也不得把供应方组合冒称为通用 Mozilla 浏览器。

### ProviderBinding

表示某个 Profile 与某个 Provider 家族及兼容版本策略的绑定。

```text
ProviderBinding
  ├── provider_id
  ├── executable_ref
  ├── version
  ├── compatibility_range
  ├── capability_facts
  └── migration_state
```

`ProviderBinding` 是 Profile 的正式绑定；它不等同于历史的私有 patch binding。正式绑定可以记录供应方版本、来源、协议和环境 bundle 引用，但不记录或启用 WebEnvoy 改写供应方核心语义的补丁。

### 所有权原则

```text
Provider executable
  可以是外部安装或 WebEnvoy 管理下载

Profile data
  必须由 WebEnvoy 创建和管理
  不使用 Provider 的日常默认目录
  不挂接外部软件正在使用的 Profile
  不允许两个管理者同时写入
```

Provider 的程序、资源和驱动组合仍由供应方发布和负责；WebEnvoy 只管理受管 Profile 数据、正式配置／协议引用、Driver 转换、权限、环境 bundle、生命周期和结果。供应方更新不自动移植或重新启用历史私有补丁。

使用用户已安装的 Chrome 或 Camoufox 是允许的；直接使用其日常默认 Profile 不是允许的。

---

## 3.6 `EnvironmentConfiguration`

EnvironmentConfiguration 表示 Profile 的设备与网络环境。

至少包括：

- Provider 与浏览器家族；
- 浏览器版本策略；
- 设备与指纹配置；
- 代理引用；
- 实际网络出口观测；
- 地区；
- 语言；
- 时区；
- 屏幕与窗口环境；
- GPU／WebGL；
- CPU／内存表征；
- WebRTC；
- 字体；
- Canvas、Audio 等 seed；
- 扩展策略；
- 交互策略。

环境配置必须区分三个状态：

```text
configured：用户或 Agent 已保存的目标配置
effective：当前活动实例实际生效的配置
pending：已保存但尚未应用，需重启或迁移
```

不得将“配置已保存”直接等同于“活动实例已生效”。

---

## 3.7 `Instance`：浏览器实例

Instance 是某个 Profile 当前运行的浏览器进程和现场。

```text
Instance
  ├── instance_id
  ├── profile_id
  ├── provider_binding
  ├── process_state
  ├── viewer_state
  ├── pages / tabs
  ├── ControlLease
  └── related Runs
```

**[V1 收敛约束]**

> 一个 Profile 同一时间最多运行一个浏览器主实例。

实例内部可以存在多个标签页和窗口。矩阵并发通过多个不同 Profile 的实例实现。

---

## 3.8 `AgentPrincipal`

AgentPrincipal 表示稳定的调用主体，例如：

- 某个本地 Agent 宿主；
- 某个插件安装；
- 某个自动化程序；
- 某个上游系统身份。

Principal 不应因一次连接断开而消失。

```text
AgentPrincipal
  ├── principal_id
  ├── display_name
  ├── principal_type
  ├── trust_state
  ├── created_at
  └── revoked_at
```

---

## 3.9 `AgentConnection`

AgentConnection 表示 Principal 的一次实际连接。

```text
AgentConnection
  ├── connection_id
  ├── principal_id
  ├── host_type
  ├── transport
  ├── local_or_remote
  ├── connected_at
  ├── last_seen_at
  └── connection_state
```

一个 Principal 可以重连并产生新的 Connection。

---

## 3.10 `Grant`

Grant 表示用户授予 Principal 的能力范围。

```text
Grant
  ├── subject_principal
  ├── profile_scope
  ├── browser_operation_scope
  ├── environment_management_scope
  ├── account_management_scope
  ├── skill_management_scope
  ├── provider_management_scope
  ├── destructive_operation_scope
  ├── valid_from
  ├── expires_at
  └── revoked_at
```

V1 不需要复杂组织 RBAC，但必须能够回答：

- 谁在调用；
- 通过哪条连接；
- 可以访问哪些 Profile；
- 可以执行哪些浏览器操作；
- 可以修改哪些环境；
- 可以管理哪些账号体系；
- 能否创建 Profile；
- 能否执行破坏性操作；
- 授权是否过期或撤销。

---

## 3.11 `ControlLease`

ControlLease 表示当前谁有权向某个 Instance 发送输入。

```text
ControlLease
  ├── instance_id
  ├── holder_type: human | agent
  ├── holder_id
  ├── acquired_at
  ├── heartbeat_at
  ├── expires_at
  └── lease_state
```

观看画面不需要持有 ControlLease。点击、输入、导航、上传等改变实例状态的操作必须持有有效 Lease。

**[V1 收敛约束]** ControlLease 先按 Instance 粒度管理。

---

## 3.12 `Run`

Run 是一次具体执行的持久化事实。

Run 可以是：

- 环境管理操作；
- 浏览器会话操作；
- 业务任务；
- 对外写入；
- 状态对账。

```text
Run
  ├── run_id
  ├── principal / connection
  ├── profile
  ├── account
  ├── business_target
  ├── instance
  ├── skill
  ├── authorization
  ├── state
  ├── result
  ├── failures
  └── external_operations
```

推荐状态：

```text
accepted
→ running
→ succeeded | failed | unknown_outcome | manual_recovery_required
```

终态不得被随意覆盖。

---

## 3.13 `ExternalOperation` 与 `ExternalOutcome`

ExternalOperation 表示可能已经在网站侧发生的动作，例如：

- 上传；
- 发布；
- 修改；
- 删除；
- 保存；
- 发送；
- 提交。

ExternalOutcome 必须独立于 Run 连接和实例状态表达：

```text
not_started
requested
accepted
running
succeeded
failed
unknown
```

断线、实例退出或用户停止后续操作，不得被解释为已经撤销网站侧动作。

---

## 3.14 `Activity`

Activity 是面向 App 的人类可理解工作摘要，可以聚合一个或多个 Run。

例如：

- Agent 正在更新某个店铺商品；
- 正在等待用户登录；
- 已完成一篇内容发布；
- 某个结果需要人工核对。

Activity 不是 Run 的替代品，也不拥有独立业务真相。

---

## 3.15 `SKILL`

SKILL 是 Agent 可读取的网站知识、工作流和工具使用说明。

它不是：

- 权限授予文件；
- 浏览器运行时；
- 自动执行的可信程序；
- Core 自动解释的 DSL；
- AccountSystem 的唯一容器。

---

# 4. 权限与授权模型

## 4.1 有效权限公式

有效权限不得使用“叠加”理解，应使用交集：

```text
有效权限
= Profile 权限上限
∩ AgentPrincipal / Grant 授权
∩ 当前任务授权范围
∩ 当前运行时安全约束
```

其中：

- Profile 权限上限定义该环境允许做什么；
- Grant 定义当前 Principal 被允许做什么；
- 当前任务进一步限制本次目标；
- Runtime 根据身份、控制权、页面状态、环境状态和外部风险判断当前动作能否执行。

## 4.2 动作确认

具体动作确认不是新的权限来源。

```text
动作确认
= 在有效权限交集内，对一次具体动作作出明确决定
```

因此：

- 任务授权不能突破 Profile 上限；
- Grant 不能被 SKILL 扩大；
- 一次确认不能永久提高 Profile 权限；
- 历史成功不能推导当前授权；
- Agent 不能通过创建新 Profile 为自己提权；
- Agent 不能通过修改任务参数扩大自己的 Grant；
- SKILL 不参与权限计算。

## 4.3 使用权与管理权

至少区分：

### 浏览器使用权

- 查看状态；
- 启动或复用实例；
- 导航；
- 读取；
- 点击；
- 填写；
- 上传；
- 执行已授权网站任务；
- 请求接管。

### 环境管理权

- 修改代理；
- 修改语言、时区或设备配置；
- 迁移 Provider；
- 修改 Account 绑定；
- 修改 Profile 权限；
- 安装或更新 Provider。

### 破坏性和敏感权限

- 删除 Profile 数据；
- 清理 Cookie 和站点存储；
- 导出敏感状态；
- 删除 AccountSystem；
- 扩大 Agent Grant；
- 强制迁移；
- 删除恢复备份。

允许网页操作不得自动推导出环境管理权。

## 4.4 创建 Profile 的授权

新 Profile 尚不存在，因此创建权限必须来自 Principal 的管理 Grant，而不能来自 Profile 自身。

Agent 创建 Profile 时：

- 只能使用允许的 Provider；
- 只能引用已授权的代理；
- 只能使用用户批准的环境和权限模板；
- 不得自行创建更宽松权限；
- 不得自动导入敏感数据；
- 不得绕过账号唯一归属约束。

---


## 4.5 深层浏览器能力的授权

**[已确认原则]**

> Runtime 是否具备某项浏览器能力，与当前 Agent 是否被允许发现、读取或执行该能力，是两个不同问题。

Network、Console、受控脚本、存储、下载和其他深层浏览器能力必须继续遵守有效权限交集，不得因为被归类为“调试”“诊断”或“内部能力”而绕过授权。

至少区分：

- 低风险元数据观察；
- 可能包含业务或敏感内容的内容读取；
- 会改变请求、页面或浏览器状态的拦截、脚本和存储操作；
- 破坏性或敏感数据操作。

SKILL 可以声明任务需要的 Runtime capability，并建议 Agent 只呈现或优先使用其中一部分工具；这属于能力选择与任务指导，不是新的权限来源。SKILL、工具隐藏、宿主批准和 allowed-tools 元数据均不得替代 Core／Runtime 的正式授权检查。

Cookie 原文、密码、验证码、token、未脱敏凭据和 raw DevTools／CDP／Juggler endpoint 不作为普通 Agent 浏览器能力直接暴露。

底层协议可以由 WebEnvoy 自有 Driver 做有界转换，并继续经过授权、Profile、环境和 ControlLease 检查；协议转换不授权修改供应方程序、资源或驱动，也不构成绕过供应方原生能力的后门。

# 5. 账号、Profile 和经营对象规则

## 5.1 一账号一 Profile

**[V1 收敛约束]**

一个 Account 最多归属一个正式、可运行 Profile。

需要迁移时，使用显式迁移流程，而不是先复制出第二个并行环境。

## 5.2 一个 Profile 可有多个账号

一个 Profile 可以绑定多个不同 AccountSystem 的 Account。

例如：

```text
Profile P
  ├── Google / account-a
  ├── 小红书 / account-b
  └── 店铺平台 / account-c
```

## 5.3 同体系单账号

**[已确认原则]**

同一个 Profile 在同一个 AccountSystem 内最多绑定一个独立 Account。

这项约束必须同时落实在：

- 数据模型；
- 创建和导入；
- 账号绑定；
- Agent 操作；
- 现场身份验证；
- Provider 迁移；
- Profile 恢复。

## 5.4 Account 与 BusinessTarget

一个 Account 可以对应多个 BusinessTarget。切换店铺、主页或频道不必然意味着切换登录 Account。

SKILL 和 Runtime 必须根据站点实际语义区分：

- 账号切换；
- 经营对象切换；
- 页面导航；
- 权限不足；
- 身份冲突。

---

# 6. 新站点登录和身份冲突

## 6.1 基本原则

用户或 Agent 在 Profile 中登录此前未登记的站点时：

> **保留浏览器自然产生的 Cookie、存储和登录状态；更新观测事实；不自动修改正式 Account 绑定、AccountSystem、Profile 权限或 Grant。**

必须区分：

```text
浏览器数据发生变化
≠ WebEnvoy 管理配置自动发生变化
```

## 6.2 处理规则

| 现场情况 | 默认处理 |
|---|---|
| 已知 AccountSystem，账号与现有绑定一致 | 更新登录状态和观测时间 |
| 已知 AccountSystem，但当前 Profile 尚未绑定账号 | 创建 discovered identity，等待显式登记 |
| 站点未归入任何本地 AccountSystem | 创建未归类登录发现 |
| 当前账号与 Profile 绑定账号不同 | 报告身份冲突，停止依赖该身份的操作 |
| 当前账号已归属另一个 Profile | 报告归属冲突，停止依赖该身份的操作 |
| 同一 AccountSystem 观察到多个账号 | 报告同体系多账号冲突 |
| 无法可靠识别身份 | 标记 unknown，不猜测、不自动绑定 |

## 6.3 V1 不自动建立正式绑定

**[V1 收敛约束]**

可靠观测到新账号后：

```text
发现账号
→ 保留登录状态
→ 创建 discovered identity
→ 不建立正式 Account/Profile 归属
→ 提供显式登记和绑定操作
```

如果用户的任务本身明确包含“登记并绑定该账号”，则可以在有效授权范围内完成，不需要额外把同一意图拆成重复操作。

## 6.4 默认不进行的动作

系统不得自动：

- 删除 Cookie；
- 清理站点存储；
- 退出账号；
- 覆盖现有绑定；
- 把账号从其他 Profile 迁移过来；
- 创建 AccountSystem；
- 扩大 Profile 权限；
- 扩大 Principal Grant；
- 把 unknown 标成 verified。

## 6.5 冲突影响范围

身份冲突只阻止依赖该身份的操作。

- 指定账号发布或修改必须停止；
- 公开页面读取可以继续；
- 与其他 AccountSystem 无关的任务不应被全局停止；
- 是否允许继续由有效权限和当前任务要求共同决定。

---

# 7. Profile 生命周期

## 7.1 创建

```text
用户或 Agent 请求创建
→ 检查管理 Grant
→ 解析显式选择／用户新建默认／人类确认的推荐预选
→ 检查 Provider 可用性与使用授权
→ 选择环境模板和权限模板
→ 创建 WebEnvoy 管理的数据目录
→ 生成并持久化设备环境
→ 启动浏览器
→ 完成登录
→ 发现账号
→ 显式登记和绑定
→ 正常使用
```

创建时的 Provider 选择必须遵守：

- 显式选择优先于用户的新建默认偏好；
- 两者都必须实际可用并处于有效授权内；
- 无用户默认时，人类入口可以显示可修改的产品推荐预选，但必须让用户确认最终选择；
- Agent 既未明确指定、又没有用户默认时，必须返回需要选择，不得暗用项目推荐；
- 显式选择或用户默认不可用／未获授权时，返回局部诊断和可选处理，不得静默换用其他 Provider。

用户新建默认偏好只影响后续创建，不修改既有 Profile／Instance、环境、账号绑定或 Grant。设置默认不授予安装、使用、迁移、管理或扩大 Grant 的权限；Agent 修改该偏好必须有明确管理授权。

## 7.2 外部 Profile 导入

固定语义：

> **从用户选择的外部环境读取可迁移数据，创建新的 WebEnvoy Profile；导入完成后，运行不再依赖外部原目录。**

不得：

- 挂载外部默认目录；
- 使用符号链接长期共享；
- 与外部软件同时写同一 Profile；
- 把外部活动实例直接当成 WebEnvoy Instance。

## 7.3 部分导入

导入必须报告：

- 成功迁移的数据；
- 未迁移的数据；
- 需要重新登录的站点；
- 不兼容扩展；
- 无法转换的配置；
- 账号归属冲突；
- 需要用户处理的事项。

“部分成功并明确缺口”优于“宣称完整成功但实际不可用”。

导入失败不得阻止用户创建空 Profile 并手动登录。

## 7.4 普通复制

普通复制只复制环境模板，不复制账号身份。

可复制：

- Provider 类型建议；
- 非敏感环境模板；
- 标签和组织信息；
- 权限模板；
- 代理引用是否复制由用户明确决定。

不得默认复制：

- Cookie；
- 登录存储；
- Account 绑定；
- 凭据；
- 未完成 Run；
- 授权决定；
- 活动 Instance；
- ExternalOperation 状态。

## 7.5 Provider 迁移

跨 Provider 或不兼容版本变更必须走显式迁移：

```text
创建目标迁移环境
→ 迁移兼容数据
→ 报告不兼容内容
→ 必要时重新登录
→ 验证 Account 与 BusinessTarget
→ 切换正式归属
→ 原环境转为恢复备份
```

迁移完成前，目标环境不得作为第二个正式可运行的同账号 Profile。

版本升级的最小流程是：

```text
供应方正式浏览器／驱动组合
→ 来源、版本、完整性和兼容性核对
→ 核心用户路径验证
→ 用户明确选择
→ 更新 ProviderBinding
```

未知来源、版本、完整性或兼容性不得进入运行路径，必须局部拒绝并给出诊断。活动 Instance 不得热换；不得自动把旧 Profile／账号数据交给新 Provider，必须由用户发起并经检查的显式迁移决定；不得自动回退、把旧私有补丁移植到新版本，或因失败改写账号、指纹、代理、权限和授权历史。

## 7.6 恢复备份

恢复备份不是第二个可运行 Profile。

- 默认不得正常启动；
- 不参与账号归属；
- 只用于迁移失败或数据恢复；
- 恢复为可运行 Profile 时必须重新执行归属和一致性检查。

恢复、查询和对账不得回滚已撤销的授权、账号归属或 Run／receipt 历史。

## 7.7 Provider 不可用

Provider 不可用时：

- 不静默切换；
- 不自动重新生成指纹；
- 不自动更换代理；
- 返回明确诊断；
- 提供修复、更新或迁移选项；
- 不因 Provider 故障改写已经发生的 ExternalOutcome。
- 不把不可用的显式选择或用户默认替换为产品推荐；既有 Profile 仍保留原 ProviderBinding。

---

# 8. 运行中配置变更

## 8.1 变更类型

| 变更类型 | 应用语义 |
|---|---|
| 名称、标签、备注、分组 | 可以立即生效 |
| 权限收紧、Grant 撤销 | 对后续动作立即生效；在途动作在安全边界停止 |
| 权限扩大 | 必须由有权管理该资源的 Grant 执行 |
| 代理、时区、语言、设备、指纹相关配置 | 保存为 pending，在安全重启后生效 |
| Provider 家族或不兼容版本 | 走迁移 |
| Account 绑定变化 | 活动身份任务停止并重新验证 |
| BusinessTarget 变化 | 影响写入任务时必须重新确认目标 |

## 8.2 不得静默热变更

在活动实例或业务任务进行中，不得静默改变：

- 代理；
- 网络出口；
- 时区；
- 语言；
- 指纹 seed；
- GPU／WebGL；
- 屏幕设备环境；
- Provider；
- Account 绑定。

App 保存配置成功，不等于当前 Instance 已经应用。

## 8.3 配置展示

App 和 Agent API 应同时表达：

- configured；
- effective；
- pending；
- drift；
- last_verified_at。

---

# 9. Provider 策略

## 9.1 Provider Qualification Gate、支持范围与推荐

### 先分类，再确定工作范围

1. **接口差异**：Provider 已经提供真实能力，只是协议、启动或调用方式不同，由 Driver 适配。
2. **可接受能力差异**：限制不会破坏已承诺的用户结果，准确展示并局部处理；不要求所有 Provider 完全同等级。
3. **核心浏览器能力缺失**：实现已承诺结果需要 WebEnvoy 自行建设或模拟底层浏览器行为，停止采用，不转成普通 Driver 任务。

核心浏览器能力至少包括浏览器渲染与命中、键盘和 IME、窗口／弹窗／对话框、下载、Web Storage／IndexedDB、站点权限，以及浏览器级设备身份。边界按实际承担了什么能力判断，不按代码位于 Driver、SKILL 或安装脚本、也不按使用的语言判断；只改 JS、只读、CSS／Juggler、复制 bundle、安装时补丁或运行时 monkey-patch 仍是改写供应方能力。

### 固定资格顺序

```text
产品场景与职责边界初筛
→ 固定版本文档／源码、供应方正式组合和最小黑盒核对
→ 必要的可丢弃适配 spike
→ 有证据的采用／不采用决定
→ 正式 Driver、注册、合同、App／Plugin 与安装交付
```

前置核对必须回答：长期账号状态是否由浏览器可靠提供；必要人工操作是否有原现场路径；关键输入、弹窗和文件行为是否真实存在；接口是否空返回或自动替用户决定；供应方来源、正式组合、版本和完整性是否可核对；安全和版本路径是否可接受。接口存在或命令成功不是资格证据；也不要求零 Driver、零成本或所有站点完全兼容。

每次 spike 开始前必须写清产品问题、已有能力证据、允许范围和输出决定。资格未通过时，不把候选加入正式 Provider enum、持久合同、用户安装、App 选择项或支持承诺。发现必须补核心浏览器语义、维护 fork／内核补丁链，或放弃身份隔离、可信控制与结果真实性时立即停止；普通执行者不得以“临时兼容”续接。

### 接入底线与允许的正常工作

一个可选 Provider 必须能通过 WebEnvoy Driver 与既有 owner 路径满足其声明范围内的底线：Profile 身份与数据不混淆；ProviderBinding、可用性和授权可查询；人与 Agent 使用同一原 Instance且输入服从 ControlLease；结果、失败和 `unknown` 可信；失败局部且保留用户数据；版本、构建、许可、分发和安全边界可核对。

WebEnvoy 可以且应继续：

- 对接 Provider 已有协议、启动方式及页面、输入、文件、网络和窗口接口；
- 调用供应方正式配置、参数和协议，并在 WebEnvoy 自有 Driver 中做必要的协议／数据转换；
- 管理受管目录、进程、Profile／账号归属、授权、ControlLease、结果与恢复；
- 保存并精确复用 Provider 已支持的配置、seed 或官方生成结果，核对实际生效与漂移；
- 管理 Provider 原生持久数据的生命周期、备份、导入、迁移和版本兼容；
- 转发原实例画面与可靠输入并提供可信人类入口；
- 在不改变浏览器语义时组合调用、等待状态、诊断和处理失败。

这不要求 Provider 自带 WebEnvoy 的管理体系，也不禁止不同协议、配置持久化或非原生有头窗口；但 WebEnvoy 不得修改供应方程序、资源、驱动实现或 bundle，不得在安装或运行时注入补丁，不得把只读／JS／副本改写包装成合规接入。配置管理不能演变为自建设备身份系统，数据管理不能实现缺失的网页存储引擎，画面和输入转发不能自行判断浏览器渲染层叠或模拟输入系统。

### 能力与支持范围

每个 Provider／Driver 必须按版本和平台分别声明 capability 的 `supported`、`limited` 或 `unsupported`。未测试不等于 `unsupported`；支持状态、证据来源、运行可用性和授权状态分别表达。不同 Provider 不要求完全相同的能力等级；某候选受限不能删除 V1 Runtime 能力类别，也不能反向要求 WebEnvoy 补其核心缺口。声明的组合必须是供应方正式发布、来源可核对且未经 WebEnvoy 改写的组合。

人工使用要求用户能观看并操作原实例、完成必要浏览与接管；可以是原生窗口或原实例画面与可靠输入，只有截图不能证明人工交互通过。

### 产品推荐考虑因素

产品推荐可以综合免费／付费与并发成本、开源和可审计程度、目标平台、资源开销、网站兼容性、环境控制、维护活跃度、版本／构建／升级质量及分发许可。这些是推荐权衡，不机械升级为每个可选 Provider 的同一准入条件；推荐不是授权、用户设置或 ProfileBinding。

## 9.2 Camoufox

**[待原型验证]**

Camoufox 是 WebEnvoy 的首个工程验证 Provider，并已按其正式证据限定支持范围；它不是产品永久指定默认，也不是其他 Provider 接入的能力模板或统一前置。当前验证对象是供应方正式发布、来源和版本可核对的 Camoufox／Playwright 组合，不是 WebEnvoy 复制、改写后的浏览器或驱动，也不是可随意替换的通用 Mozilla 浏览器。

目标不是把它称为“完美 Provider”，而是验证它是否能同时满足：

- 多 Profile 独立运行；
- 长期环境一致性；
- 人工浏览和接管；
- Agent 自动化；
- 目标社媒和店铺兼容性；
- 多实例观看；
- 版本升级和恢复；
- 无付费并发门槛。

WebEnvoy 使用 Camoufox 时必须保持自洽的 Firefox 身份，不得把它伪装成 Chromium。

Camoufox 默认生成的随机设备配置和 seed 不应每次重新生成。WebEnvoy 必须在 Profile 创建时保存完整环境配置，并在后续启动时复用。

保留供应方原生环境生成结果和受管 JSON 的精确复用；不得为接入、升级或失败恢复重新随机身份、移植私有补丁或改变供应方原生行为。

## 9.3 官方 Chrome

**[已确认原则]**

官方 Chrome 作为显式可选的兼容性 Provider，与其他满足底线的选择使用相同的绑定、授权、诊断和恢复规则。

用途包括：

- 网站兼容性；
- 人工浏览基线；
- 某些扩展或浏览器行为需求；
- Provider 对比和诊断。

Chrome 不具备同等级的原生设备环境控制能力，但不得因此被自动判定为不可用，也不得被固定为只能在故障时使用的次等回退路径。

## 9.4 CloakBrowser

**[待评估／长期可选]**

CloakBrowser 不作为免费多实例默认底座，也不作为 WebEnvoy 必需依赖。

未来可以评估为 BYOL Provider：

- 用户自行安装；
- 用户自行持有授权；
- WebEnvoy 不随包分发其二进制；
- Profile 数据仍由 WebEnvoy 管理；
- 并发和许可限制必须如实展示。

是否长期维护该适配，不进入 V1 必须范围。

## 9.5 明确排除

- ego-lite／ego-browser：仅作为研究对象，不接入，不作为 Provider；
- Wayfern：不研究、不接入；
- 没有真实 Work Item、消费者和有界验收的横向 Provider 集成。

## 9.6 Obscura 不采用决定

**[当前愿景内明确不采用]**

在 WebEnvoy 当前愿景完成前，不采用、不继续接入 Obscura；停止研发、适配、验证、分发准备、候选跟踪与版本监控，不以待授权、新版本、重命名或临时补丁继续。原因是继续满足核心产品场景会越过 WebEnvoy 与浏览器的职责边界，需要本项目承担浏览器核心能力补偿。

这不是“已经证明永远不可用”，也不取消多 Provider、用户选择或完整 Runtime 目标。当前愿景完成不会自动重新开启；届时仍需新的显式产品决定。历史目标、实验和失败证据保留在 [#511](https://github.com/WebEnvoy/WebEnvoy/issues/511)，其 `not_planned` 关闭不表示功能验收成功。

## 9.7 Provider Driver 抽象

Harbor 的公共浏览器能力不得长期等同于 CDP、Playwright 或 Juggler 的原始协议。

Provider Driver 的职责是把供应方已经提供的能力转换为 WebEnvoy 公共语义，并承接协议、权限、Profile／环境 bundle、生命周期、等待、拒绝、结果和恢复边界。WebEnvoy 自有 Driver 可以做这种转换，但不得改写供应方 Driver 实现、程序、资源或 bundle；不得用 CSS／Juggler／JS、只读特例、复制改写、安装时补丁或运行时 monkey-patch 补上缺失的浏览器核心语义。

V1 的 Browser Runtime capability plane 至少覆盖以下主要能力类别；公共语义由 WebEnvoy 定义，Provider Driver 负责适配：

```text
Instance
  launchInstance
  closeInstance
  readInstanceState

Page / Tab / Window
  listPages
  openPage
  switchPage
  activate
  closePage
  pageInfo
  navigate
  reload
  historyNavigation
  popup / dialog facts

Observation
  snapshot
  visibleText / semanticControls
  frame / shadow-boundary facts
  screenshot / frameStream

Interaction
  click
  input
  press
  mouse / scroll
  select
  dragAndDrop
  waitForState

Files
  upload
  download
  browserDialog

Network
  observeRequests
  observeResponses
  observeFailures
  waitForNetworkResult
  readSelectedResponse
  interceptOrModifyRequest

Console / Page Runtime
  observeConsole
  observePageErrors
  evaluateControlledScript

Control / Recovery
  acquireControl
  releaseControl
  cancelOperation
  queryOperation
  readState
```

底层分别适配：

```text
Camoufox / Playwright Firefox Driver
Chrome / CDP Driver
未来经过验证的其他 Driver
```

不得为了理论通用性提前建设复杂 Provider 插件平台，也不得为了某个站点把站点知识写回 Runtime 公共能力。

### 9.7.1 任务页与原生焦点合同

任务页是用户或 Agent 本次工作的真实 Page 引用；原生标签／窗口选中、OS 前台或其他焦点事实是可选的现场观测。可信 Page 引用不要求完整的全局窗口关系或 OS 前台证明，但仍必须有供应方可验证的归属和新鲜度。二者必须分开建模，不能用焦点事实替代 Page 归属、新鲜度、权限、Account 或 BusinessTarget 核对。

- 每次读、输入、导航和写入都应绑定可验证的 Instance、Page／document 和适用身份／经营对象；页面引用失效或文档替换时，相关操作必须拒绝或重新走安全选择。
- 公共 `active`／selected 语义与供应方原生字段不兼容时必须版本化；旧消费者应准确拒绝或经验证适配，不得把任务页 ID、`false` 或其他占位值塞入旧原生 `active` 字段冒充焦点事实。
- `openPage` 必须调用供应方真实创建能力并返回真实 Page 和可用的窗口关系事实；该动作可能聚焦。若请求后台创建或不得抢前台，而 Provider 无法证明这一点，必须在派发前拒绝，不创建后再宣称满足。
- `activate` 只能明确激活用户指定的真实 Page。`list`、普通 `read`、`snapshot`、`diagnostics` 和其他只读操作不得为了补齐选中／焦点事实而激活、切页或抢前台。
- `closePage` 只能关闭已获准的目标 Page；不得借邻页、URL、标题或创建顺序猜测目标，也不得强制邻页取得焦点。若目标或最后一个可用 Page 无法可靠确认，必须拒绝；关闭最后 Page 不得隐式结束 Instance，也不得创建替代 Page。
- Viewer 必须展示明确选择的原 Instance／任务页，不冒称该页是 OS 前台。仅依赖 OS 窗口、屏幕坐标或原生键盘路由的输入，必须证明可靠窗口与画面对应，否则拒绝输入；供应方 Page／Element 对象级的 `click`、`fill`、`press` 等输入只需核验 Page／document、target/actionability、授权和 ControlLease，不额外要求全局选中或 OS 前台。
- 人类接管任务页 A 后可以修改 A、观看同一 Instance 的帮助或其他页面 B；用户明确交还后，Agent 默认重新观察 A，不自动跟随 B，也不要求先证明 A 仍是原生选中标签。
- A 关闭、失联、替换或出现身份歧义时，必须准确停止依赖 A 的动作；只有供应方可信关系，或用户明确选择且仍可验证的真实 Page，才可继续。URL、标题、内容、创建顺序或“看起来相同”不得自动认领；不得复制、reload／reopen URL、重建 Instance 或重放操作冒充恢复。
- 原生选中／前台关系未知但 Page／document 归属和读取授权仍可验证时，可以继续 Page／Element 对象级读取或诊断；对象级输入另需核验 target/actionability、授权和 ControlLease。只有依赖 OS 窗口、屏幕坐标或原生键盘路由的输入才要求可靠窗口与画面对应。任一必要事实不可靠时，只拒绝相关动作，必要时暂停该上下文，不扩大为无关资源的全局失败。

不要求所有 Provider 都支持同窗后台创建、关闭后焦点一致、完整原生窗口／选中关系或搬页后保留内部编号；这些未证事实不得被伪造。可选焦点未知不是可验证 Page 的全局失败条件。

### 9.7.2 旧私有补丁绑定退役

历史的 Provider-specific patch binding、私有 CSS／Juggler／reload／native-swap 合同和复制改写 bundle 不再是当前构建、安装、启动、CI 或 Runtime 的依赖。当前路径不得生成或执行它们，也不得以兼容模式偷偷补回；旧字符串可以作为历史、负例或拒绝诊断保留。

退役只改变可运行路径，不删除用户事实：旧绑定、Profile、备份、Run／receipt、授权记录、账号归属和 SKILL 本地修改仍可查询、管理、撤销、对账和安全恢复；旧绑定被发现时应明确提示需要选择受支持的供应方版本，不自动选择系统 Provider、新 Profile 或清理数据。恢复与升级不得把旧补丁移植回来，也不得回滚已撤销的授权或历史结果。

## 9.8 Runtime 能力完整性与 Provider 支持状态

**[已确认原则]**

> V1 先明确主要浏览器能力类别，再通过真实交付单元逐步实现和验证；不得因为当前网站或当前 Agent 暂时没有使用某一基础能力，就从 Runtime 规划中省略该能力类别。

每项公共 capability 必须能够回答：

1. WebEnvoy 的公共语义是什么；
2. 当前 Provider 是 `supported`、`limited` 还是 `unsupported`；
3. 是否已有对应的真实或确定性验证；
4. 已安装 Plugin 是否能够消费；
5. 当前主体在什么授权和运行条件下能够使用。

能力存在不等于 Agent 自动获得能力。Plugin 可以按宿主、任务、SKILL 和授权上下文减少工具呈现；Core／Runtime 仍独立执行权限、身份、ControlLease、敏感数据和 ExternalOutcome 检查。

对于明确列为 V1 必需的能力，不能仅以 `unsupported` 标记作为完成证据；应由具备真实能力的成熟 Provider 加 WebEnvoy 有界适配交付，或通过明确产品决定调整支持范围。不同 Provider 不要求具备完全相同的实现方式或能力等级；不得由 WebEnvoy 实现缺失的浏览器核心语义来制造“支持”。

## 9.9 验证证据语义

验收按用户结果选择所需证据，并明确区分：

| 证据类型 | 含义 |
|---|---|
| 确定性测试 | fixture、mock 或单元测试；证明受控逻辑，不证明真实浏览器或用户路径。 |
| 真实 Provider | 来源可核对、未经 WebEnvoy 改写的供应方正式浏览器／驱动组合与原 Instance 的实际行为。 |
| 安装路径 | 正式构建与隔离安装资产经正式接口调用；测试客户端可以证明此类证据。 |
| 真实 Agent | 真实第三方 Agent 通过已安装 Plugin 完成声明路径，不是测试脚本代发工具请求。 |
| 真人操作 | 真实人类完成观看、输入、接管等；中文 IME 不得由 `insertText` 脚本替代。 |
| 真实站点 | 记录第三方站点、版本／时间和具体场景；受控站点不算第三方站点，真实读取不证明账号登录或写入。 |

六类不是互斥等级，也不是所有操作都必须凑齐六项。保留 `fixture_verified`、`live_verified`、`plugin_verified` 的合同名称和原含义；`plugin_verified` 只用于“已安装 Plugin + 真实第三方 Agent”。安装路径、真人和真实站点上下文优先写入现有 evidence／verification 记录，现有消费者确实无法区分时才增加最小版本化字段。

`provider_claim` 只是上游声明。没有测试不等于 `unsupported`；API 返回成功、CI 通过或 PR APPROVE 都不等于用户结果、真人可用、Work Item 完成或 main 已交付。

每份验收至少记录：测试的用户结果、实际消费者、供应方来源及正式组合、固定源码与 build／config、平台及是否原实例、场景和身份／授权边界、成功／拒绝／恢复、脱敏证据地址，以及明确未执行项。任务页协作还应记录 Page／document 引用、A／B 是否分离、交还后的重新观察和失联时的拒绝理由；不得保存凭据、原始 Profile、私有业务数据或未脱敏画面。

# 10. 设备环境与自动化暴露控制

## 10.1 产品语言

产品应使用：

- 原生设备环境与指纹控制能力；
- 设备环境一致性；
- 身份连续性；
- 减少由驱动、运行环境和辅助代码不必要暴露的自动化特征；
- 自然、状态驱动的交互。

产品不应使用“不可检测”“绝不封号”等承诺。

## 10.2 Profile 作为长期设备环境

每个 Profile 应维护一套自洽、持久的环境：

- 浏览器家族；
- 浏览器版本；
- 操作系统特征；
- User-Agent 及相关属性；
- 屏幕与窗口；
- 字体；
- GPU 与 WebGL；
- CPU 与内存；
- 语言与时区；
- 地理位置；
- WebRTC；
- Audio、Canvas 等 seed；
- 代理和实际网络出口；
- 浏览器存储；
- 历史连续性。

不得对每个字段独立随机。

## 10.3 跨时间连续性

同一 Profile 重启后应继续使用：

- 同一数据目录；
- 同一设备环境配置；
- 同一关键 seed；
- 同一 Provider 家族；
- 同一 Account 绑定；
- 可解释的浏览器版本升级。

版本升级可以导致合理变化，但必须经过兼容性验证。

## 10.4 分工

### Provider

负责：

- 浏览器内核；
- 原生设备环境能力；
- 底层输入；
- Provider 自身自动化特征处理；
- 发布和维护其正式浏览器／驱动组合、配置和协议。

### Harbor

负责：

- 生成和保存 Profile 环境配置；
- 启动时正确应用；
- 验证关键配置；
- 管理代理和网络出口；
- 记录实际观测和漂移；
- 管理 Instance 生命周期和控制权；
- 选择、调用并验证供应方能力，不通过补丁改写供应方行为。

### SKILL

不得：

- 更换 Provider；
- 重生成指纹；
- 修改 UA；
- 静默换代理；
- 改变 Account 绑定；
- 绕过 Profile 权限；
- 因任务失败自动换环境重试。

---

# 11. 自然交互

## 11.1 目标

自然交互的目标是：

- 减少抢跑；
- 减少误点；
- 减少重复提交；
- 适配页面真实交互；
- 便于人类观察和接管；
- 避免不必要的自动化暴露。

它不是万能防检测开关。

## 11.2 页面状态优先

优先依据：

- 控件是否可见、稳定、可用；
- 上传是否完成；
- 校验是否通过；
- 页面是否完成导航；
- 数据是否回读；
- 经营对象是否正确；
- 结果是否明确。

不得以大量随机等待替代状态判断。

## 11.3 输入方式

- 普通字段可使用可靠的直接填写；
- 页面依赖键盘事件时使用逐键输入；
- 富文本、画布式编辑器必须先进行小范围验证；
- 上传必须确认页面实际接收并处理文件；
- 发布和保存必须防止重复触发；
- 依赖 OS 窗口、屏幕坐标或原生键盘路由的输入必须有可靠窗口与画面对应，无法确认实际落点时拒绝；供应方 Page／Element 对象级 `click`、`fill`、`press` 只需按 Page／document、target/actionability、授权和 ControlLease 核验，不额外要求全局选中或 OS 前台。

## 11.4 明确禁止

不得为了“拟人”而：

- 浏览无关页面；
- 随机点赞、关注或评论；
- 制造虚假互动；
- 自动养号；
- 在挑战出现后不断轮换环境；
- 执行无业务意义的动作。

---

# 12. Agent 接入与 Plugin

## 12.1 Plugin 定位

Plugin 是 Agent 接入和分发方式，不是浏览器运行时。

```text
Agent 宿主
  └── WebEnvoy Plugin
        ├── WebEnvoy 管理 SKILL
        ├── 通用浏览器 SKILL
        ├── 网站 SKILL
        └── CLI / MCP / 本地工具入口
              ↓
        WebEnvoy Runtime
```

## 12.2 Runtime 独立于 App

**[已确认原则]**

- 不启动 App 也能使用 Runtime；
- App 可以启动、连接、监控 Runtime，但不是唯一宿主；
- Plugin 与 App 使用同一 Runtime；
- Profile 数据不属于插件目录；
- 插件更新或卸载不得丢失 Profile；
- App 和 Agent 不得各自管理一套浏览器实例。

## 12.3 Agent 管理能力

在有效 Grant 范围内，Agent 应能够：

### Provider 与环境

- 查询已安装 Provider；
- 查询版本和能力；
- 为新 Profile 显式选择获准且可用的 Provider；
- 查询用户新建默认偏好及项目推荐，但不混淆二者；
- 诊断启动问题；
- 查看配置和实际观测；
- 修改允许的非破坏性环境配置；
- 发起迁移。

Provider 安装、更新和修复是否允许由 Agent 直接执行，属于待原型验证；至少必须支持查询和发起需要用户处理的操作。

Agent 未明确选择且用户没有新建默认偏好时，创建请求必须返回需要选择。Provider 选择权不授予安装、迁移或偏好管理权；修改用户默认需要独立的明确管理授权。

### Profile

- 列表；
- 查询；
- 创建；
- 重命名；
- 标签管理；
- 导入；
- 复制环境模板；
- 发起迁移；
- 归档；
- 删除。

### Account 与 AccountSystem

- 查询；
- 导入公共模板；
- 创建本地 AccountSystem；
- 处理 discovered identity；
- 建立显式绑定；
- 查询唯一归属；
- 报告冲突；
- 管理 BusinessTarget。

### Instance

- 启动；
- 复用；
- 查询；
- 停止；
- 打开原生窗口；
- 请求人工接管；
- 在用户交还后继续。

### SKILL

- 查询；
- 安装；
- 更新；
- 禁用；
- 验证；
- 使用用户本地 overlay；
- 创建修复草稿。

## 12.4 Agent 不得直接获得

- Cookie 原文；
- 密码；
- 验证码；
- token；
- Profile 本地路径；
- 内部数据库任意写权限；
- 任意提高 Grant 的能力；
- 绕过 ControlLease；
- 绕过账号冲突；
- 静默更换 Provider；
- 在 unknown outcome 后自动重复写入。

---


## 12.5 Plugin-first 的 V1 实施优先级

**[V1 收敛约束]**

> 在完整 App 产品化之前，先让一个明确支持的第三方 Agent 宿主通过已安装 WebEnvoy Plugin，完整消费 V1 中允许委托给 Agent 的管理和浏览器能力。

这意味着第一完整消费端应能够在有效 Grant 范围内：

- 管理和使用 Profile、Instance、Account／AccountSystem、BusinessTarget、Environment、Provider facts、SKILL 和 Run／结果；
- 使用没有网站 SKILL 时的通用 Browser Runtime capability；
- 加载并使用版本化网站 SKILL，提高特定网站任务的效率和准确性；
- 在 App 未启动时连接独立 Runtime，并在需要用户处理时进入可信的人类确认或同实例接管路径；
- 不依赖开发 worktree、内部数据库写入、复制 supervisor／owner 凭据或手工调试端口。

Plugin 必须保持薄层：负责宿主适配、能力呈现、SKILL 分发和工具接入；不得拥有第二套 Profile、账号、权限、Run、恢复或浏览器状态真相。

首期只要求一个主要 Agent 宿主完成该完整消费检查点，不因此提前建设多宿主注册平台。

## 12.6 Runtime 能力与 Agent 工具暴露

Runtime 的 V1 capability plane 可以比某一次任务向 Agent 展示的工具集合更完整。

Plugin／宿主可以根据：

- 当前 Grant；
- Profile 权限上限；
- 当前任务范围；
- 已安装 SKILL 声明的所需能力；
- Provider 当前实际能力；

选择向 Agent 呈现更小的工具集合或更有界的结果。但工具是否展示不参与权限计算，也不能将 Runtime 缺失的能力伪装成“由 SKILL 实现”。

正式 Plugin 的 Profile 创建必须消费 owner API 已确认的 Provider 选择、支持事实和授权，不在薄层内复制默认规则或静默回退。Provider 差异只改变 capability availability／limitation，不改变 Profile、Instance、ControlLease、Run 和恢复的 owner。

# 13. App 定位

## 13.1 人类控制台

App 是人类资源管理和运行控制台，不是完整 Agent 工作台。

核心区域包括：

1. Agent 接入；
2. AccountSystem；
3. Account；
4. Profile 与环境；
5. Provider；
6. SKILL；
7. Instance；
8. Activity 与待处理事项。

## 13.2 Agent 接入管理

App 必须能够：

- 查看 AgentPrincipal；
- 查看当前和历史 AgentConnection；
- 查看 Grant；
- 修改或撤销 Grant；
- 查看最近活动；
- 处理重连和失效连接；
- 区分本地与远程连接。

## 13.3 Account 与 AccountSystem

App 应支持：

- 管理公共模板导入；
- 创建和编辑本地 AccountSystem；
- 从 Account 角度查看唯一归属 Profile；
- 查看身份验证状态；
- 查看 BusinessTarget；
- 处理身份冲突；
- 迁移 Account 归属。

## 13.4 Profile 与环境

App 应支持：

- 创建；
- 导入；
- 迁移；
- 归档；
- 删除；
- Provider 选择；
- 用户新建默认偏好；
- 项目推荐预选与最终用户确认的区分；
- 代理配置；
- 环境模板；
- 权限配置；
- configured／effective／pending／drift 状态。

## 13.5 Provider

App 应支持：

- 检测；
- 查看版本；
- 查看能力和限制；
- 诊断；
- 发起安装、更新、修复或迁移。

App 必须分别展示产品推荐、用户新建默认和当前 Profile 绑定。实验性／受限 Provider 可以被用户显式选择，但必须准确展示限制；未达到产品接入底线的条目不得包装为可用。修改默认只作用于后续创建。

完整自动修复不属于 V1 必须范围。

## 13.6 SKILL

App 应支持：

- 查看；
- 安装；
- 更新；
- 禁用；
- 查看来源和版本；
- 查看依赖的 AccountSystem 和 Runtime 能力；
- 管理本地草稿和 overlay。

## 13.7 任务管理收敛

App 中的任务管理收敛为：

```text
正在运行
需要我处理
最近完成
```

每条 Activity 优先展示：

- 目标摘要；
- 发起 Agent；
- Account；
- Profile；
- Instance；
- 任务页及其 Page／document 新鲜度；
- SKILL；
- BusinessTarget；
- 当前状态；
- 待处理事项；
- 最终业务结果或失败原因。

任务页是工作目标的引用；Viewer 的选中页和 OS 前台焦点是独立的现场事实，不能互相冒充。

App 不接管外部 Agent 的完整计划、聊天记录和文档工作流。

---


## 13.8 V1 实施优先级

**[V1 收敛约束]**

完整 App 资源工作台、Library、Activity 工作台和高级管理体验在 Plugin 完整体验检查点之后集中产品化。该后置不取消本章的完整 V1 产品要求。

Plugin-first 阶段 App 或其他可信 owner 入口必须继续提供完成真实闭环所需的最小人类控制面，包括：

- AgentPrincipal／Grant 的建立、查看、收紧和撤销；
- 首次信任、权限扩大和其他必须由人作出的敏感决定；
- 身份冲突、迁移、删除等需要用户处理的明确入口；
- 同一原 Instance 的接管和交还；
- 当前 Profile／Instance／控制者和“需要我处理”状态的最小可理解展示。

已经在有效授权内明确允许的 Agent 管理意图，不应被强制重复到 App 中再次批准同一意图。App 不得成为 Runtime 生命周期或普通 Agent 操作的隐藏硬依赖。

# 14. 多实例现场视图

## 14.1 产品能力

App 应支持：

- 多个 Profile 的 Instance 同时运行；
- 在不同 Instance 画面间切换；
- 同时展示多个 Instance；
- 不同 Instance 使用不同 Provider；
- 从画面直接查看账号、Profile、经营对象和控制者。

## 14.2 展示模式

### 单实例主视图

左侧实例列表，右侧显示当前选中实例。

### 多实例概览

多个低刷新率缩略图，展示：

- Account；
- Profile；
- 当前页面；
- 当前 Agent；
- 控制者；
- 是否等待处理；
- 最后更新时间。

### 分屏和网格

**[V1 UI 建议]**

先验证两实例分屏，再决定是否交付四实例网格。布局数量不是长期架构限制。

## 14.3 硬约束

- 必须显示原实例；
- 不得重新加载 URL；
- 切换画面不得创建新会话；
- 关闭面板不得停止 Instance；
- 查看不得自动取得 ControlLease；
- 只有持有 Lease 的主体可以输入；
- 接管一个 Instance 不影响其他 Instance；
- App 布局不得修改设备环境；
- 旧帧必须标记过期；
- 观看失败不等于任务失败；
- 系统文件选择器、扩展弹窗等复杂交互可以转到同一实例原生窗口；
- Page 关闭只能作用于获准目标，不得借邻页补焦点或隐式结束 Instance；最后一个可用 Page 无法确认时必须拒绝；
- Viewer 的明确选中页不等于 OS 前台页；只读查看不得主动激活。依赖 OS 窗口、屏幕坐标或原生键盘路由的输入若无法证明窗口与画面对应必须拒绝，Page／Element 对象级输入按 Page／document、target/actionability、授权和 ControlLease 核验。
- 人工使用的验收是观看和输入回到同一原 Instance，不要求底层一定提供原生有头窗口；只有截图或重新加载 URL 均不满足。

## 14.4 技术路线

采用：

> 浏览器独立运行，App 获取同一 Instance 的截图或画面流，并将受控输入发送回该 Instance。

任务页引用、Viewer 的当前选择和原生窗口／OS 前台关系分别表达。画面切换不等于切换任务目标；人类交还后，Agent 依据可信 Page 引用重新观察原任务页。

不采用：

- 用 Electron WebContents 重新加载站点；
- 把任意浏览器内核真正嵌入 App；
- 强行把所有外部窗口变成跨平台子窗口；
- 要求所有 Provider 具备同等级内嵌能力。

Provider 可以声明：

```text
native_window
static_screenshot
low_frequency_preview
live_frame_stream
interactive_view
```

App 根据能力降级。

`static_screenshot` 或 `low_frequency_preview` 只证明观看能力；只有原实例画面配合受控点击、滚动、输入和 ControlLease 接管／交还，才能证明 `interactive_view` 或等价人工使用。

---


## 14.5 Runtime 画面能力与 App 布局分离

原页面截图、基础画面获取、Page 引用和 Provider 对画面能力的事实属于 Browser Runtime capability plane，不应因为完整 App 多实例布局后置而一起延期。原生选中／前台关系是可选事实；只读获取不得为了补它而激活页面。

App 的单实例主视图、多实例概览、分屏、旧帧展示和留存体验仍由本章负责。Runtime 提供画面能力不等于默认录制，也不授予 ControlLease。

# 15. 现场画面和数据留存

## 15.1 临时观看与持久化分离

**[已确认原则]**

> 用户实时看到画面，不等于系统必须录制或永久保存画面。

默认规则：

- 实时帧只用于本地即时显示；
- 打开多实例概览不自动录屏；
- 缩略图缓存必须有过期时间；
- 画面停止更新后必须显示旧帧状态；
- 是否保存截图或片段由用户操作或证据策略决定；
- 保存时必须关联 Profile、Instance、Run 和时间；
- 保存时遵守脱敏与保留策略；
- 关闭画面流不影响 Instance；
- 删除 Run 不等于删除 Profile 数据；
- 删除 Profile 不等于自动删除独立证据，除非策略明确。

## 15.2 敏感内容

店铺订单、客户信息、私信、账号设置等画面不得因为调试方便而默认长期保存。

---

# 16. SKILL 与 Lode

## 16.1 Lode 定位

> **Lode 是网站 SKILL、AccountSystem 模板及其配套资源的版本化资产库。**

SKILL 是主要产品资产，但不是所有共享知识的唯一容器。

## 16.2 资产结构

建议：

```text
Lode
├── account-systems/
│   ├── google/
│   │   ├── manifest.yaml
│   │   └── identity-observation.md
│   └── meta/
│       └── manifest.yaml
├── skills/
│   ├── youtube-publish/
│   │   ├── SKILL.md
│   │   ├── references/
│   │   ├── scripts/
│   │   └── tests/
│   └── store-update-product/
│       └── ...
└── shared/
```

多个 SKILL 可以引用同一 AccountSystem 模板。

## 16.3 SKILL 包含内容

网站 SKILL 可以包含：

- 网站入口；
- AccountSystem 引用；
- Account 识别方法；
- BusinessTarget 识别方法；
- 页面结构；
- 字段语义；
- 操作流程；
- 页面等待条件；
- 写入前检查；
- 结果验证；
- 异常分类；
- 恢复方式；
- 确定性辅助脚本；
- 脱敏测试样例。

## 16.4 粒度

SKILL 应围绕用户目标，例如：

- 发布图文内容；
- 更新商品；
- 查询订单；
- 管理评论；
- 检查账号状态。

不应为每个按钮创建 SKILL。

## 16.5 三层边界

必须使用以下定义：

> **SKILL 决定 Agent 推荐怎样完成任务。**
> **Runtime 决定系统实际提供哪些操作。**
> **授权系统决定当前主体允许执行什么。**

由此：

- SKILL.md 不是确定性程序；
- Core 不自动把自然语言编译成可信执行代码；
- 非 Agent API 调用方不能仅凭 SKILL.md 获得执行能力；
- 确定性操作必须由 Runtime API、工具或脚本提供；
- allowed-tools 等元数据不能替代 Runtime 权限；
- 附带脚本必须有来源、版本、完整性和执行范围；
- SKILL 不得绕过失败的授权检查；
- SKILL 更新不得静默修改用户本地 AccountSystem 或 Profile 权限。

## 16.6 没有 SKILL 时

没有网站 SKILL 时，Agent 仍可以在授权范围内：

- 选择 Profile；
- 启动浏览器；
- 打开网站；
- 观察页面；
- 执行通用浏览器操作。

有 SKILL 后，应减少探索、工具调用和错误，并提高账号、经营对象和结果判断的准确性。

## 16.7 关键交付指标

> 当 Runtime 已经支持必要基础操作时，新增网站或场景应主要通过新增或修改 SKILL 完成，而不需要同时修改 App、Core 和 Harbor。

---


## 16.8 SKILL 与 Runtime capability 的关系

网站 SKILL 可以声明完成某项用户目标所需要的 Runtime capability，并向 Agent 推荐更小、更高效的工具集合、页面入口、字段语义、等待条件、结果判断和恢复方式。

但必须保持：

```text
Runtime capability 是否存在
≠ SKILL 是否安装
≠ 工具是否向当前 Agent 展示
≠ 当前主体是否获得执行权限
```

新增网站不应通过在 Harbor／Core 中增加站点专用 Network、Console、DOM 或输入旁路来弥补 Runtime 基础能力缺失。必要的通用浏览器能力应先归入 Runtime 公共能力；真正的网站知识留在 SKILL／Lode。

# 17. Run、连接、实例、控制权和外部结果

## 17.1 状态必须分离

```text
ConnectionState
≠ InstanceState
≠ ControlState
≠ RunState
≠ ExternalOutcome
```

## 17.2 规范性规则

- AgentConnection 断开不等于 Run 成功；
- AgentConnection 断开也不自动等于 Run 失败；
- App 关闭不等于 Instance 退出；
- 观看流失败不等于 Instance 失败；
- Instance 崩溃不等于网站侧动作被撤销；
- 用户停止表示禁止后续操作，不表示回滚已发生动作；
- 人类接管只改变 ControlLease，不自动改写 Run；
- 恢复连接不得自动重放写入；
- unknown_outcome 不得因为重新打开页面而被静默改写；
- 用户交还控制后，Agent 必须重新观察页面和身份，再继续；
- 任务页 A 与观看的帮助页 B 分离；交还后默认重新观察 A，不自动跟随 B，也不要求先证明 A 仍是原生选中标签；
- A 关闭、失联、替换或身份歧义时，只能依据供应方可信关系或用户明确选择且仍可验证的真实 Page 继续；不得用 URL、标题、内容或创建顺序认领，也不得复制、reload／reopen、重建 Instance 或重放操作；
- 一个高层 Activity 可以聚合多个 Run；
- Run 的技术状态不得替代业务结果；
- 外部业务结果不能仅依据 HTTP 状态或工具调用成功推断。

## 17.3 写入语义

写入应支持：

- 幂等 key；
- operation ref；
- 写入前目标检查；
- 写入后结果检查；
- unknown outcome；
- manual recovery；
- reconcile status；
- request cancel。

无法确认结果时不得自动重试。任务页／document、target/actionability、身份、授权或 ControlLease 无法确认时，必须拒绝相关写入并保留可验证的恢复入口；依赖 OS 窗口、屏幕坐标或原生键盘路由的输入还必须确认窗口与画面对应。

---

# 18. 人工接管

## 18.1 触发场景

- 登录；
- 验证码；
- 安全挑战；
- 复杂富文本；
- 系统文件选择；
- Provider 或页面异常；
- Agent 主动请求；
- 用户主动接管。

## 18.2 流程

```text
Agent 请求或用户接管
→ Runtime 停止该 Instance 的新 Agent 输入
→ ControlLease 转给 Human
→ App 展示同一真实现场
→ 用户完成操作
→ 用户明确交还
→ ControlLease 转给 Agent
→ Agent 以交还前的可信任务页引用重新观察（不自动跟随用户观看的其他页）
→ 继续或结束原 Run
```

不得自动夺回用户控制权。

---

# 19. 模块职责

| 模块 | 核心职责 |
|---|---|
| **App** | 人类管理 Agent 接入、AccountSystem、Account、Profile、环境、Provider、SKILL、Instance、Activity、观看和接管 |
| **Plugin** | 将 WebEnvoy 接入现有 Agent 宿主 |
| **Core** | 主体授权、业务任务、Run、结果、幂等、失败、ExternalOutcome 和恢复 |
| **Harbor** | Profile、Provider、Environment、Instance、页面操作、画面、ControlLease 和运行观测 |
| **Lode** | SKILL、AccountSystem 模板、站点知识、脚本、测试和版本 |
| **Provider** | 浏览器内核、原生设备环境能力和底层运行 |

职责边界按实际承担的能力判断：Harbor、Driver、App、Plugin 或 Lode 都不得实现、模拟或长期补偿 Provider 缺失的浏览器渲染／命中、键盘／IME、窗口／弹窗／对话框、下载、Web Storage／IndexedDB、站点权限或浏览器级设备身份，也不得改写供应方程序、资源、驱动 bundle 或注入安装／运行时补丁。WebEnvoy 管理既有能力的正式配置／协议、生命周期、授权、Driver 转换、环境 bundle、调用、观察、恢复和证据；Provider 负责浏览器内核及其核心语义。

## 19.1 Core 不应做

- 直接读写 Profile 目录；
- 直接管理浏览器进程；
- 理解 Provider 内部实现；
- 把每次点击都变成重型业务任务；
- 自动解释所有 SKILL 自然语言。

## 19.2 Harbor 不应做

- 决定用户业务内容；
- 定义站点业务结果；
- 保存站点知识；
- 修改 Principal Grant；
- 替用户选择经营策略。

## 19.3 App 不应做

- 复制 Core 状态机；
- 直接修改 Profile 数据目录；
- 绕过 Harbor 控制 Instance；
- 实现另一套 Agent 规划器；
- 维护独立授权真相。

## 19.4 Lode 不应做

- 保存 Cookie 或真实登录状态；
- 启动浏览器；
- 决定当前授权；
- 保存生产现场；
- 拥有 Profile；
- 直接改变用户本地 AccountSystem。

---

# 20. 典型流程

## 20.1 Agent 创建 Profile

```text
用户提出创建环境
→ 验证 AgentPrincipal 和 Grant
→ 选择允许的 Provider
→ 选择环境与权限模板
→ 创建 WebEnvoy Profile
→ 持久化环境配置
→ 启动 Instance
→ 完成登录
→ 创建 discovered identity
→ 用户或明确任务登记 Account
→ 进入正常使用
```

## 20.2 导入外部 Profile

```text
选择外部来源
→ 扫描可迁移内容
→ 创建新的 WebEnvoy Profile
→ 复制兼容数据
→ 报告未迁移内容
→ 验证登录状态
→ 检查 Account 唯一归属
→ 完成、重新登录或进入修复
```

## 20.3 执行网站运营任务

```text
用户向 Agent 提出目标
→ Agent 加载 SKILL
→ 解析 Account 和唯一 Profile
→ 验证 Grant 与 Profile 权限
→ 启动或复用 Instance
→ 验证实际 Account
→ 验证 BusinessTarget
→ 选择并绑定真实任务页及 document 新鲜度
→ 取得 ControlLease
→ 执行任务
→ 回读页面和业务结果
→ 返回结果、失败或人工处理事项
```

## 20.4 身份冲突

```text
页面观测到 Account B
→ Profile 绑定 Account A
→ 停止依赖身份的操作
→ 保留 Cookie 和现场
→ 不自动重绑、不自动清理
→ 创建待处理事项
→ 用户退出、修复或迁移
```

任务页失联或被替换时，相关动作准确停止；恢复只能使用供应方可信关系或用户明确选择且仍可验证的真实 Page，不得按 URL、标题、内容或创建顺序猜测。

## 20.5 Provider 迁移

```text
发起迁移
→ 停止活动身份任务
→ 创建目标环境
→ 迁移兼容数据
→ 重新登录或验证
→ 检查 Account 与 BusinessTarget
→ 切换正式 Profile
→ 原环境转为恢复备份
```

---

# 21. V1 实施优先级

本章描述实施优先级，不规定按架构层或 Milestone 全串行交付。

**[已确认实施原则]**

> 先明确 V1 Browser Runtime 的主要能力类别，并通过一个已安装 Plugin 在真实第三方 Agent 中持续消费；每个交付单元仍以“用户或 Agent 能完整做成一件事”为边界，贯穿必要模块。不得先封闭建设一个无人消费的大 Runtime，也不得等待某个站点需要时才决定基础浏览器能力是否存在。

## 21.0 实施基线与获批执行门

V1 的十二类 Browser Runtime 最低结果（Instance、Page／Tab／Window、Navigation、Observation、Interaction／Wait、Files／Dialog、Network、Console／Page errors、Controlled evaluation、Screenshot／Frame、Storage／Permissions、Control／Recovery）必须先有公共语义和来源映射，再按完整用户结果滚动交付。执行索引见 WebEnvoy [Browser Runtime 能力规格第 4 节](https://github.com/WebEnvoy/WebEnvoy/blob/main/docs/specs/browser-runtime-capabilities-v1.md#4-v1-十二类能力最低结果矩阵)；当前状态和证据仍由对应 Issue、PR、checks、review、安装记录及 verification 事实承载。

进入实现前，Work Item／PR 必须具备可回读的原生 parent、Milestone、范围和完成标准，并逐项声明适用的 Design Obligation。获批执行只覆盖已确认的用户结果、文件和授权边界：Provider 必须使用来源可核对的正式组合，不能换 Provider、放宽 Grant、恢复私有补丁或以 fallback／猜测补齐缺失能力；高影响外部动作需明确 owner 授权。未派发失败和已派发 unknown 必须分别记录，后者只能查询、对账、接管或停止后续动作，不得换 key 重放。

文档、实现和验证必须保持三层边界：canonical／ADR 定义方向与架构，spec／contract 定义公共语义，Issue／PR／verification 记录当前交付和证据。安装器、脚本、独立客户端、CI 或 PR review 不能冒充真实第三方 Agent；只有实际安装 Plugin 被真实 Agent 消费的证据才可标记 `plugin_verified`。

## 21.1 Runtime 能力与 Plugin 主入口

优先：

- 建立 V1 Browser Runtime capability plane 的完整能力基线；
- 逐步交付 Page／Window、Observation、Interaction、Files、Network、Console／Errors、受控脚本、Screenshot／Frame、Control 和 Recovery；
- 每项能力同时给出 Provider 支持状态、验证证据和授权边界；
- 可以先用一个明确支持的第三方 Agent 宿主和单个有界真实用户结果验证已安装 Plugin 的正式入口；该早期消费者只证明声明的能力子集，不提前声称完整 Runtime 或完整 Plugin 检查点；
- 完成 §21.4 检查点时，一个已安装 Plugin 必须在真实第三方 Agent 中按十二类能力矩阵逐项呈现允许委托的结果、必要拒绝和恢复，不把单个早期闭环升级为全量通过；
- 没有网站 SKILL 时仍可完成通用浏览器操作。

## 21.2 长期 Profile、身份与设备环境并行成熟

并行推进 AccountSystem／Account／BusinessTarget、Profile 生命周期和 Provider／Environment；需要真实登录、账号绑定或业务写入的现场验证在取得相应授权后执行，缺授权只暂停相关动作，不把无关 Runtime／环境工作全局停止。

Provider 接入按 9.1 的 Qualification Gate 有界推进；当前不启动新 Provider 研究。每项验证必须区分原型、能力交付、正式可选、用户默认与完整 V1；某个 Provider 的局部缺口不缩减公共能力，需要 WebEnvoy 补浏览器核心语义的候选停止采用。

## 21.3 Plugin 完整资源管理与资产消费

在完整 App 产品化之前，先证明一个主要 Agent 宿主通过 Plugin 可以消费所有 V1 允许委托的 Profile／Instance、Account／AccountSystem／BusinessTarget、Environment／Provider facts、SKILL，以及 Run／结果／恢复能力。Plugin 更新、卸载或 Runtime 重启不得建立第二套现场或丢失长期 Profile。单个早期真实消费者可以先验证其中一个有界结果，但不替代本项完整资源消费门。

## 21.4 Plugin 完整体验检查点

作为 V1 必须完成、且位于完整 App 产品化之前的检查点，至少证明：

```text
安装第三方 Agent 接入口
→ 完成必要 owner 授权
→ App 可完全退出
→ Agent 管理长期 Profile / 身份 / 环境 / SKILL
→ 使用完整 V1 浏览器能力完成通用任务
→ 人类按需接管同一实例并交还
→ 结果、拒绝、unknown 和恢复可信
→ 更新 / 重启 / 卸载边界不丢长期数据
```

同时逐项回读十二类能力的公共语义、当前 Provider 状态、Plugin 暴露／不暴露原因、Grant／ControlLease、成功／必要拒绝／恢复证据，并确认真实第三方 Agent 实际消费了安装后的 Plugin。该检查点证明 Agent-native 主入口成立，不等于完整 V1 已验收；任一早期有界消费者或单个文件/站点闭环都不能替代它。

## 21.5 第二网站 SKILL 扩展验证

目标任务所需的 Runtime 能力、正式 Plugin 入口、实际资产和授权条件具备时，可以先做一次有界的真实消费者验证；不以整个 #497、#474、#475 或 §21.4 作为所有任务的统一前置。完整 Plugin checkpoint 仍须按 §21.4 完成后，才可把第二个真实用户目标和不同网站／场景作为 V1 汇合证据，验证跨网站复用、扩展成本和失败边界；简单任务的提前验证不能替代该完整 checkpoint，也不能把一个局部成功误认为产品完成。真正的公共能力缺口归入既有 Runtime capability，不为站点增加旁路。

## 21.6 完整 App 产品化与多实例监督

Plugin 完整体验检查点后，再集中完善 Agent 接入、AccountSystem／Account、Profile／Environment、Provider、SKILL、Instance／Activity、多实例切换与概览。Plugin-first 阶段已经需要的授权、人工接管、交还和敏感决定入口不得等待到此阶段才首次实现。

## 21.7 V1 产品交付与最终验收

持续回读 V1 验收项，不在末期第一次集成。最终同时核对 Plugin 完整体验、完整 App／Viewer 产品路径、Provider／Profile／身份／环境长期一致性、SKILL 扩展成本、安装升级卸载恢复，以及 Run／ExternalOutcome、权限、隐私与失败边界。

近期执行先完成本次规则、文档和 GitHub 对齐，再核验原 Instance 在 Agent 空闲与人工接管期间能否持续处理已获准页面事件；同期准备现有身份／经营对象主线的授权和材料。此后按目标任务优先补普通交互缺口，真实消费者在自身能力、Plugin、资产和授权条件具备时进入，不因刚完成 Files 自动继续扩建 Files，也不把各 Milestone 重排为瀑布依赖。最多并行两个有界交付单元；涉及正式安装或浏览器现场时由一个集成人协调。

# 22. V1 验收标准

V1 至少必须证明以下用户结果，并按 9.9 的证据类型记录实际消费者、固定源码／构建、平台／原实例、身份授权、成功／拒绝／恢复、证据地址与未执行项：

1. WebEnvoy 自己创建和保存 Profile；
2. 外部 Profile 导入后不再依赖源目录；
3. 多个独立 Profile 可以同时运行；
4. 同一 Profile 跨重启保持登录和设备环境连续；
5. 同一 Account 不能同时归属第二个可运行 Profile；
6. 同一 Profile 不能绑定同一 AccountSystem 的第二个 Account；
7. 新站点登录不会被自动清理；
8. 新账号不会被自动正式绑定；
9. 身份冲突会阻止依赖该身份的任务；
10. AgentPrincipal、AgentConnection 和 Grant 可被识别、查看和撤销；
11. 有效权限使用交集模型；
12. Agent 可以在授权范围内创建和管理 Profile；
13. Agent 不能提高自己的权限；
14. Agent 可以启动、复用和停止 Instance；
15. App 可以查看多个 Instance；
16. App 可以在多个 Instance 间切换；
17. App 画面来自原实例；
18. 查看与控制分离；
19. 人工接管后 Agent 不再输入；
20. 用户交还后 Agent 重新观察再继续；
21. Provider 不可用时不静默切换；
22. 运行中环境变更不会静默热应用；
23. 实时观看不会默认开启录制；
24. SKILL、Runtime 和授权三层边界成立；
25. 新网站场景主要通过 SKILL 增加；
26. 写入前能核对 Account 和 BusinessTarget；
27. 写入后能确认结果或标记 unknown；
28. unknown outcome 不自动重试；
29. Connection、Instance、Control、Run 和 ExternalOutcome 状态互不混淆；
30. App 不承担完整 Agent 对话和任务编排。

---


31. V1 Browser Runtime 的主要能力类别有明确公共语义、Provider 支持状态、实际验证和授权边界；基础能力不依赖某个站点 SKILL 才存在。
32. 一个明确支持的第三方 Agent 宿主通过已安装 Plugin，可以消费所有 V1 允许委托的资源管理、浏览器、SKILL 和结果／恢复能力；App 未启动时普通 Agent 路径仍成立，必要人类决定和同实例接管有可信入口。
33. 用户可以为新 Profile 显式选择获准且可用的 Provider；显式选择优先于用户新建默认，用户默认与项目推荐分离，不可用时不静默回退。
34. 修改用户新建默认不改变既有 Profile／Instance、环境、账号绑定或 Grant；既有 Profile 始终按其持久 ProviderBinding 运行，跨 Provider 走显式迁移。
35. Provider 的人工使用可以采用不同窗口／画面实现，但必须证明观看、输入、接管和交还都作用于同一原 Instance。
36. 真实 Provider 验收使用来源可核对、未经 WebEnvoy 改写的供应方正式浏览器／驱动组合；WebEnvoy 仅保留正式配置／协议调用、Driver 转换、权限、环境 bundle、生命周期和恢复。
37. 任务页 A 与 Viewer 观看的页 B 分离；A 仍可信时交还后默认重新观察 A，不自动跟随 B，也不要求先证明原生选中标签关系。
38. `openPage` 的真实创建、可能聚焦和后台能力按供应方事实表达；后台要求无法证明时派发前拒绝，`list`／read／diagnostics 不激活，Viewer 不冒称 OS 前台。
39. `closePage` 只关闭获准目标；目标或最后可用 Page 无法确认时拒绝，不借邻页、URL、标题或创建顺序认领，不隐式结束 Instance 或创建替代页；依赖 OS 窗口、屏幕坐标或原生键盘路由的输入若无法确认窗口与画面对应则拒绝，Page／Element 对象级输入按 Page／document、target/actionability、授权和 ControlLease 核验。
40. A 关闭、失联、替换或身份歧义时，相关动作准确停止；恢复只使用供应方可信关系或用户明确选择且仍可验证的真实 Page，不复制、reload／reopen、重建 Instance 或重放操作。
41. 旧私有 patch binding、补丁合同和复制改写 bundle 从当前安装／启动／Runtime 路径退役，但旧绑定、数据、授权、账号归属、Run／receipt、SKILL 和恢复事实仍可查询、管理、撤销、对账和安全恢复。
42. 版本升级核对供应方来源、兼容性和实际能力，经明确选择更新绑定；不热换、自动回退或移植旧补丁，不因失败回滚授权、账号、指纹、代理、权限或历史结果。
43. 公共 `active`／selected 语义不兼容时已版本化；旧消费者准确拒绝或经验证适配，不把任务页引用或占位值冒充供应方原生焦点字段。

# 23. 待原型验证清单

以下方向已经明确，但必须通过工程验证：

- Camoufox 长时间人工浏览体验；
- Camoufox 多实例资源成本；
- Camoufox 与目标社媒、店铺站点兼容性；
- Camoufox 配置跨版本稳定性；
- macOS 实例画面流；
- 供应方正式 Camoufox／Playwright 组合的实时画面和输入能力；
- 系统文件选择器与原生弹窗边界；
- 任务页 A／B 协作、交还后重观察、原生焦点未知时的只读与输入拒绝；
- 旧私有 patch binding 退役后的查询、撤销、Run／receipt 和恢复路径；
- 用户自定义 AccountSystem 的身份识别；
- Provider 安装和更新是否允许 Agent 直接执行；
- CloakBrowser BYOL 的长期价值；
- SKILL overlay 和修复流程；
- V1 权限模板；
- 两实例分屏和四实例网格的实际产品价值。

---

# 24. 规范性总结

> **WebEnvoy 是面向矩阵社媒和多店铺运营的 Agent 原生多账号浏览器平台。所有正式可运行 Profile 由 WebEnvoy 统一创建、导入和管理；外部 Profile 只能迁入，不能直接挂载。Provider 程序可以由用户安装或由 WebEnvoy 协助获取，但运行时必须使用 WebEnvoy 管理的 Profile 数据。**
>
> **V1 中，每个 Account 只允许归属一个可运行 Profile；每个 Profile 在同一个 AccountSystem 中只允许一个 Account，但可以包含多个不同 AccountSystem 的账号。Account 与店铺、主页、频道等 BusinessTarget 分开管理。权限不根据 Profile 用途或标签推断，有效权限等于 Profile 上限、Principal Grant、任务范围和运行时约束的交集。**
>
> **Agent 不仅能够操作网页，也能够在明确 Grant 范围内管理 AccountSystem、Account、Profile、环境、Provider、Instance 和 SKILL。App 是人类资源管理和控制台，负责 Agent 接入、账号、Profile、环境、Provider、SKILL、实例、活动、现场观看和人工接管，不再承担完整 Agent 工作台。**
>
> **App 可以切换和同时展示多个真实浏览器 Instance。画面必须来自原实例，不得通过重新加载 URL 创建第二个会话。观看与控制分离，输入必须持有 ControlLease；复杂交互可以打开同一实例的原生窗口。实时观看默认不等于录制。**
>
> **Provider 由用户选择；新增 Provider 增加选择，不替换现有 Provider。产品推荐、用户新建默认和 Profile 实际绑定必须分离，显式选择优先，不可用或未授权时局部拒绝且不静默回退。Camoufox 保留首个工程验证对象及已验证范围，Chrome 保留显式兼容选择；Obscura 在当前愿景完成前明确不采用。Provider 在 Profile 创建时确定，后续变更通过迁移完成。WebEnvoy 只管理和调用供应方正式组合已经具备的浏览器能力，通过正式配置／协议、Driver 转换、权限和环境 bundle 接入，不改写供应方程序、资源、驱动或 bundle，不实现、模拟或长期补偿核心浏览器语义。**
>
> **网站知识以 SKILL 为主要载体，AccountSystem 模板等共享知识作为独立资产被多个 SKILL 引用。SKILL 决定 Agent 推荐怎样完成任务，Runtime 决定实际提供哪些操作，授权系统决定当前允许执行什么。没有 SKILL 时，Agent 仍可使用通用浏览器能力；有 SKILL 后，应减少探索、试错和错误，并提高账号、经营对象和结果判断的准确性。**
>

> **V1 以一个已安装 Plugin 在真实第三方 Agent 中完成完整消费作为优先检查点。Browser Runtime 先明确主要能力类别，包括页面／窗口、交互、文件、Network、Console／错误、受控执行、画面、控制和恢复；能力是否存在与当前 Agent 是否被展示或授权使用必须分离。完整 App 产品化在该检查点后集中完善，但必要的人类授权、敏感决定和同实例接管不能缺失。**
>
> **ConnectionState、InstanceState、ControlState、RunState 和 ExternalOutcome 必须分离。断线、关闭 App、观看失败、实例退出或用户停止后续操作，都不能自动改写网站侧已经发生或可能发生的结果。无法确认写入结果时，必须保留 unknown outcome，并禁止自动重复写入。**

> **任务页引用与原生选中／前台焦点分离。任务页 A 仍可信时，人类在同一 Instance 中观看或操作其他页 B 后交还，Agent 默认重新观察 A；只读不激活，Viewer 不冒称 OS 前台。Page／Element 对象级输入按 Page／document、target/actionability、授权和 ControlLease 核验；只有依赖 OS 窗口、屏幕坐标或原生键盘路由的输入才要求可靠窗口与画面对应。A 失联时只能依据供应方可信关系或用户明确选择且仍可验证的真实 Page 恢复，不得按 URL、标题、内容或创建顺序认领，不得复制、重载、重建或重放。**

---

# 附录 A：核心对象关系

```text
AgentPrincipal
  ├── AgentConnection
  └── Grant
        ↓
AccountSystem
  └── Account
        ↓
Profile
  ├── ProviderBinding
  ├── EnvironmentConfiguration
  ├── PermissionPolicy
  ├── BusinessTarget
  └── Instance
        ├── ControlLease
        ├── Page / Tab
        └── Run
              └── ExternalOperation / ExternalOutcome

Activity
  └── 聚合一个或多个 Run，供 App 展示

Lode
  ├── AccountSystem Templates
  ├── SKILL
  ├── Shared References
  ├── Scripts
  └── Tests
```

# 附录 B：决策状态登记表

| 决策 | 状态 |
|---|---|
| WebEnvoy 定位为矩阵社媒和多店铺运营的 Agent 原生多账号浏览器平台 | 已确认原则 |
| 所有正式 Profile 由 WebEnvoy 管理 | 已确认原则 |
| 外部 Profile 只能迁入 | 已确认原则 |
| Provider 程序可以外部安装，Profile 数据必须由 WebEnvoy 管理 | 已确认原则 |
| 权限不根据 Profile 用途标签推断 | 已确认原则 |
| 有效权限采用交集模型 | 已确认原则 |
| Agent 可以管理环境和 Profile | 已确认原则 |
| 同一 Profile 内同体系只能一个 Account | 已确认原则 |
| Account 与 BusinessTarget 分开 | 已确认原则 |
| App 展示原实例画面 | 已确认原则 |
| App 支持多实例切换和同时展示 | 已确认原则 |
| Runtime 独立于 App | 已确认原则 |
| SKILL 是网站知识主要载体 | 已确认原则 |
| AccountSystem 是独立共享资产 | 已确认原则 |
| SKILL、Runtime、授权三层分离 | 已确认原则 |
| ego-lite／ego-browser 不接入 | 已确认原则 |
| Wayfern 不研究、不接入 | 已确认原则 |
| 一个 Account 只归属一个可运行 Profile | V1 收敛约束 |
| 一个 Profile 同时一个主实例 | V1 收敛约束 |
| 新账号不自动正式绑定 | V1 收敛约束 |
| 普通复制不复制登录状态 | V1 收敛约束 |
| 首期支持一个 Agent 宿主 | V1 收敛约束 |
| 控制权按 Instance 管理 | V1 收敛约束 |
| Provider 由用户选择；项目推荐、用户新建默认和 Profile 绑定分离 | 已确认原则 |
| 显式选择优先，不可用／未授权不静默回退 | 已确认原则 |
| 供应方正式浏览器／驱动组合必须来源可核对且未经 WebEnvoy 改写；WebEnvoy 仅做正式配置／协议调用、Driver 转换、权限和环境 bundle 管理 | 已确认原则 |
| Camoufox 作为首个工程验证 Provider 及其已验证范围 | 已确认原则 |
| Camoufox 长期人工使用 | 待原型验证 |
| WebEnvoy 不补 Provider 缺失的浏览器核心能力，也不维护浏览器 fork／内核补丁链 | 已确认原则（至少持续到当前愿景完成） |
| Provider Qualification Gate 先分类、后 spike、再决定是否正式接入 | 已确认原则 |
| Obscura 在当前愿景完成前不采用；不会因新版本自动重开 | 明确退出当前范围（历史：#511） |
| 实时多实例交互画面 | 待原型验证 |
| Agent 直接安装／更新 Provider | 待原型验证 |
| CloakBrowser BYOL | 待评估 |
| SKILL overlay／fork 完整产品化 | 长期目标 |
| 自动登记新账号 | 长期目标 |
| 同账号多 Profile | 当前排除，后续由真实需求重新评估 |
| 通用 Browser Agent | 明确非目标 |
| Hosted Browser 服务 | 明确非目标 |
| 自动养号、虚假互动、绕过平台安全 | 明确非目标 |
| V1 Browser Runtime 能力基线独立于站点 SKILL 定义 | 已确认原则 |
| Runtime 能力存在与 Agent 工具暴露／授权分离 | 已确认原则 |
| 任务页与原生选中／前台焦点分离；只读不激活，Viewer 不冒称 OS 前台；OS 窗口／坐标／原生键盘输入需可靠窗口对应，Page／Element 输入按对象事实核验 | 已确认原则 |
| 任务页 A 与观看页 B 分离；交还后默认重观察 A，失联只按可信关系或可验证的明确选页恢复 | V1 收敛约束 |
| 旧私有 patch binding 退役但保留数据、授权、账号、Run／receipt、管理、撤销、查询和恢复 | 已确认原则 |
| Provider 升级先核对正式组合、来源兼容和核心路径，再经明确选择更新绑定；未知版本拒绝、活动实例不热换、不自动交旧数据 | 已确认原则 |
| 首个完整消费端优先为一个第三方 Agent 的已安装 Plugin | V1 收敛约束 |
| 完整 App 产品化在 Plugin 完整体验检查点之后集中推进，必要 owner 控制持续保留 | V1 收敛约束 |

# 附录 C：实施解释与分级验收

本附录解释如何交付本规范，不改变正文及附录 B 的五类决策状态。

1. 第 21 章是能力演进参考，不是瀑布式前置。早期切片可以在最小正式入口中先验证 Provider、受管 Profile、人工接管和一个真实网站消费者；不要求先实现全部对象或合同。
2. 第 22 章是完整 V1 验收，不是每个切片的验收。切片必须明确自身成功、必要拒绝和恢复证据；原型失败可以完成验证任务，但不代表能力已交付。
3. 对象与语义分离不要求独立服务、数据库、队列或状态机。`Activity` 初期优先投影已有 `Run`／receipt；结构校验、业务不变量和现场事实各由其 owner 校验，预检和正式执行复用同一业务判定。
4. 防御作用域不得大于风险作用域。身份、授权、控制权和重复写入保护必须保留；可选证据、观看流或未安装网站 SKILL 不得全局阻断环境管理或通用浏览器。
5. `Profile` 不是网站白名单，账号绑定清单也不是站点访问白名单。没有网站 SKILL 时，Agent 仍可在授权范围内使用通用浏览器能力。
6. 凭据保护不等于禁止 Agent 读取完成授权业务所必需的内容。凭据、授权业务内容、公开资产和诊断摘要必须按不同风险处理。
7. SKILL 不授予权限；通用脚本、CDP 或其他协议工具也不能成为受控模式的授权后门。尚未提供的隔离或保证必须如实说明。
8. Camoufox 是首个工程验证 Provider 并只代表其已有证据范围；Chrome 是显式兼容选择。Obscura 已明确退出当前愿景范围；新增 Provider 必须先通过 Qualification Gate，也不得重新引入 ego-lite／ego-browser 或 Wayfern。
9. 写入结果未知时禁止重放，但允许对原 operation 做安全查询、对账、人工接管和停止后续执行。新结论追加事实，不抹去历史 unknown。
10. `AccountSystem` 是可独立复用并由 SKILL 引用的资产，运行时以用户本地定义为准；没有真实消费者时不为文档结构创建脚手架。

## 推进与验收原则

- 用户或 Agent 的真实路径是交付单元；模型、Schema 和合同只在服务该路径时成为交付物。
- 先验证最可能推翻设计的页面或 Provider 假设，再固化必要接口。
- 每条业务规则只有一个 owner；复用和减法优先，兼容层必须有消费者和退出条件。
- 正向、必要负向和恢复路径一起验证；真实消费者使用实际 pin 的资产。
- 研究可用授权探针；正式产品验收走可安装入口。验证强度与风险相称。
- fixture／mock、真实 Provider、正式安装路径、真实第三方 Agent、真人操作和真实第三方站点分别记录，不互相冒充；`plugin_verified` 只表示已安装 Plugin + 真实 Agent，未测试不写成 `unsupported`。
- 已可见产品目标完整规划到 Milestone 和 FR，保留 V1、原型与长期目标的决策状态；具体 Work Item 在接近实施时滚动细化，只有真实技术或验收阻塞才建立原生 dependency。
- Issue 关闭、PR 合并和 checks 通过都不能单独证明业务能力完成。

本规范是产品方向和决策状态的唯一入口。执行状态只记录在 GitHub Milestone、Project、Issue、PR、checks、review 与 `main` 回读中，不另建持续维护的迁移台账或第二状态机。
