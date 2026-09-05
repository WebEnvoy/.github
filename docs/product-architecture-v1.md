# WebEnvoy v1 产品与架构方向规范

> **文档状态：** v1.0 产品与架构方向规范
> **适用范围：** WebEnvoy 的产品定位、核心对象、权限模型、Profile 与账号管理、Provider 策略、Agent 接入、App、Harbor、Core、Lode／SKILL 的职责边界，以及 V1 实施与验收范围。
> **不包含：** 对当前仓库实现状态、历史 PR、历史架构质量或完成度的评价。
> **重要说明：** 本规范描述目标方向与 V1 约束，不表示相关能力已经完成实现。

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

## 2.2 V1 收敛约束

1. 一个账号最多归属一个正式、可运行的 Profile。
2. 一个 Profile 同一时间最多运行一个浏览器主实例；实例内可以存在多个窗口或标签页。
3. 普通 Profile 复制只复制环境模板，不复制账号绑定和登录状态。
4. 新发现账号不会自动建立正式绑定。
5. 控制权先按实例粒度管理，不在 V1 引入标签页级复杂租约。
6. 首期优先支持一个主要 Agent 宿主。
7. App 任务管理收敛为活动监督、待处理事项、人工接管和结果查看，不建设完整任务编排平台。
8. 多实例界面先实现单实例主视图和多实例概览，再根据原型结果增加分屏或网格。
9. 默认 Provider 选择以 Camoufox 原型验证结果为前提，不在验证前继续横向集成大量 Provider。

## 2.3 待原型验证

