# GitHub 真相切换与旧仓映射（dry-run）

本文件是 Work Item [WebEnvoy/.github#9](https://github.com/WebEnvoy/.github/issues/9) 的只读 mapping carrier。读回时间：2026-08-04（GitHub API）。它记录 source URL、状态、milestone、目标入口和证据缺口；不创建目标 issue、不转移对象、不关闭源 issue、不归档仓库。

## Canonical 与 legacy 边界

- canonical 产品开发入口：[`WebEnvoy/WebEnvoy`](https://github.com/WebEnvoy/WebEnvoy) 的 `packages/*`、`apps/desktop` 和 `services/harbor`；Core、App、Harbor 仍是独立进程、独立 owner API 和独立 truth source。
- legacy source/history/backlog/rollback 入口：[`WebEnvoy/App`](https://github.com/WebEnvoy/App) 与 [`WebEnvoy/Harbor`](https://github.com/WebEnvoy/Harbor)。两仓均保持可读、未归档；本批次不接受新实现，不因 banner 或 mapping 改变 archive flag。
- 独立能力资产：[`WebEnvoy/Lode`](https://github.com/WebEnvoy/Lode) 保持独立 MIT、独立可写仓库，通过 version/hash pin 与 compatibility CI 被消费；不迁入 monorepo、不使用 submodule、不成为 runtime runner。
- Harbor 敏感运行事实继续独占 credential、cookie、token、profile、raw DOM、HAR、screenshot；Lode、站点策略和业务结果归一化不进入 Harbor。
- 未读回或目标不存在的关系写为 `unavailable` / `deferred / not-created`，不以标题、相邻 issue 或历史习惯推断 parent、sub-issue、blocked-by、dependency、Project 或 target URL。

## Core closeout truth

Core FR [#339](https://github.com/WebEnvoy/WebEnvoy/issues/339) 与 [#340](https://github.com/WebEnvoy/WebEnvoy/issues/340) 已完成 closeout；milestone [#15 v0.1-product-monorepo](https://github.com/WebEnvoy/WebEnvoy/milestone/15) 已关闭且 `open_issues=0`。Work Item 与合并证据如下：

| Work Item | PR / merge commit | closeout evidence |
| --- | --- | --- |
| [#341](https://github.com/WebEnvoy/WebEnvoy/issues/341) | [#348](https://github.com/WebEnvoy/WebEnvoy/pull/348) → `3725cb9c3162548d77392fdb6e69b7ca28748dc7` | [issue evidence](https://github.com/WebEnvoy/WebEnvoy/issues/341#issuecomment-5166989703) |
| [#342](https://github.com/WebEnvoy/WebEnvoy/issues/342) | [#349](https://github.com/WebEnvoy/WebEnvoy/pull/349) → `a8325687abf01833a4b477f39f66cca4c9979ce1` | [issue evidence](https://github.com/WebEnvoy/WebEnvoy/issues/342#issuecomment-5168651787) |
| [#343](https://github.com/WebEnvoy/WebEnvoy/issues/343) | [#350](https://github.com/WebEnvoy/WebEnvoy/pull/350) → `408853bfe53da14326c0dc334646f76397e6ad7b` | [issue evidence](https://github.com/WebEnvoy/WebEnvoy/issues/343#issuecomment-5169322093) |
| [#344](https://github.com/WebEnvoy/WebEnvoy/issues/344) | [#351](https://github.com/WebEnvoy/WebEnvoy/pull/351) → `26704259b5acb428a29e954eebad144b14bc640e` | [issue evidence](https://github.com/WebEnvoy/WebEnvoy/issues/344#issuecomment-5169619131) |
| [#345](https://github.com/WebEnvoy/WebEnvoy/issues/345) | [#352](https://github.com/WebEnvoy/WebEnvoy/pull/352) → `306b0b817d0bcca4f5c6762f366b3f1f5416824a` | [issue evidence](https://github.com/WebEnvoy/WebEnvoy/issues/345#issuecomment-5170177502) |
| [#346](https://github.com/WebEnvoy/WebEnvoy/issues/346) | [#353](https://github.com/WebEnvoy/WebEnvoy/pull/353) → `39a6454ed55fe04873153c2fb3466d47784b3200` | [issue evidence](https://github.com/WebEnvoy/WebEnvoy/issues/346#issuecomment-5170913732) |
| [#347](https://github.com/WebEnvoy/WebEnvoy/issues/347) | [#354](https://github.com/WebEnvoy/WebEnvoy/pull/354) → `94e70483b8fbeff45a3fcc975c4c94269d983393` | [issue evidence](https://github.com/WebEnvoy/WebEnvoy/issues/347#issuecomment-5171503566) |

#347 的 `no_release` artifact readback：hosted run `30850165337`，artifact `8870340324`，digest `sha256:bdad356186e2eb8fda7c338f78277e3ec2a08540419796161cb50d1ae82472b7`；post-merge host readback run `30851598937` / artifact `8870798139`。证据 carrier 是 `WebEnvoy/WebEnvoy/docs/verification/core-347-clean-checkout.md` 与 `core-347-release-artifact-provenance-v1.json`，包含独立 Core/Harbor、精确 Lode pin、四态 offline smoke、旧多仓 rollback 和 no-release/no-archive 边界。

## Legacy inventory（逐项 source URL）

### App：17 个 open issues

目标位置统一为 `WebEnvoy/WebEnvoy/apps/desktop`；目标 issue、parent、blocked-by、Project 和 PR mapping 尚未创建，状态记为 `deferred / not-created`。源 issue 保留原状态和 milestone，不关闭、不转移。

- milestone 14：[#290](https://github.com/WebEnvoy/App/issues/290)、[#261](https://github.com/WebEnvoy/App/issues/261)、[#258](https://github.com/WebEnvoy/App/issues/258)、[#257](https://github.com/WebEnvoy/App/issues/257)、[#256](https://github.com/WebEnvoy/App/issues/256)、[#244](https://github.com/WebEnvoy/App/issues/244)、[#243](https://github.com/WebEnvoy/App/issues/243)、[#238](https://github.com/WebEnvoy/App/issues/238)、[#237](https://github.com/WebEnvoy/App/issues/237)、[#236](https://github.com/WebEnvoy/App/issues/236)、[#233](https://github.com/WebEnvoy/App/issues/233)。均为 `OPEN`；[milestone #14](https://github.com/WebEnvoy/App/milestone/14) 当前保持 open。
- milestone 15：[#289](https://github.com/WebEnvoy/App/issues/289)、[#288](https://github.com/WebEnvoy/App/issues/288)、[#287](https://github.com/WebEnvoy/App/issues/287)、[#286](https://github.com/WebEnvoy/App/issues/286)、[#245](https://github.com/WebEnvoy/App/issues/245)、[#240](https://github.com/WebEnvoy/App/issues/240)。均为 `OPEN`；[milestone #15](https://github.com/WebEnvoy/App/milestone/15) 当前保持 open。
- App PR：[PR #291](https://github.com/WebEnvoy/App/pull/291) 为 `OPEN`，head `b3b8f6318512fbf9c6c1e61b3a1f84376bf2b079`；它仍属于 legacy App backlog，target PR 记为 `deferred / not-created`，不关闭、不转移。
- 跨仓可追溯 Lode PR：[Lode PR #291](https://github.com/WebEnvoy/Lode/pull/291) 已合并为 `6238d3f9de0cd09157c9769e27d90174c299406a`；它是 Lode pin 事实，不是 App issue 的 target PR。

### Harbor：14 个 open issues

目标位置统一为 `WebEnvoy/WebEnvoy/services/harbor`；目标 issue、parent、blocked-by、Project 和 PR mapping 尚未创建，状态记为 `deferred / not-created`。源 issue 保留原状态和 milestone，不关闭、不转移。

- milestone 12：[#278](https://github.com/WebEnvoy/Harbor/issues/278)、[#267](https://github.com/WebEnvoy/Harbor/issues/267)、[#263](https://github.com/WebEnvoy/Harbor/issues/263)、[#259](https://github.com/WebEnvoy/Harbor/issues/259)、[#254](https://github.com/WebEnvoy/Harbor/issues/254)、[#252](https://github.com/WebEnvoy/Harbor/issues/252)、[#241](https://github.com/WebEnvoy/Harbor/issues/241)、[#219](https://github.com/WebEnvoy/Harbor/issues/219)、[#218](https://github.com/WebEnvoy/Harbor/issues/218)、[#203](https://github.com/WebEnvoy/Harbor/issues/203)。均为 `OPEN`；[milestone #12](https://github.com/WebEnvoy/Harbor/milestone/12) 当前保持 open。
- milestone 13：[#266](https://github.com/WebEnvoy/Harbor/issues/266)、[#265](https://github.com/WebEnvoy/Harbor/issues/265)、[#264](https://github.com/WebEnvoy/Harbor/issues/264)、[#251](https://github.com/WebEnvoy/Harbor/issues/251)。均为 `OPEN`；[milestone #13](https://github.com/WebEnvoy/Harbor/milestone/13) 当前保持 open。
- readiness milestone 14：[milestone #14](https://github.com/WebEnvoy/Harbor/milestone/14) 保持 open，已完成的 [Harbor #352](https://github.com/WebEnvoy/Harbor/issues/352) / [PR #353](https://github.com/WebEnvoy/Harbor/pull/353) 仍作为跨仓 owner-facts 证据；不存在待迁入的 target issue。

## Source flags and rollback

截至本次 readback，以下仓库均为 `public`、`archived=false`、默认分支 `main`：[`WebEnvoy/WebEnvoy`](https://github.com/WebEnvoy/WebEnvoy)、[`WebEnvoy/App`](https://github.com/WebEnvoy/App)、[`WebEnvoy/Harbor`](https://github.com/WebEnvoy/Harbor)、[`WebEnvoy/Lode`](https://github.com/WebEnvoy/Lode)、[`WebEnvoy/.github`](https://github.com/WebEnvoy/.github)。本批次没有 transfer、close、archive、delete、visibility、permission 或 ruleset 变更。

可恢复 rollback target 由 #347 carrier 绑定：monorepo baseline `26704259b5acb428a29e954eebad144b14bc640e`，Core `2c401cf90c0cf7150e8156b904975cefaf435fa8`，Harbor `f9e13311ccd3f80cf8ef54cb97245a42da49882b`，Lode `1fbef74b4bf1b4f0a86aacd885386d7a62181207`。旧仓、旧入口、历史和 artifact 不因 mapping 文档改变；回滚只使用独立 clean checkout 和记录的安装/验证命令，不 reset、rewrite 或删除源历史。

## Explicit-confirmation gate（#10）

[WebEnvoy/.github#10](https://github.com/WebEnvoy/.github/issues/10) 是入口切换与旧仓退役的明确授权门。只有在 W9/W10 release/readback/rollback evidence、完整 snapshot、source mapping、所有 repo URL/visibility/archive flags、恢复步骤和 owner 明确确认均已读回后，才能单独授权更新组织 repo map、profile、descriptions/default execution entry，或考虑 App/Harbor archive。缺一项即保持现状并标记 `deferred`。

本 PR 只写 dry-run inventory 和 canonical/legacy 说明；不把 issue close、PR merge、milestone close 或单次 build 当作 archive/entry-switch 授权，也不创建目标 issue、Project 或替代 dependency 关系。
