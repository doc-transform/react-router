# React Router 开放治理模型 <!-- omit in toc -->

- [概述](#overview)
- [设计目标](#design-goals)
- [指导委员会](#steering-committee)
- [Bug/Issue 流程](#bugissue-process)
- [新功能流程](#new-feature-process)
- [新功能阶段](#new-feature-stages)
  - [阶段 0 — 提案](#stage-0--proposal)
  - [阶段 1 — 评审](#stage-1--consideration)
  - [阶段 2 — Alpha](#stage-2--alpha)
  - [阶段 3 — Beta](#stage-3--beta)
  - [阶段 4 — 稳定化](#stage-4--stabilization)
  - [阶段 5 — 稳定](#stage-5--stable)
- [会议记录](#meeting-notes)

## 概述

React Router 自 2014 年以来一直存在，主要由 [Michael Jackson](https://x.com/mjackson) 和 [Ryan Florence](https://x.com/ryanflorence) 开发和监督。在 2021 年推出 [Remix](https://remix.run/) 之后，随着 Remix 团队的组建，以及 Remix v2 合并到 React Router v7[^1][^2]，该项目从[创始人领导](https://www.redhat.com/en/blog/understanding-open-source-governance-models)模型转变为基于征求意见（RFC）流程运作的"指导委员会"（SC）模型。

[^1]: https://remix.run/blog/merging-remix-and-react-router

[^2]: https://remix.run/blog/incremental-path-to-react-19

本文档将概述 React Router 将如何继续演进，以及新功能将如何进入代码库。这是一份持续更新的文档，将根据需要进行修改以反映流程的未来变化。

## 设计目标

在考虑 RFC 是否接受时，应考虑以下设计目标：

- **少即是多**。React Router 在过去几年中获得了*大量*功能，但随之而来的是大量新的 API 表面。现在是时候专注于核心功能，并旨在*在不牺牲能力的情况下*减少 API 表面。这可能有多种形式，例如将几个现有 API 合并为一个 API，或弃用当前 API 而采用新的 React API。
- **聚焦路由和数据。** 专注于核心的路由器集成/路由器中心 API，避免添加可以在用户空间实现的一等 API。
- **简单的迁移路径。** 主要版本升级不必令人痛苦。破坏性变更应在 future 标志后面实现。弃用应提前在代码和文档中正确标记。在主要版本发布之前应添加控制台警告，引导开发者开始准备升级所需的更改。
- **最低公共模式。** 功能在最低可能的模式（`声明式 -> 数据 -> framework`）中添加，然后被更高级别的模式利用。这确保了最大数量的 React Router 应用可以利用它们。
- **定期发布节奏。** 力争每年进行一次主要 SemVer 发布，以便应用开发者可以提前做好准备。

## 指导委员会

指导委员会将负责接受 RFC 进行评审、批准以"不稳定"状态落地功能的 PR，以及批准将功能稳定化到 React Router 中的稳定化 PR。

SC 最初将由 Remix 团队开发者组成：

- Matt Brophy ([`@brophdawg11`](https://github.com/brophdawg11))
- Pedro Cattori ([`@pcattori`](https://github.com/pcattori))
- Mark Dalgleish ([`@markdalgleish`](https://github.com/markdalgleish))
- Jacob Ebey ([`@jacob-ebey`](https://github.com/jacob-ebey))
- Brooks Lybrand ([`@brookslybrand`](https://github.com/brookslybrand))
- Sergio Xalambrí ([`@sergiodxa`](https://github.com/sergiodxa))
- Bryan Ross ([`@rossipedia`](https://github.com/rossipedia))

未来，我们可能会将少数深度参与的社区成员加入 SC。

为减少摩擦，SC 将主要通过 GitHub 异步运作，但可根据需要安排私人和/或公开会议。

## Bug/Issue 流程

由于 React Router 应用数量庞大，我们必须对提交 issue 的流程有一定的严格要求，以避免 GitHub 上的过载。

- **所有** bug 必须有一个**最小化**且**可运行**的复现[^3]
  - *最小化*意味着不是简单地指向一个已部署的网站或你现有应用的一个分支
  - *可运行*意味着是一个可以看到问题的工作应用，而不仅仅是需要手动组装成运行应用的几段代码片段
  - 复现的首选方式为：
    - **Framework 模式**：[StackBlitz](https://reactrouter.com/new) 或基于 [`bug-report-test.ts`](integration/bug-report-test.ts) 的带有失败集成测试的 GitHub fork
    - **数据/声明式模式**：[CodeSandbox (TS)](https://codesandbox.io/templates/react-vite-ts) 或 [CodeSandbox (JS)](https://codesandbox.io/templates/react-vite)
  - 如果 StackBlitz/CodeSandbox 不可用，基于全新 `npx create-react-router` 应用的 GitHub 仓库也可接受
  - 只有在极特殊情况下才会接受代码片段或非最小化复现
- Issue 审查
  - 不符合上述标准的 issue 将被关闭并指向本文档
  - 非 issue（功能请求、使用问题）也将被关闭并附带本文档链接
  - SC 将定期对 issue 进行分类
- 修复 Issue
  - SC 将使用 `Accepting PRs` 标签标记优质的社区 issue
  - 这些 issue 通常是可能有较小修复面的问题
  - 但是，任何人都可以处理任何 issue，但如果修改面过大不利于核心团队成员快速审查，则不能保证 PR 会被接受

[^3]: https://antfu.me/posts/why-reproductions-are-required

## 新功能流程

新功能添加到 React Router 的流程将遵循一系列松散基于 [TC39 流程](https://tc39.es/process-document/)的阶段。需要注意的是，进入任何给定阶段并不意味着 RFC 会继续推进。这些阶段将充当漏斗，进入后期阶段的 RFC 越来越少，只有最强的 RFC 才能以稳定方式进入 React Router 版本。

> [!NOTE]
> 大多数新的社区驱动的 React Router 功能将经历所有阶段。某些功能如果足够简单或显而易见，可能会跳过阶段直接作为稳定功能实现。

下表提供了各阶段的高层概述，但请参阅下面的各阶段部分以获取有关阶段和推动 RFC 通过各阶段的流程的更详细信息。一旦功能达到阶段 2，它将被添加到[路线图](https://github.com/orgs/remix-run/projects/5)中，可以在其中跟踪其进展。

| 阶段 | 名称   | 准入标准                                                                    | 目的                                                                                                                                                                                                                                                          |
| ---- | ------ | --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0    | 提案   | 在 GitHub 上开启提案讨论                                                    | 我们从 GitHub 提案开始，以提供最低的 RFC 提交门槛。任何人都可以提交 RFC，社区成员可以在 SC 初始参与之前进行审查、评论、投票。                                                                                                                                 |
| 1    | 评审   | 2 名 SC 成员接受提案                                                        | 评审阶段是传入 RFC 的第一个"漏斗"，SC 可以正式表达对更受欢迎 RFC 的兴趣。我们只需要 2 名 SC 成员表达兴趣即可将 RFC 推进到**评审**阶段，以允许在 **Alpha** 阶段进行低摩擦的功能实验。                                                                          |
| 2    | Alpha  | 打开 PR 以"不稳定"状态实现功能                                              | **Alpha** 阶段是 RFC 的下一个漏斗。一旦 SC 在**评审**阶段表达了兴趣，我们就开放 RFC 进行示例 PR 实现，并为社区成员提供 alpha 测试功能的机制，而无需在 React Router SemVer 发布中发布任何内容。此阶段允许在运行应用中评估 RFC，并考虑 RFC 在代码中的实际实现。 |
| 3    | Beta   | 2 名 SC 成员批准 PR，表示接受该 PR 作为不稳定 API                           | RFC 在足够多的 SC 成员对 beta 功能的代码感到满意，并且从 alpha 测试者那里看到功能按预期工作的积极反馈后，进入 **Beta** 阶段。一旦 **Alpha** 阶段的 PR 获得足够的 SC 批准，它将被合并并包含在下一个 React Router 版本中。                                      |
| 4    | 稳定化 | 在 Beta 阶段至少 1 个月，并打开 PR 稳定化 API。该 PR 还应包含新功能的文档。 | **稳定化**阶段确保不稳定功能有足够的时间供应用更新其 React Router 版本并选择加入 beta 测试。我们不想匆忙通过 beta 测试，以便在稳定化功能之前获得最大限度的反馈。                                                                                              |
| 5    | 稳定   | 至少 50% 的 SC 成员批准 PR，表示接受该 PR 作为稳定 API                      | RFC 在足够多的 SC 成员对稳定功能的代码感到满意，并且从 beta 测试者那里看到功能按预期工作的积极反馈后完成并进入**稳定**阶段。一旦 **Beta** 阶段的 PR 获得足够的 SC 批准并在 **Beta** 阶段停留了所需的时间，它可以被合并并包含在下一个 React Router 版本中。    |

## 新功能阶段

### 阶段 0 — 提案

- 所有新功能在 GitHub 提案讨论中撰写 RFC 时从**阶段 0 — 提案**开始
- 任何人都可以撰写 RFC，包括核心团队成员和社区成员
- RFC 应概述新功能的用例、为什么当前 API 不足以满足用例，并提供功能的潜在 API 设计
- 提案应清晰、简洁，并提供足够的上下文供指导委员会（SC）和社区评估其价值
- 社区对提案的投票用作 SC 兴趣和需求的信号 — 投票更高的 issue 更有可能被 SC 成员考虑
- 在此阶段，社区成员可以随意在仓库的 fork 中进行示例实现并在 RFC 中提供链接，但在达到阶段 1 之前**不应**打开 PR

### 阶段 1 — 评审

- 当 2 名 SC 成员表示对该想法作为 React Router 有价值补充的兴趣/支持时，提案进入**阶段 1 — 评审**
- 这些最初的支持 SC 成员将成为该功能的推动者，并将大致负责引导该功能通过 RFC 流程的各个阶段
- 在此阶段，提案有资格接受来自核心团队或社区成员的示例 PR 实现
- SC 将在此阶段表明这是一个开放给社区 PR 的功能还是核心团队更愿意处理的功能
- 如果我们接受社区 PR，将为 RFC 添加 `accepting-prs` 标签
- 此阶段的所有 PR 应以"不稳定"方式实现功能（通常在 future 标志或 API 上使用 `unstable_` 前缀）

### 阶段 2 — Alpha

- 一旦打开了以 `unstable_` 状态实现功能的 PR，提案进入**阶段 2 — Alpha**
- 在此阶段，我们应为提案开一个 Issue 并将其添加到[路线图](https://github.com/orgs/remix-run/projects/5)
- 我们将移除 `accepting-prs` 标签并添加 `🗺️ Roadmap` 标签，表示此 RFC 正式进入路线图
- 在此阶段，我们在将任何工作合并到 React Router 仓库*之前*寻求早期社区测试 — 因此这些 PR 应提供一种机制让社区成员可以选择加入 alpha 测试
  - 维护者可以通过添加 `alpha-release` 标签从 PR 分支触发 alpha 发布，这将启动一次实验性发布并将其评论回 PR
  - 因为 alpha 发布可能包含已提交到 `dev` 但尚未在稳定版本中发布的其他工作，它可能并非在所有情况下都适合测试
  - 在这些情况下，PR 作者也可以在评论中添加 `.patch` 文件的内容，供人们通过 [patch-package](https://www.npmjs.com/package/patch-package) 或 [pnpm patch](https://pnpm.io/cli/patch) 使用
- 来自 alpha 测试者的反馈被认为是进一步推进的关键
- PR 还应包含记录新 API 的 changeset 用于发布说明
- SC 成员将通过 GitHub review 审查和批准 PR
- 此阶段的批准传达：
  - 该功能对 React Router 有价值
  - API/代码足以进行不稳定/beta 测试，但可能需要进一步迭代
  - 代码不要求处于最终状态，但必须以不会对 API 的其他区域引入回归的方式编写
  - 我们已从 Alpha 测试者那里看到足够的积极反馈来推进该功能

### 阶段 3 — Beta

- 一旦从 2 名 SC 成员获得**阶段 2 — Alpha** PR 批准并合并到 `dev`，提案进入**阶段 3 — Beta**
  - SC 成员编写 `unstable_` PR 算作隐式批准，因此在这些情况下需要额外 1 名 SC 成员的明确批准
- 这将在 `nightly` 版本和下一个正常 SemVer 版本中包含该功能，供更广泛的 beta 测试使用 `unstable_` 标志

### 阶段 4 — 稳定化

- 在**阶段 3 — Beta** 中至少 1 个月后，且已打开 PR 移除 `unstable_` 前缀并稳定化功能，提案进入**阶段 4 — 稳定化**
- 稳定化 PR 应为功能添加适当的文档
- SC 成员将通过 GitHub review 审查和批准 PR
- 此阶段的批准传达：
  - 已从 beta 测试者那里收到足够的社区反馈，可以信任 API 的设计和实现
  - 代码达到生产质量且经过充分测试，没有相关的回归
  - PR 包含稳定功能的文档

### 阶段 5 — 稳定

- 一旦从至少 50% 的 SC 成员获得**阶段 4 — 稳定化** PR 批准并合并到 `dev`，提案进入**阶段 5 — 稳定**
  - SC 成员编写稳定化 PR 算作隐式批准
- 这将在 `nightly` 版本和下一个正常 SemVer 版本中包含稳定功能

## 会议记录

本节记录了 React Router 指导委员会会议的记录：

<!-- TEMPLATE
<details>
  <summary>YYYY-MM-DD 会议记录</summary>

  ...
</details>
-->

<details>
<summary>2025-09-23 会议记录</summary>

**摘要**

Brooks 宣布计划在 7.9.2 中发布不稳定的 framework RSC 支持和 `fetcher.unstable_reset()` API。Matt 和 Pedro 讨论了拆分 Ryan 关于 `useRouteLoaderData` 类型安全的提案，将"路由器数据"与"路由数据"分离。Bryan 和 Matt 审查了新的观测 API 提案。Matt 和 Jacob 决定关闭几个与 ESLint 配置、OpenTelemetry 和模块联邦相关的 issue。

**详情**

- 7.9.2 将包含不稳定的 RSC framework 模式支持以及 `fetcher.unstable_reset()` API
- 团队审查了当前的观测 POC 实现：
  - RFC：https://github.com/remix-run/react-router/discussions/13749
  - POC PR：https://github.com/remix-run/react-router/pull/14377
  - 当前的 `instrumentRouter`/`instrumentRoutes` API 应该足以支持在其之上分层实现各种日志/追踪
  - React Router 文档可以展示几种可观测性实现的简单示例（日志、OTEL、`performance.mark`/`measure`），但将依靠社区提供特定可观测性方案的包
  - Jacob 提出了一个很好的观点，当前 API 的设计允许的不仅仅是观测，因为人们可以修改现有的处理器参数，所以 Matt 将研究提供只读信息子集的方法来禁止这种情况，因为这不是预期用例，可能会以不可预见的方式被滥用
  - Matt 还将尝试潜在的观测工具，看看是否值得发布还是只放在文档中
- 委员会审查并同意将 2 个新 RFC 推进到"评审"阶段：
  - [预渲染并发](https://github.com/remix-run/react-router/discussions/14080)
  - [每路由 Layout 组件](https://github.com/remix-run/react-router/discussions/13818)
- Matt 将在 ESLint issue 上回复以关闭它，并将 OpenTelemetry issue 指向新的观测方案
- Pedro 将开始路由相关的工作并尝试提交 PR，一旦 PR 打开，我们也将在路线图上创建一个 issue
- Jacob 将与 Zach 确认他对[当前模块导入相关工作](https://github.com/remix-run/react-router/pull/12638)的兴趣，Matt 将在 issue 上添加评论询问是否需要，如果一周内没有回复则关闭它。

</details>

<details>
<summary>2025-09-08 会议记录</summary>

**摘要**

Matt、Bryan、Mark 和 Pedro 讨论了各种功能的进展，包括中间件、context、`onError` 功能和 RSC framework 模式，大多数接近完成或已发布。Matt 和 Bryan 还探索了可观测性和 OpenTelemetry 与 Sentry 和 React Router 的集成，考虑将 OpenTelemetry 作为 JavaScript 监控的潜在标准。团队决定专注于当前正在进行的项目，而不是审查和接受额外的提案，因为已经有 10 多个提案在进行中。

**详情**

- 路线图审查和发布进展
  - Matt 通过审查公共路线图开始会议，从[中间件](https://github.com/remix-run/react-router/issues/12695)和 [context](https://github.com/remix-run/react-router/issues/14055) 开始，它们已合并到 dev 并等待 7.9.0 版本的预发布
  - Bryan 确认 [`onError`](https://github.com/remix-run/react-router/issues/12958) 功能在 7.8.2 中发布，工作正常并提供了预期数据
  - Mark 指出 RSC framework 模式的初始版本不会功能完整，但已接近完成，主要剩余任务是渲染期间的错误处理（[RFC](https://github.com/remix-run/react-router/issues/11566)）
- 即将推出的功能和 API 讨论
  - Pedro 讨论了 `useRouterState` hook，指出 Ryan 的注意力在其他地方，但他们有兴趣重新审视它以获得类型安全，并可能替换 `useRouteLoaderData` hook
  - Brooks 和 Pedro 同意 `useMatches` API 存在问题，特别是在类型安全方面，并建议找到不依赖它的解决方案
  - 我们可以保持区分：data 模式中使用的 hook 类型安全性低于 framework 模式中的 typegen 等价物，所以 `useRouterState().matches` 类型安全性低于 `Route.ComponentProps["matches"]` 可能是可以接受的
  - [RFC](https://github.com/remix-run/react-router/issues/13073)
- 可观测性和 OpenTelemetry 集成
  - Matt 和 Bryan 讨论了[可观测性](https://github.com/remix-run/react-router/discussions/13749)功能，旨在改善 Sentry 与 React Router 应用的集成
  - Bryan 解释说严格的基于事件的系统不支持 OpenTelemetry，因为 OpenTelemetry 需要在 span 内绑定代码执行，而事件是瞬时的
  - 他们正在考虑 React Router 是否应该全面拥抱 OpenTelemetry，因为它似乎正在成为 JavaScript 监控的事实标准，这可能取代对单独事件系统的需求
- 会议总结和后续步骤
  - Matt 宣布 7.9.0 版本的预发布将很快发布，完整版本预计在本周末前发布
  - Bryan 确认重复 loader issue 的修复将包含在此版本中
  - 团队决定不给自己增加额外任务，专注于当前正在进行的项目

**行动项**

- Mark 将致力于稳定化 split route modules 和 Vite environment API 标志
- Matt 将通读 SvelteKit 博客文章以了解他们的 OpenTelemetry 集成方案
- Matt 将在 7.9.0 发布后合并不稳定的 [`fetcher.reset()`](https://github.com/remix-run/react-router/issues/14207) 工作
- Matt 将尝试尽快接手 [`<Link onPrefetch>`](https://github.com/remix-run/react-router/discussions/12375) 任务
- Matt 和 Pedro 将在线下同步，确定合并 hook 的哪些部分可以通过 typegen 更好地完成，并确定需求（[RFC](https://github.com/remix-run/react-router/issues/13073)）
</details>

<details>
<summary>2025-11-04 会议记录</summary>

SC 审查了 React Router v8 开放提案中的项目

- 确认放弃 CJS 构建，仅使用 ESM 构建的计划
  - 我们计划在 2026 年第二季度发布 RR v8，与 Node 20 的 EOL 时间很好地对齐
  - v8 的最低 Node 版本为 22.12，这样 `require(esm)` 功能就不再在[实验性标志](https://nodejs.org/docs/latest-v22.x/api/modules.html#loading-ecmascript-modules-using-require)后面
- 今后我们将力争在同一个第二季度时间段内进行年度主要版本发布
- 我们希望尝试将 `useRouterState` 纳入 v8，作为 `unstable_useRoute` 的另一半
- 我们认为子资源完整性（SRI）已准备好稳定化，但我们希望联系一些现有用户和/或专家来确认实现是否有效
- 讨论了 `unstable_optimizedDeps` 功能，确认它将在 V8 中保持不稳定，然后转而支持 RollDown 进行伪弃用
  - 对 RollDown 的完整 bundle 模式限制可扩展性有一些担忧，所以我们可能需要等到 rolldown 准备好测试
- 决定不将"类型安全匹配"作为 V8 的即时必需品，因为 API 变动较大
- RSC 实现在 V8 中不会有稳定的 API，但将在后续次要版本中发布
  - 我们不会在那时弃用现有 API，因为不是每个人都应该被迫使用 RSC
- `Vite environment API` 和 `split route modules` 接近稳定化
- 审查了一个停止在 loader 中进行 URL 规范化的新 RFC

</details>

<details>
<summary>2025-11-18 会议记录</summary>

Matt 提到打开 PR 以稳定化 `fetcher.reset` 和客户端 `onError`

**稳定化 Fetcher Reset 和客户端错误处理**

Matt 提到打开 PR 以稳定化 `fetcher.reset` 和客户端 `onError`。对于客户端 `onError`，Matt 做了一个更改，通过将 `location` 和 `params` 传递给错误处理器来与服务器上的 `handleError` 保持一致，目标是不推迟 API 的稳定化。Bryan 建议考虑将模式添加到错误信息中，Matt 同意这对 Sentry 中的错误过滤很有用。

**稳定化其他路由器 API**

Matt 与 Mark 确认 `split route modules` 和 `environment API` 都已准备好稳定化。计划是将这些稳定化批量纳入一个次要版本。其他功能如可观测性和过渡标志仍然太新，不适合稳定化。

**自定义导航的退出标志**

Matt 讨论了快速推进一个标志，允许用户选择不将自己的导航包装在 `startTransition` 中。这是需要的，因为当前实现在自定义导航被包装在 `startTransition` 中时存在与导航状态不显示相关的 bug，特别影响 `useSyncExternalStore` 并导致搜索参数的轻微撕裂问题。Matt 提到如果需要，我们可以 fork 并快速发布此不稳定标志的 `false`（退出）版本。

**类型安全 Fetcher 讨论**

讨论转向了备受期待的类型安全 fetcher 功能。Bryan 建议 fetcher 的定义应在创建时绑定到路由，因为 fetcher 解析为单个处理器（action 或 loader），所有类型签名粘合都在那里发生。一个挑战是当路径匹配多个 loader（如布局路由和索引路由）时如何解决歧义。

**路由 ID vs 模式用于类型安全 Fetcher**

小组讨论了使用路由 ID 还是路径模式来标识路由。Bryan 和 Jacob 同意参数应在 fetcher 的调用处接受。Mark 提出了使用路由 ID 可能需要查询完整 manifest 的担忧，这在只有少量路由在运行时已知的"迷雾"架构中可能存在问题。他们同意使用模式，它在某种意义上兼作路由 ID，且不需要查询 manifest 来构建 URL。

**类型安全 Fetcher 的新 Hook 提案**

Bryan 提议将 `useFetcher` 拆分为两个独立的 hook：`useRouteLoader` 和 `useRouteAction`，分别显式绑定到 loader 和 action。这种分离是有益的，因为 loader 主要关注 GET 方法，而 action 可以处理多种方法（POST、PUT 等）。新 hook 将返回一个包含状态/数据对象和命令式方法（如 `submit` 或 `load`）的数组，Matt 和 Mark 都喜欢这种模式。

**使用 React API 简化状态跟踪**

对话转向了将新 hook 与现代 React API 对齐，特别是 React 19 中的那些。Bryan 建议路由器可以通过使用 React 的 `useTransition` 和 `useOptimistic` hook 将状态跟踪转移到用户空间，从而产生一个更精简的抽象。这将替代现有的 `fetcher.state`（idle、loading、submitting）和 `fetcher.form`。

**利用 `useActionState` 和 Form Actions**

Jacob 指出，为表单的 action 使用异步函数还允许使用 React 的 `useActionState` hook，它可以跟踪表单的 pending 状态，进一步简化 API。这种设计还将使 `fetcher.form` 可以被弃用，转而使用标准 React 表单。然而，小组注意到这种方法适用于支持 JS 的表单，而非 RSC 世界中没有 JavaScript 的渐进增强表单。

**调用处重新验证退出**

小组讨论了调用处重新验证退出的社区 PR，特别是调用处选项如 `shouldRevalidate: false` 是否应覆盖路由现有的 `shouldRevalidate` 函数的开放问题。Jacob 和 Bryan 同意 Sergio 的建议，即调用处选项应设置传递给路由的新默认值，本质上是向上冒泡，以避免覆盖所有重新验证可能带来的潜在支持问题和数据完整性问题。

**重新验证退出的命名约定**

Bryan 建议将传递给 `submit` 的选项命名为 `defaultShouldRevalidate` 以保持一致性。Matt 同意这个建议。
Bryan 和 Matt 讨论了路由重新验证默认设置的实现，Bryan 对人们可能误用硬绕过表示担忧，但同意传入默认值允许路由逻辑仍然决定重新验证。Matt 强调这个默认值对于有许多路由需要共同重新验证行为的应用来说是一个简单的胜利，避免了更改五十个路由的需要，同时仍然允许有自己逻辑的特定路由覆盖默认值。他们同意路由应始终是重新验证的最终权威。

**默认重新验证的范围和粒度**

Jacob 提议设置特定路由的默认值，可能是为父路由但不是视图，用于涉及提交的场景，但 Matt 否定了这一点，指出路由已经有一个专门的位置用于它们的逻辑。Bryan 也建议更细粒度的控制将导致过多的复杂性。团队得出结论，默认值不应允许函数，因为 Jacob 认为它应该从本地状态派生。

**'default revalidate' 的实现**

Matt 确认喜欢 "default revalidate" 这个名称，并确定它应适用于所有命令式 hook，包括 navigate 和 submit。Bryan 和 Matt 同意在 navigate 时让路由保持最终控制重新验证是合理的。Matt 提到这个功能有一个现有的 PR，他们计划更新它。

</details>

<details>
<summary>2025-12-02 会议记录</summary>

**会议状态和稳定化**

Matt 分享了三个稳定化已推出：`environment API future 标志`、`split route modules future 标志`和 `fetch error reset`。Matt 指出 `onError` 将在下一个版本中发布，因为最后一次内部重构需要解决严格模式中的潜在重复报告问题。

**`useRoute` 和类型安全 Fetcher**

Matt 讨论了完成 `useRoute` 的另一半——`useRouterState` 功能的计划，并将它们作为稳定化的配套。Pedro 同意，强调在稳定化之前需要类型安全 fetcher 方案与 `useRoute` 保持一致，以避免如果后来更改基于 ID 的方案就会有多种做同一件事的方式。

**从 Babel 迁移到 SWC/Oxide 以提升性能**

Mark 提出了从 Babel 切换到 SWC/Oxide 以提升速度的提案，指出 `split route modules` 的稳定化增加了在 Babel 中发生的转换量。Pedro 表示支持加速，但质疑优先级，因为他们尚未看到 Babel 成为瓶颈的真实用例，指出当前 HMR 时间低于 40 毫秒。

**性能瓶颈和 Rollup 集成**

Pedro 解释说一个更大的架构问题——需要对所有路由进行完整遍历以创建 manifest——导致了性能问题，使开发服务器启动时间与路由数量成正比。Mark 澄清 Rollup 加速的是构建而非开发，Pedro 建议进行性能分析以确定 Babel 是否真的是 50 毫秒开销中的瓶颈。Matt 建议让社区提供硬数据，并可能等待看 Rollup 是否能通过 AST 管线 API 缓解问题，这可能需要重写转换。Matt 请 Mark 和 Pedro 在提案上评论，表示感兴趣但需要更多瓶颈证据。

**Fetcher 错误处理和命令式使用**

讨论回到了关于 fetcher 错误处理和完成的现有高票提案。Matt 指出从 `fetcher.load` 和 `fetcher.submit` 返回 promise 部分解决了完成问题，但返回数据仍然缺失。另一个主要请求是防止 fetcher 错误触发路由级别的错误边界，Matt 建议使用选择加入机制如 `don't bubble errors` 或 `handle error` 选项。Bryan 认为 fetcher 作为带外网络请求，不应自然冒泡到路由错误边界。

**用于 Fetcher 错误处理的内联 Action 方案**

讨论转向了错误处理的内联 action 方案，与 `client loader` 机制对齐，这是 Jacob 建议的。Matt 和 Bryan 考虑了内联处理器如何允许用户捕获网络错误并决定是将错误作为数据返回还是抛出。Sergio Daniel Xalambrí 质疑这些更改是否应应用于新的类型安全 fetcher（例如 `useRoute action`）而不是改进 `useFetcher`。Matt 和 Bryan 同意在新的类型安全 fetcher API 中实现这项工作，其中 `submit` 将返回数据并在错误时 reject，似乎是最合适的方案。

**用于模态的路由遮罩/重写**

Matt 介绍了一个重新浮出水面的高优先级提案——路由遮罩/重写，类似于 Next.js 的并行/拦截路由或 Tanstack 的路由遮罩。此功能之前在声明式模式中可用，允许在保持 URL 栏中不同 URL 的同时在背景 URL 上渲染模态。Matt 建议一种 API，用户提供要在 URL 栏中显示的 URL，路由器内部导航到不同的 URL，可能由搜索参数驱动。Mark 和 Bryan 同意这似乎与客户端导航耦合，用于如在搜索结果上显示工作详情等 UX 场景。

**服务器重写和客户端路由器同步**

Sergio Daniel Xalambrí 指出人们经常要求服务器重写，在 Next.js 术语中通常意味着 URL 别名，其中一个路径渲染另一个路由的内容，可能带有参数重写。Matt 总结说真正的服务器重写是一个单独的功能，但指出实现客户端遮罩功能将缩小将重写逻辑与客户端路由器同步所需的差距，可能解锁未来的服务器端重写功能。Matt 打算更新提案并将其移至"接受 PR"阶段，指出实现可以借鉴 V6 的内部实现。

**元素滚动恢复**

讨论的最后一个话题是关于在窗口以外的元素上进行滚动恢复的高票提案。Matt 解释说，完全的用户空间实现不可靠，因为路由器是唯一真正知道视图即将更改的时刻以可靠地捕获滚动位置的。Matt 计划根据之前的 PR 讨论提供指导和反馈，希望社区能完成实现。

</details>

<details>
<summary>2025-12-16 会议记录</summary>

**尾斜杠一致性 Bug 修复**

Matt 概述了一个与数据请求中 URL 有尾斜杠时提供给 `loader`/`action` 的请求路径名不一致相关的 bug 修复。Matt 解释说，与 Jacob 一起开发的解决方案涉及更改尾斜杠场景的数据请求 URL 格式，使其类似于 `_root.data` 格式，由于潜在的破坏性变更和缓存规则影响，这被放在 future 标志后面。当 URL 为 `/a/b/c/` 时，新格式将使用 `/a/b/c/_.data`。

Matt 还指出 future 标志提供了将 `_root.data` 格式合并到新的尾斜杠格式中的机会，从而在未来产生两种标准化的数据请求格式。Bryan 就各种 URL 和查询参数配置要求澄清，Matt 解释了新的尾斜杠格式与索引查询参数使用之间的区别，确认新格式将是选择加入的，对于采用它的用户来说基本不会造成破坏。

</details>