1. Camoufox 是否达到默认 Provider 的稳定性和人工使用标准。
2. Camoufox 多实例的 CPU、内存和长期稳定性。
3. Camoufox Profile 环境配置跨重启、跨版本是否保持一致。
4. Playwright Firefox 驱动是否满足首批社媒和店铺场景。
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
- 按供应方规则单独更新。

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
→ 选择 Provider
→ 选择环境模板和权限模板
→ 创建 WebEnvoy 管理的数据目录
→ 生成并持久化设备环境
→ 启动浏览器
→ 完成登录
→ 发现账号
→ 显式登记和绑定
→ 正常使用
```

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

## 7.6 恢复备份

恢复备份不是第二个可运行 Profile。

- 默认不得正常启动；
- 不参与账号归属；
- 只用于迁移失败或数据恢复；
- 恢复为可运行 Profile 时必须重新执行归属和一致性检查。

## 7.7 Provider 不可用

Provider 不可用时：

- 不静默切换；
- 不自动重新生成指纹；
- 不自动更换代理；
- 返回明确诊断；
- 提供修复、更新或迁移选项；
- 不因 Provider 故障改写已经发生的 ExternalOutcome。

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

## 9.1 默认 Provider 的筛选标准

默认 Provider 应尽可能满足：

- 免费使用，不按 Profile 或并发会话收费；
- 浏览器核心和关键环境控制实现开源；
- 允许 WebEnvoy 自主管理 Profile；
- 支持多个独立实例；
- 支持有头运行和人工接管；
- 支持目标桌面平台；
- 支持稳定自动化接口；
- 支持持久化浏览器数据；
- 支持可固定、可复用的设备环境；
- 有明确版本、构建和升级路径；
- 不要求把用户 Profile 托管给第三方。

## 9.2 Camoufox

**[待原型验证]**

Camoufox 是当前默认 Provider 的第一验证对象。

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

## 9.3 官方 Chrome

**[已确认原则]**

官方 Chrome 作为显式可选的兼容性 Provider。

用途包括：

- 网站兼容性；
- 人工浏览基线；
- 某些扩展或浏览器行为需求；
- Provider 对比和诊断。

Chrome 不具备同等级的原生设备环境控制能力，但不得因此被自动判定为不可用。

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
- Camoufox 之外的 Firefox Provider：V1 不横向扩张；
- 更多 Chromium 指纹浏览器：在 Camoufox 原型完成前不并行集成。

## 9.6 Provider Driver 抽象

Harbor 的公共浏览器能力不得长期等同于 CDP。

公共能力应表达：

```text
launchInstance
closeInstance
listPages
openPage
switchPage
pageInfo
snapshot
screenshot / frameStream
click
input
scroll
upload
download
evaluateControlledScript
readState
acquireControl
releaseControl
```

底层分别适配：

```text
Camoufox / Playwright Firefox Driver
Chrome / CDP Driver
未来经过验证的其他 Driver
```

不得为了理论通用性提前建设复杂 Provider 插件平台。

---

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
- Provider 自身自动化特征处理。

### Harbor

负责：

- 生成和保存 Profile 环境配置；
- 启动时正确应用；
- 验证关键配置；
- 管理代理和网络出口；
- 记录实际观测和漂移；
- 管理 Instance 生命周期和控制权。

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
- 发布和保存必须防止重复触发。

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
- 诊断启动问题；
- 查看配置和实际观测；
- 修改允许的非破坏性环境配置；
- 发起迁移。

Provider 安装、更新和修复是否允许由 Agent 直接执行，属于待原型验证；至少必须支持查询和发起需要用户处理的操作。

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
- SKILL；
- BusinessTarget；
- 当前状态；
- 待处理事项；
- 最终业务结果或失败原因。

App 不接管外部 Agent 的完整计划、聊天记录和文档工作流。

---

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
- 系统文件选择器、扩展弹窗等复杂交互可以转到同一实例原生窗口。

## 14.4 技术路线

采用：

> 浏览器独立运行，App 获取同一 Instance 的截图或画面流，并将受控输入发送回该 Instance。

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

---

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

无法确认结果时不得自动重试。

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
→ Agent 重新观察
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

# 21. V1 实施顺序

## 阶段一：对象模型和硬约束

完成：

- AccountSystem；
- Account；
- BusinessTarget；
- Profile；
- ProviderBinding；
- EnvironmentConfiguration；
- AgentPrincipal；
- AgentConnection；
- Grant；
- Instance；
- ControlLease；
- Activity；
- Run；
- ExternalOutcome；
- 一账号一 Profile；
- 同体系单账号；
- discovered identity；
- 导入与迁移语义。

## 阶段二：Camoufox 原型

最低验证：

- 三个独立 Profile 同时运行；
- 独立数据目录；
- 设备配置跨重启稳定；
- Cookie 和账号不串；
- 代理、语言和时区生效；
- 原生窗口人工使用；
- 中文输入；
- 上传下载；
- 富文本；
- 目标网站登录；
- 版本升级与恢复。

“三个 Profile”是原型规模建议，不是长期容量上限。

## 阶段三：Runtime 脱离 App

完成：

- 独立本地 Runtime；
- Profile 管理 API；
- Provider 管理 API；
- AccountSystem 与 Account API；
- Instance 管理 API；
- ControlLease；
- App 与 Agent 共用接口。

## 阶段四：Plugin 与一个 Agent 宿主

完成：

- 安装和连接；
- Principal 与 Grant；
- WebEnvoy 管理 SKILL；
- 通用浏览器 SKILL；
- Profile 创建与启动；
- 账号发现和显式绑定；
- 人工接管；
- 结果返回。

## 阶段五：App 管理台

优先交付：

- Agent 接入；
- AccountSystem；
- Account；
- Profile；
- Provider；
- SKILL；
- Instance；
- Activity；
- 单实例主视图；
- 待处理事项。

## 阶段六：多实例观看

依次验证：

1. 单实例截图；
2. 实例切换；
3. 多实例缩略图；
4. 两实例分屏；
5. 实时画面；
6. 根据价值决定四实例网格。

## 阶段七：首个真实网站 SKILL

完成一条真实纵向链路：

```text
创建或导入 Profile
→ 登录 Account
→ 识别 BusinessTarget
→ Agent 加载 SKILL
→ 完成真实运营任务
→ 用户观看或接管
→ 回读并核验结果
```

---

# 22. V1 验收标准

V1 至少必须证明：

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

# 23. 待原型验证清单

以下方向已经明确，但必须通过工程验证：

- Camoufox 长时间人工浏览体验；
- Camoufox 多实例资源成本；
- Camoufox 与目标社媒、店铺站点兼容性；
- Camoufox 配置跨版本稳定性；
- macOS 实例画面流；
- Playwright Firefox 的实时画面和输入能力；
- 系统文件选择器与原生弹窗边界；
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
> **Camoufox 是默认 Provider 的第一验证对象，官方 Chrome 是显式兼容性 Provider。Provider 在 Profile 创建时确定，后续变更通过迁移完成，不得静默替换。每个 Profile 持久化一套自洽的设备环境和独立浏览器存储，并减少由驱动和运行环境不必要暴露的自动化特征；WebEnvoy 不承诺不可检测，也不提供绕过平台安全机制的能力。**
>
> **网站知识以 SKILL 为主要载体，AccountSystem 模板等共享知识作为独立资产被多个 SKILL 引用。SKILL 决定 Agent 推荐怎样完成任务，Runtime 决定实际提供哪些操作，授权系统决定当前允许执行什么。没有 SKILL 时，Agent 仍可使用通用浏览器能力；有 SKILL 后，应减少探索、试错和错误，并提高账号、经营对象和结果判断的准确性。**
>
> **ConnectionState、InstanceState、ControlState、RunState 和 ExternalOutcome 必须分离。断线、关闭 App、观看失败、实例退出或用户停止后续操作，都不能自动改写网站侧已经发生或可能发生的结果。无法确认写入结果时，必须保留 unknown outcome，并禁止自动重复写入。**

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
| Camoufox 成为默认 Provider | 待原型验证 |
| Camoufox 长期人工使用 | 待原型验证 |
| 实时多实例交互画面 | 待原型验证 |
| Agent 直接安装／更新 Provider | 待原型验证 |
| CloakBrowser BYOL | 待评估 |
| SKILL overlay／fork 完整产品化 | 长期目标 |
| 自动登记新账号 | 长期目标 |
| 同账号多 Profile | 当前排除，后续由真实需求重新评估 |
| 通用 Browser Agent | 明确非目标 |
| Hosted Browser 服务 | 明确非目标 |
| 自动养号、虚假互动、绕过平台安全 | 明确非目标 |

# 附录 C：实施解释与分级验收

本附录解释如何交付本规范，不改变正文及附录 B 的五类决策状态。

1. 第 21 章是能力演进参考，不是瀑布式前置。早期切片可以在最小正式入口中先验证 Provider、受管 Profile、人工接管和一个真实网站消费者；不要求先实现全部对象或合同。
2. 第 22 章是完整 V1 验收，不是每个切片的验收。切片必须明确自身成功、必要拒绝和恢复证据；原型失败可以完成验证任务，但不代表能力已交付。
3. 对象与语义分离不要求独立服务、数据库、队列或状态机。`Activity` 初期优先投影已有 `Run`／receipt；结构校验、业务不变量和现场事实各由其 owner 校验，预检和正式执行复用同一业务判定。
4. 防御作用域不得大于风险作用域。身份、授权、控制权和重复写入保护必须保留；可选证据、观看流或未安装网站 SKILL 不得全局阻断环境管理或通用浏览器。
5. `Profile` 不是网站白名单，账号绑定清单也不是站点访问白名单。没有网站 SKILL 时，Agent 仍可在授权范围内使用通用浏览器能力。
6. 凭据保护不等于禁止 Agent 读取完成授权业务所必需的内容。凭据、授权业务内容、公开资产和诊断摘要必须按不同风险处理。
7. SKILL 不授予权限；通用脚本、CDP 或其他协议工具也不能成为受控模式的授权后门。尚未提供的隔离或保证必须如实说明。
8. Camoufox 是默认 Provider 的首个验证目标，不是已经通过的结论；Chrome 是显式兼容 Provider。不得重新引入 ego-lite／ego-browser 或 Wayfern。
9. 写入结果未知时禁止重放，但允许对原 operation 做安全查询、对账、人工接管和停止后续执行。新结论追加事实，不抹去历史 unknown。
10. `AccountSystem` 是可独立复用并由 SKILL 引用的资产，运行时以用户本地定义为准；没有真实消费者时不为文档结构创建脚手架。

## 推进与验收原则

- 用户或 Agent 的真实路径是交付单元；模型、Schema 和合同只在服务该路径时成为交付物。
- 先验证最可能推翻设计的页面或 Provider 假设，再固化必要接口。
- 每条业务规则只有一个 owner；复用和减法优先，兼容层必须有消费者和退出条件。
- 正向、必要负向和恢复路径一起验证；真实消费者使用实际 pin 的资产。
- 研究可用授权探针；正式产品验收走可安装入口。验证强度与风险相称。
- 只细化当前批和下一批；只有真实技术或验收阻塞才建立原生 dependency。
- Issue 关闭、PR 合并和 checks 通过都不能单独证明业务能力完成。

本规范是产品方向和决策状态的唯一入口。执行状态只记录在 GitHub Milestone、Project、Issue、PR、checks、review 与 `main` 回读中，不另建持续维护的迁移台账或第二状态机。
