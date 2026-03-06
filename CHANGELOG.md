<!-- markdownlint-disable no-duplicate-header no-emphasis-as-heading no-inline-html -->

# React Router 版本发布记录

本页面列出了 React Router 从 `v6.0.0` 至今的所有版本发布记录。v6 之前的版本请参阅 [Github Releases 页面](https://github.com/remix-run/react-router/releases)。
我们选择在此文件中管理版本发布记录，而非使用分页的 Github Releases 页面，原因有二：

- Github UI 的分页机制意味着你无法方便地一次性搜索大量版本的发布记录
- Github 的分页界面在列表视图中还会截断较长的发布记录而不给出提示，你需要点击进入详情视图才能看到完整的发布记录

<details>
  <summary>目录</summary>

- [React Router Releases](#react-router-releases)
  - [v6.30.3](#v6303)
    - [Patch Changes](#patch-changes)
  - [v6.30.2](#v6302)
    - [Patch Changes](#patch-changes-1)
  - [v6.30.1](#v6301)
    - [Patch Changes](#patch-changes-2)
  - [v6.30.0](#v6300)
    - [Minor Changes](#minor-changes)
    - [Patch Changes](#patch-changes-3)
  - [v6.29.0](#v6290)
    - [Minor Changes](#minor-changes-1)
    - [Patch Changes](#patch-changes-4)
  - [v6.28.2](#v6282)
    - [Patch Changes](#patch-changes-5)
  - [v6.28.1](#v6281)
    - [Patch Changes](#patch-changes-6)
  - [v6.28.0](#v6280)
    - [What's Changed](#whats-changed)
    - [Minor Changes](#minor-changes-2)
    - [Patch Changes](#patch-changes-7)
  - [v6.27.0](#v6270)
    - [What's Changed](#whats-changed-1)
      - [Stabilized APIs](#stabilized-apis)
    - [Minor Changes](#minor-changes-3)
    - [Patch Changes](#patch-changes-8)
  - [v6.26.2](#v6262)
    - [Patch Changes](#patch-changes-9)
  - [v6.26.1](#v6261)
    - [Patch Changes](#patch-changes-10)
  - [v6.26.0](#v6260)
    - [Minor Changes](#minor-changes-4)
    - [Patch Changes](#patch-changes-11)
  - [v6.25.1](#v6251)
    - [Patch Changes](#patch-changes-12)
  - [v6.25.0](#v6250)
    - [What's Changed](#whats-changed-2)
      - [Stabilized `v7_skipActionErrorRevalidation`](#stabilized-v7_skipactionerrorrevalidation)
    - [Minor Changes](#minor-changes-5)
    - [Patch Changes](#patch-changes-13)
  - [v6.24.1](#v6241)
    - [Patch Changes](#patch-changes-14)
  - [v6.24.0](#v6240)
    - [What's Changed](#whats-changed-3)
      - [Lazy Route Discovery (a.k.a. "Fog of War")](#lazy-route-discovery-aka-fog-of-war)
    - [Minor Changes](#minor-changes-6)
    - [Patch Changes](#patch-changes-15)
  - [v6.23.1](#v6231)
    - [Patch Changes](#patch-changes-16)
  - [v6.23.0](#v6230)
    - [What's Changed](#whats-changed-4)
      - [Data Strategy (unstable)](#data-strategy-unstable)
      - [Skip Action Error Revalidation (unstable)](#skip-action-error-revalidation-unstable)
    - [Minor Changes](#minor-changes-7)
  - [v6.22.3](#v6223)
    - [Patch Changes](#patch-changes-17)
  - [v6.22.2](#v6222)
    - [Patch Changes](#patch-changes-18)
  - [v6.22.1](#v6221)
    - [Patch Changes](#patch-changes-19)
  - [v6.22.0](#v6220)
    - [What's Changed](#whats-changed-5)
      - [Core Web Vitals Technology Report Flag](#core-web-vitals-technology-report-flag)
    - [Minor Changes](#minor-changes-8)
    - [Patch Changes](#patch-changes-20)
  - [v6.21.3](#v6213)
    - [Patch Changes](#patch-changes-21)
  - [v6.21.2](#v6212)
    - [Patch Changes](#patch-changes-22)
  - [v6.21.1](#v6211)
    - [Patch Changes](#patch-changes-23)
  - [v6.21.0](#v6210)
    - [What's Changed](#whats-changed-6)
      - [`future.v7_relativeSplatPath`](#futurev7_relativesplatpath)
      - [Partial Hydration](#partial-hydration)
    - [Minor Changes](#minor-changes-9)
    - [Patch Changes](#patch-changes-24)
  - [v6.20.1](#v6201)
    - [Patch Changes](#patch-changes-25)
  - [v6.20.0](#v6200)
    - [Minor Changes](#minor-changes-10)
    - [Patch Changes](#patch-changes-26)
  - [v6.19.0](#v6190)
    - [What's Changed](#whats-changed-7)
      - [`unstable_flushSync` API](#unstable_flushsync-api)
    - [Minor Changes](#minor-changes-11)
    - [Patch Changes](#patch-changes-27)
  - [v6.18.0](#v6180)
    - [What's Changed](#whats-changed-8)
      - [New Fetcher APIs](#new-fetcher-apis)
      - [Persistence Future Flag (`future.v7_fetcherPersist`)](#persistence-future-flag-futurev7_fetcherpersist)
    - [Minor Changes](#minor-changes-12)
    - [Patch Changes](#patch-changes-28)
  - [v6.17.0](#v6170)
    - [What's Changed](#whats-changed-9)
      - [View Transitions 🚀](#view-transitions-)
    - [Minor Changes](#minor-changes-13)
    - [Patch Changes](#patch-changes-29)
  - [v6.16.0](#v6160)
    - [Minor Changes](#minor-changes-14)
    - [Patch Changes](#patch-changes-30)
  - [v6.15.0](#v6150)
    - [Minor Changes](#minor-changes-15)
    - [Patch Changes](#patch-changes-31)
  - [v6.14.2](#v6142)
    - [Patch Changes](#patch-changes-32)
  - [v6.14.1](#v6141)
    - [Patch Changes](#patch-changes-33)
  - [v6.14.0](#v6140)
    - [What's Changed](#whats-changed-10)
      - [JSON/Text Submissions](#jsontext-submissions)
    - [Minor Changes](#minor-changes-16)
    - [Patch Changes](#patch-changes-34)
  - [v6.13.0](#v6130)
    - [What's Changed](#whats-changed-11)
      - [`future.v7_startTransition`](#futurev7_starttransition)
    - [Minor Changes](#minor-changes-17)
    - [Patch Changes](#patch-changes-35)
  - [v6.12.1](#v6121)
    - [Patch Changes](#patch-changes-36)
  - [v6.12.0](#v6120)
    - [What's Changed](#whats-changed-12)
      - [`React.startTransition` support](#reactstarttransition-support)
    - [Minor Changes](#minor-changes-18)
    - [Patch Changes](#patch-changes-37)
  - [v6.11.2](#v6112)
    - [Patch Changes](#patch-changes-38)
  - [v6.11.1](#v6111)
    - [Patch Changes](#patch-changes-39)
  - [v6.11.0](#v6110)
    - [Minor Changes](#minor-changes-19)
    - [Patch Changes](#patch-changes-40)
  - [v6.10.0](#v6100)
    - [What's Changed](#whats-changed-13)
    - [Minor Changes](#minor-changes-20)
      - [`future.v7_normalizeFormMethod`](#futurev7_normalizeformmethod)
    - [Patch Changes](#patch-changes-41)
  - [v6.9.0](#v690)
    - [What's Changed](#whats-changed-14)
      - [`Component`/`ErrorBoundary` route properties](#componenterrorboundary-route-properties)
      - [Introducing Lazy Route Modules](#introducing-lazy-route-modules)
    - [Minor Changes](#minor-changes-21)
    - [Patch Changes](#patch-changes-42)
  - [v6.8.2](#v682)
    - [Patch Changes](#patch-changes-43)
  - [v6.8.1](#v681)
    - [Patch Changes](#patch-changes-44)
  - [v6.8.0](#v680)
    - [Minor Changes](#minor-changes-22)
    - [Patch Changes](#patch-changes-45)
  - [v6.7.0](#v670)
    - [Minor Changes](#minor-changes-23)
    - [Patch Changes](#patch-changes-46)
  - [v6.6.2](#v662)
    - [Patch Changes](#patch-changes-47)
  - [v6.6.1](#v661)
    - [Patch Changes](#patch-changes-48)
  - [v6.6.0](#v660)
    - [What's Changed](#whats-changed-15)
    - [Minor Changes](#minor-changes-24)
    - [Patch Changes](#patch-changes-49)
  - [v6.5.0](#v650)
    - [What's Changed](#whats-changed-16)
    - [Minor Changes](#minor-changes-25)
    - [Patch Changes](#patch-changes-50)
  - [v6.4.5](#v645)
    - [Patch Changes](#patch-changes-51)
  - [v6.4.4](#v644)
    - [Patch Changes](#patch-changes-52)
  - [v6.4.3](#v643)
    - [Patch Changes](#patch-changes-53)
  - [v6.4.2](#v642)
    - [Patch Changes](#patch-changes-54)
  - [v6.4.1](#v641)
    - [Patch Changes](#patch-changes-55)
  - [v6.4.0](#v640)
    - [What's Changed](#whats-changed-17)
      - [Remix Data APIs](#remix-data-apis)
    - [Patch Changes](#patch-changes-56)
  - [v6.3.0](#v630)
    - [Minor Changes](#minor-changes-26)
  - [v6.2.2](#v622)
    - [Patch Changes](#patch-changes-57)
  - [v6.2.1](#v621)
    - [Patch Changes](#patch-changes-58)
  - [v6.2.0](#v620)
    - [Minor Changes](#minor-changes-27)
    - [Patch Changes](#patch-changes-59)
  - [v6.1.1](#v611)
    - [Patch Changes](#patch-changes-60)
  - [v6.1.0](#v610)
    - [Minor Changes](#minor-changes-28)
    - [Patch Changes](#patch-changes-61)
  - [v6.0.2](#v602)
    - [Patch Changes](#patch-changes-62)
  - [v6.0.1](#v601)
    - [Patch Changes](#patch-changes-63)
  - [v6.0.0](#v600)

</details>

<!-- 添加新版本时，请复制以下模板：
## v6.X.Y

Date: YYYY-MM-DD

### 重要变更

#### 重大新特性 1

#### 重大新特性 2

### 次要变更

### 补丁变更

**完整更新日志**: [`v6.X.Y...v6.X.Y`](https://github.com/remix-run/react-router/compare/react-router@6.X.Y...react-router@6.X.Y)
-->

## v6.30.3

Date: 2026-01-07

### 补丁变更

- 验证重定向位置 ([#14707](https://github.com/remix-run/react-router/pull/14707))

**完整更新日志**: [`v6.30.2...v6.30.3`](https://github.com/remix-run/react-router/compare/react-router@6.30.2...react-router@6.30.3)

## v6.30.2

Date: 2025-11-13

### 补丁变更

- 规范化 `resolvePath` 中的双斜杠 ([#14537](https://github.com/remix-run/react-router/pull/14537))

**完整更新日志**: [`v6.30.1...v6.30.2`](https://github.com/remix-run/react-router/compare/react-router@6.30.1...react-router@6.30.2)

## v6.30.1

Date: 2025-05-20

### 补丁变更

- 部分恢复 `6.29.0` 中添加的减少 `matchRoutes` 调用的优化，因为它引发了其他问题 ([#13623](https://github.com/remix-run/react-router/pull/13623))
- 当 `v7_relativeSplatPath` 设置为 `false` 时不再记录无效警告 ([#13502](https://github.com/remix-run/react-router/pull/13502))

**完整更新日志**: [`v6.30.0...v6.30.1`](https://github.com/remix-run/react-router/compare/react-router@6.30.0...react-router@6.30.1)

## v6.30.0

Date: 2025-02-27

### 次要变更

- 新增 `fetcherKey` 作为 `patchRoutesOnNavigation` 的参数 ([#13109](https://github.com/remix-run/react-router/pull/13109))

### 补丁变更

- 修复 `6.29.0` 中通过 [#12169](https://github.com/remix-run/react-router/pull/12169) 引入的回归问题，该问题导致使用懒加载路由发现（`patchRoutesOnNavigation`）的应用在 splat 路由内导航到 hash 路由时出现问题 ([#13108](https://github.com/remix-run/react-router/pull/13108))

**完整更新日志**: [`v6.29.0...v6.30.0`](https://github.com/remix-run/react-router/compare/react-router@6.29.0...react-router@6.30.0)

## v6.29.0

Date: 2025-01-30

### 次要变更

- 将请求的 `signal` 作为参数传递给 `patchRoutesOnNavigation` ([#12900](https://github.com/remix-run/react-router/pull/12900))
  - 可用于在飞行中的导航/fetcher 被中止时取消任何 manifest 请求

### 补丁变更

- 在生产环境构建中不再记录 v7 弃用警告 ([#12794](https://github.com/remix-run/react-router/pull/12794))
- 正确地在抛出 `data()` 结果时向上冒泡 headers ([#12845](https://github.com/remix-run/react-router/pull/12845))
- 通过在可能时跳过冗余的 `matchRoutes` 调用来优化路由匹配 ([#12169](https://github.com/remix-run/react-router/pull/12169))
- 为 fetcher 调用时从 `patchRoutesOnNavigation` 的 `path` 参数中移除搜索参数 ([#12899](https://github.com/remix-run/react-router/pull/12899))

**完整更新日志**: [`v6.28.2...v6.29.0`](https://github.com/remix-run/react-router/compare/react-router@6.28.2...react-router@6.29.0)

## v6.28.2

Date: 2025-01-16

### 补丁变更

- 修复未启用 `future.v7_fetcherPersist` 时手动设置 fetcher `key` 的用法问题 ([#12674](https://github.com/remix-run/react-router/pull/12674))
- 修复 fetcher 卸载时数据层中 fetcher 数据清理的问题 ([#12674](https://github.com/remix-run/react-router/pull/12674))

**完整更新日志**: [`v6.28.1...v6.28.2`](https://github.com/remix-run/react-router/compare/react-router@6.28.1...react-router@6.28.2)

## v6.28.1

Date: 2024-12-20

### 补丁变更

- 允许用户通过将标志设置为 `false` 来关闭 v7 弃用警告 ([#12441](https://github.com/remix-run/react-router/pull/12441))

**完整更新日志**: [`v6.28.0...v6.28.1`](https://github.com/remix-run/react-router/compare/react-router@6.28.0...react-router@6.28.1)

## v6.28.0

Date: 2024-11-06

### 重要变更

- 为了准备迁移到 v7，我们为你尚未启用的所有 future 标志添加了弃用警告。请使用这些标志来更好地为最终升级到 v7 做好准备。

### 次要变更

- 为 v7 标志记录弃用警告 ([#11750](https://github.com/remix-run/react-router/pull/11750))
  - 为 `json`/`defer` 添加弃用警告，建议改为直接返回原始对象
    - 这些方法将在 React Router v7 中移除

### 补丁变更

- 更新 JSDoc URL 以适配新的网站结构（添加 /v6/ 路径段） ([#12141](https://github.com/remix-run/react-router/pull/12141))

**完整更新日志**: [`v6.27.0...v6.28.0`](https://github.com/remix-run/react-router/compare/react-router@6.27.0...react-router@6.28.0)

## v6.27.0

Date: 2024-10-11

### 重要变更

#### 稳定化的 API

本次发布稳定化了一批 "unstable" API，以为即将发布的 React Router v7 做准备（详情参见[这些](https://remix.run/blog/merging-remix-and-react-router) [文章](https://remix.run/blog/incremental-path-to-react-19)）：
([pending](https://x.com/remix_run/status/1841926034868077009))

- `unstable_dataStrategy` →`dataStrategy` (`createBrowserRouter` and friends) ([Docs](https://reactrouter.com/v6/routers/create-browser-router#optsdatastrategy))
- `unstable_patchRoutesOnNavigation` →`patchRoutesOnNavigation` (`createBrowserRouter` and friends) ([Docs](https://reactrouter.com/v6/routers/create-browser-router#optspatchroutesonnavigation))
- `unstable_flushSync` →`flushSync` (`useSubmit`, `fetcher.load`, `fetcher.submit`) ([Docs](https://reactrouter.com/v6/hooks/use-submit#optionsflushsync))
- `unstable_viewTransition` →`viewTransition` (`<Link>`, `<Form>`, `useNavigate`, `useSubmit`) ([Docs](https://reactrouter.com/v6/components/link#viewtransition))

### 次要变更

- 稳定化导航和 fetcher 的 `unstable_flushSync` 选项 ([#11989](https://github.com/remix-run/react-router/pull/11989))
- 稳定化导航的 `unstable_viewTransition` 选项及对应的 `unstable_useViewTransitionState` Hook ([#11989](https://github.com/remix-run/react-router/pull/11989))
- 稳定化 `unstable_dataStrategy` ([#11974](https://github.com/remix-run/react-router/pull/11974))
- 稳定化 `unstable_patchRoutesOnNavigation` ([#11973](https://github.com/remix-run/react-router/pull/11973))
  - 新增 `PatchRoutesOnNavigationFunctionArgs` 类型以方便使用 ([#11967](https://github.com/remix-run/react-router/pull/11967))

### 补丁变更

- 修复向当前上下文路由（带索引子路由的父路由）提交时，如果已存在来自先前提交的 `?index` 参数的错误 ([#12003](https://github.com/remix-run/react-router/pull/12003))
- 修复 `useFormAction` 错误——删除 `?index` 参数时不会保留其他非 Remix 的 `index` 参数 ([#12003](https://github.com/remix-run/react-router/pull/12003))
- 修复并发 fetch 期间 fetcher 不保留 `preventScrollReset` 的重定向问题 ([#11999](https://github.com/remix-run/react-router/pull/11999))
- 避免连续重新验证调用导致的 fetcher 中止不必要的 `console.error` ([#12050](https://github.com/remix-run/react-router/pull/12050))
- 修复使用 `partialHydration` 时带错误的水合问题 ([#12070](https://github.com/remix-run/react-router/pull/12070))
- 移除内部缓存以修复被中断的 `patchRoutesOnNavigation` 调用问题 ([#12055](https://github.com/remix-run/react-router/pull/12055))
  - ⚠️ 如果你之前依赖 `unstable_` API 的这个行为，这可能是一个破坏性变更
  - 我们之前会在内部缓存正在进行中的 `patchRoutesOnNavigation` 调用，这样多次具有相同起点/终点的导航只会执行一次函数并使用相同的 promise
  - 然而，这种方法与导航被中断时 `patch` 短路的情况相冲突（因为第一次调用的 `patch` 会被中止而无操作）
  - 此缓存还对有效的缓存键做了一些假设——并且完全不了解可能已发生的其他应用程序状态变化
  - 因此，缓存已被移除，因为在 _大多数_ 情况下，像 `import()` 这样的异步路由重复调用会自动被缓存——如果没有，用户也可以很容易地在用户层实现这个缓存
- 移除 `unstable_patchRoutesOnNavigation` 中的内部 `discoveredRoutes` FIFO 队列 ([#11977](https://github.com/remix-run/react-router/pull/11977))
  - ⚠️ 如果你之前依赖 `unstable_` API 的这个行为，这可能是一个破坏性变更
  - 这原本是作为优化实现的，但被证明限制太多
  - 如果你需要这个优化，可以在 `patchRoutesOnNavigation` 内部实现自己的缓存
- 修复 `PatchRoutesOnNavigationFunction` 的 `patch` 方法中 `RouteObject` 的类型，使其不再要求传递与路由无关的路由对象 ([#11967](https://github.com/remix-run/react-router/pull/11967))
- 将 `patchRoutesOnNavigation` 抛出的错误直接暴露给 `useRouteError`，而不是将其包装在 400 `ErrorResponse` 实例中 ([#12111](https://github.com/remix-run/react-router/pull/12111))

**完整更新日志**: [`v6.26.2...v6.27.0`](https://github.com/remix-run/react-router/compare/react-router@6.26.2...react-router@6.27.0)

## v6.26.2

Date: 2024-09-09

### 补丁变更

- 更新 `unstable_dataStrategy` API 以支持更高级的实现 ([#11943](https://github.com/remix-run/react-router/pull/11943))
  - ⚠️ 如果你已经采用了 `unstable_dataStrategy`，请仔细审查，因为本次包含对此 API 的破坏性变更
  - 将 `unstable_HandlerResult` 重命名为 `unstable_DataStrategyResult`
  - 将 `unstable_dataStrategy` 的返回类型从并行数组 `unstable_DataStrategyResult[]`（与 `matches` 并行）改为键值对象 `routeId => unstable_DataStrategyResult`
    - 这允许对重新验证行为进行更高级的控制，因为你可以通过 `match.shouldLoad` 选择启用或禁用可能默认不会被重新验证的数据的重新验证
  - 你现在应该从 `handlerOverride` 中返回/抛出结果，而不是返回 `DataStrategyResult`
    - `handlerOverride` 的返回值（或抛出的错误）将被包装为 `DataStrategyResult` 并从 `match.resolve` 返回
    - 因此，如果你将 `match.resolve()` 的结果聚合到最终结果对象中，则不需要考虑 `DataStrategyResult` 类型
    - 如果你在 `handlerOverride` 内部手动填充结果对象，则需要将 `DataStrategyResult` 作为值赋值，以便 React Router 知道它是成功执行还是错误（详见文档中的示例）
  - 新增 `fetcherKey` 参数到 `unstable_dataStrategy`，以区分导航和 fetcher 调用
- 保留已启用的视图过渡通过重定向 ([#11925](https://github.com/remix-run/react-router/pull/11925))
- 在路由器重新验证调用中保留待处理的视图过渡 ([#11917](https://github.com/remix-run/react-router/pull/11917))
- 修复快速/同步调用 `blocker.proceed` 时的 blocker 用法问题 ([#11930](https://github.com/remix-run/react-router/pull/11930))

**完整更新日志**: [`v6.26.1...v6.26.2`](https://github.com/remix-run/react-router/compare/react-router@6.26.1...react-router@6.26.2)

## v6.26.1

Date: 2024-08-15

### 补丁变更

- 将 `unstable_patchRoutesOnMiss` 重命名为 `unstable_patchRoutesOnNavigation` 以匹配新的行为 ([#11888](https://github.com/remix-run/react-router/pull/11888))
- 更新 `unstable_patchRoutesOnNavigation` 逻辑，使其在匹配到动态参数或 splat 段的路由时调用该方法，以防止存在尚未发现的更高分数的静态路由 ([#11883](https://github.com/remix-run/react-router/pull/11883))
  - 我们现在还利用内部 FIFO 队列记录已经调用过 `unstable_patchRoutesOnNavigation` 的路径，以避免后续导航到相同路径时重复调用

**完整更新日志**: [`v6.26.0...v6.26.1`](https://github.com/remix-run/react-router/compare/react-router@6.26.0...react-router@6.26.1)

## v6.26.0

Date: 2024-08-01

### 次要变更

- 新增 `replace(url, init?)` 函数作为 `redirect(url, init?)` 的替代方案，它在客户端导航重定向时执行 `history.replaceState` 而非 `history.pushState` ([#11811](https://github.com/remix-run/react-router/pull/11811))
- 新增 `unstable_data()` API 用于 Remix Single Fetch ([#11836](https://github.com/remix-run/react-router/pull/11836))
  - 此 API 不适用于直接在 React Router SPA 应用中使用
  - 它主要用于 `createStaticHandler.query()` 场景，允许 loader/action 返回任意数据并附带自定义 `status`/`headers`，而无需将数据强制序列化为 `Response` 实例
  - 这允许通过 `unstable_dataStrategy` 实现更高级的序列化策略，例如在 Remix Single Fetch 中通过 `turbo-stream` 序列化
  - ⚠️ 此变更移除了 `HandlerResult` 中的 `status` 字段
    - 如果你需要从 `unstable_dataStrategy` 返回特定 `status`，应通过 `unstable_data()` 来实现

### 补丁变更

- 修复被中断的 fetcher 的内部清理问题，避免导航时出现无效的重新验证 ([#11839](https://github.com/remix-run/react-router/pull/11839))
- 修复同时使用 `future.v7_partialHydration` 和 `unstable_patchRoutesOnMiss` 时的初始水合行为 ([#11838](https://github.com/remix-run/react-router/pull/11838))
  - 在初始水合期间，`router.state.matches` 现在会包含任何部分匹配，以便我们可以渲染祖先级 `HydrateFallback` 组件

**完整更新日志**: [`v6.25.1...v6.26.0`](https://github.com/remix-run/react-router/compare/react-router@6.25.1...react-router@6.26.0)

## v6.25.1

Date: 2024-07-17

### 补丁变更

- 对 `RouterProvider` 内部进行记忆化处理以减少不必要的重新渲染 ([#11803](https://github.com/remix-run/react-router/pull/11803))

**完整更新日志**: [`v6.25.0...v6.25.1`](https://github.com/remix-run/react-router/compare/react-router@6.25.0...react-router@6.25.1)

## v6.25.0

Date: 2024-07-16

### 重要变更

#### 稳定化 `v7_skipActionErrorRevalidation`

本次发布将 `future.unstable_skipActionErrorRevalidation` 标志稳定化为 [`future.v7_skipActionErrorRevalidation`](https://reactrouter.com/v6/upgrading/future#v7_skipactionstatusrevalidation)，以为即将发布的 React Router v7 做准备。

- 启用此标志后，返回/抛出 `4xx/5xx` `Response` 的 action 将不会默认触发重新验证
- 此变更还将 `shouldRevalidate` 的 `unstable_actionStatus` 参数稳定化为 `actionStatus`

### 次要变更

- 将 `future.unstable_skipActionErrorRevalidation` 稳定化为 `future.v7_skipActionErrorRevalidation` ([#11769](https://github.com/remix-run/react-router/pull/11769))

### 补丁变更

- 修复回归问题，正确解码 `useMatch` 内部的路径，使 matches/params 反映解码后的参数 ([#11789](https://github.com/remix-run/react-router/pull/11789))
- 修复 `unstable_patchRoutesOnMiss` 抛出的错误冒泡问题 ([#11786](https://github.com/remix-run/react-router/pull/11786))
- 修复使用 `unstable_patchRoutesOnMiss` 的 SSR 应用在服务器端匹配 splat 路由时的水合问题 ([#11790](https://github.com/remix-run/react-router/pull/11790))

**完整更新日志**: [`v6.24.1...v6.25.0`](https://github.com/remix-run/react-router/compare/react-router@6.24.1...react-router@6.25.0)

## v6.24.1

Date: 2024-07-03

### 补丁变更

- 从警告消息中移除 `polyfill.io` 引用，因为该域名已被出售并被确认用于提供恶意软件 ([#11741](https://github.com/remix-run/react-router/pull/11741))
  - 参见 https://sansec.io/research/polyfill-supply-chain-attack
- 导出 `NavLinkRenderProps` 类型以便更容易地为自定义 `NavLink` 回调定义类型 ([#11553](https://github.com/remix-run/react-router/pull/11553))
- 使用 `future.v7_relativeSplatPath` 时，正确解析作为无路径路由子路由的 splat 路由中的相对路径 ([#11633](https://github.com/remix-run/react-router/pull/11633))
- 迷雾探索（不稳定）：在路由补丁期间触发新的 `router.routes` 标识/重新流 ([#11740](https://github.com/remix-run/react-router/pull/11740))
- 迷雾探索（不稳定）：修复 splat 路由匹配时的初始匹配问题 ([#11759](https://github.com/remix-run/react-router/pull/11759))

**完整更新日志**: [`v6.24.0...v6.24.1`](https://github.com/remix-run/react-router/compare/react-router@6.24.0...react-router@6.24.1)

## v6.24.0

Date: 2024-06-24

### 重要变更

#### 懒加载路由发现（也称“迷雾探索”）

我们非常兴奋地在 `v6.24.0` 中发布了新的“懒加载路由发现” API！有关背景信息，请查看原始 [RFC](https://github.com/remix-run/react-router/discussions/11113)。简而言之，自从我们在 v6.4 中通过 `<RouterProvider>` 引入数据 API 以来，我们一直对缺少一个像 `<BrowserRouter>`/`<Routes>` 应用中那样的代码分割方案感到遗憾。我们在 `v6.9.0` 中通过 `route.lazy` 迈出了第一步，而在 `v6.24.0` 中我们完成了剩余的工作。

通过“迷雾探索”功能，你现在可以通过传递给 `createBrowserRouter`（及其 memory/hash 对应版本）的新 `unstable_patchRoutesOnMiss` 选项来懒加载路由树的部分内容。这提供了一种方式来处理 React Router 无法匹配给定路径的情况，并在导航（或 fetcher 调用）期间将新路由补丁到路由树中。

以下是一个非常简单的示例，更多信息和用例请参阅[文档](https://reactrouter.com/v6/routers/create-browser-router#optsunstable_patchroutesonmiss)：

```js
const router = createBrowserRouter(
  [
    {
      id: "root",
      path: "/",
      Component: RootComponent,
    },
  ],
  {
    async unstable_patchRoutesOnMiss({ path, patch }) {
      if (path === "/a") {
        // Load the `a` route (`{ path: 'a', Component: A }`)
        let route = await getARoute();
        // Patch the `a` route in as a new child of the `root` route
        patch("root", [route]);
      }
    },
  },
);
```

### 次要变更

- 新增懒加载路由发现（也称“迷雾探索”）支持 ([#11626](https://github.com/remix-run/react-router/pull/11626))

### 补丁变更

- 修复 `fetcher.submit` 类型——移除不正确的 `navigate`/`fetcherKey`/`unstable_viewTransition` 选项，因为它们仅与 `useSubmit` 相关 ([#11631](https://github.com/remix-run/react-router/pull/11631))
- 允许传递给 `<StaticRouter>` 的 `location.state` 为假值 ([#11495](https://github.com/remix-run/react-router/pull/11495))

**完整更新日志**: [`v6.23.1...v6.24.0`](https://github.com/remix-run/react-router/compare/react-router@6.23.1...react-router@6.24.0)

## v6.23.1

Date: 2024-05-10

### 补丁变更

- 允许通过 `<Await>` 解析 `undefined` ([#11513](https://github.com/remix-run/react-router/pull/11513))
- 添加防御性 `document` 检查，以确认 `document.startViewTransition` 是否可用 ([#11544](https://github.com/remix-run/react-router/pull/11544))
- 将 `react-router-dom/server` 的导入改回 `react-router-dom` 而非 `index.ts` ([#11514](https://github.com/remix-run/react-router/pull/11514))
- `@remix-run/router` - 支持在 `staticHandler.queryRoute` 上使用 `unstable_dataStrategy` ([#11515](https://github.com/remix-run/react-router/pull/11515))

**完整更新日志**: [`v6.23.0...v6.23.1`](https://github.com/remix-run/react-router/compare/react-router@6.23.0...react-router@6.23.1)

## v6.23.0

Date: 2024-04-23

### 重要变更

#### 数据策略（不稳定）

新的 `unstable_dataStrategy` API 是一个低级 API，专为高级用例设计，允许你控制 `loader`/`action` 函数的数据策略。默认实现是当前的行为（并行获取所有 loader），但此选项允许用户实现更高级的数据流，包括 Remix ["Single Fetch"](https://remix.run/docs/guides/single-fetch)、用户端中间件/上下文 API、自动 loader 缓存等。详情请参见[文档](https://reactrouter.com/v6/routers/create-browser-router#unstable_datastrategy)。

**注意：** 这是一个为高级用例设计的低级 API。它会覆盖 React Router 对 `loader`/`action` 执行的内部处理，如果使用不当会破坏你的应用代码。请谨慎使用并进行充分测试。

#### 跳过 Action 错误重新验证（不稳定）

目前，所有活跃的 `loader` 在任何 `action` 提交后都会重新验证，无论 `action` 结果如何。然而，在大多数情况下，`action` 返回 `4xx`/`5xx` 响应状态意味着实际上没有数据被更改，重新验证是不必要的。我们引入了一个新的 `future.unstable_skipActionErrorRevalidation` 标志来改变这里的行为，并计划在 React Router 未来版本中将其作为默认行为。

启用此标志后，返回/抛出 `4xx`/`5xx` 响应状态的 `action` 将不再自动重新验证。如果你需要在启用此标志后对 `4xx`/`5xx` 结果进行重新验证，仍然可以通过 `shouldRevalidate` 返回 `true` 来实现——现在还会接收一个新的 `unstable_actionStatus` 参数（与 `actionResult` 并列），这样你可以根据 `action` 响应的状态做出决定，而无需将其编码到 action 数据中。

### 次要变更

- 新增 `unstable_dataStrategy` 配置选项 ([#11098](https://github.com/remix-run/react-router/pull/11098), [#11377](https://github.com/remix-run/react-router/pull/11377))
- `@remix-run/router` - 新增 `future.unstable_skipActionRevalidation` future 标志 ([#11098](https://github.com/remix-run/react-router/pull/11098))
- `@remix-run/router` - SSR：为 `staticHandler.query` 方法新增 `skipLoaderErrorBubbling` 选项，用于在 Remix Single Fetch 实现中禁用静态处理程序的错误冒泡 ([#11098](https://github.com/remix-run/react-router/pull/11098), ([#11377](https://github.com/remix-run/react-router/pull/11377)))

**完整更新日志**: [`v6.22.3...v6.23.0`](https://github.com/remix-run/react-router/compare/react-router@6.22.3...react-router@6.23.0)

## v6.22.3

Date: 2024-03-07

### 补丁变更

- 修复 `future.v7_partialHydration` 的错误：当 SSR loader 错误冒泡到父级边界时，会重新运行边界下方的 loader ([#11324](https://github.com/remix-run/react-router/pull/11324))
- 修复 `future.v7_partialHydration` 的错误：如果路由没有 loader，会认为路由器未初始化 ([#11325](https://github.com/remix-run/react-router/pull/11325))

**完整更新日志**: [`v6.22.2...v6.22.3`](https://github.com/remix-run/react-router/compare/react-router@6.22.2...react-router@6.22.3)

## v6.22.2

Date: 2024-02-28

### 补丁变更

- 在部分水合运行期间保留已水合的错误 ([#11305](https://github.com/remix-run/react-router/pull/11305))

**完整更新日志**: [`v6.22.1...v6.22.2`](https://github.com/remix-run/react-router/compare/react-router@6.22.1...react-router@6.22.2)

## v6.22.1

Date: 2024-02-16

### 补丁变更

- 修复预编码动态参数值的编码/解码问题 ([#11199](https://github.com/remix-run/react-router/pull/11199))

**完整更新日志**: [`v6.22.0...v6.22.1`](https://github.com/remix-run/react-router/compare/react-router@6.22.0...react-router@6.22.1)

## v6.22.0

Date: 2024-02-01

### 重要变更

#### Core Web Vitals 技术报告标志

2021 年，HTTP Archive 推出了 [Core Web Vitals 技术报告仪表盘](https://discuss.httparchive.org/t/new-dashboard-the-core-web-vitals-technology-report/2178)：

> 通过将 Chrome UX Report 26 (CrUX) 数据集中的真实用户体验与 HTTP Archive 30 中的 Web 技术检测结合，我们可以了解 CMS 平台或 JavaScript 框架等架构决策如何影响网站的 CWV 性能。

他们使用一个名为 [`wappalyzer`](https://github.com/HTTPArchive/wappalyzer) 的工具来识别给定网站使用的技术，方法是查找特定的脚本、全局 JS 变量或其他识别特征。例如，对于 Remix 应用，他们[查找全局变量 `__remixContext`](https://github.com/HTTPArchive/wappalyzer/blob/c2a24ee7c2d07bf9c521f02584ae2dcf603ac0b7/src/technologies/r.json#L1328) 来识别网站是否使用 Remix。

我们注意到 React Router 无法被可靠地识别，因为没有可识别的全局特征。他们目前[查找名称中包含 `react-router` 的外部脚本](https://github.com/HTTPArchive/wappalyzer/blob/c2a24ee7c2d07bf9c521f02584ae2dcf603ac0b7/src/technologies/r.json#L637)。这能识别从 CDN（如 `unpkg`）使用 React Router 的网站——但会遗漏 **绝大多数** 从 npm 注册表安装并打包到 JS 文件中的网站。这导致 React Router 在 Web 上的使用量被[严重低估](https://lookerstudio.google.com/s/pixHkNmGbN4)。

从 `6.22.0` 版本开始，使用 `react-router-dom` 的网站将开始添加 `window.__reactRouterVersion` 变量，其值为 SemVer 主版本号的字符串（即 `window.__reactRouterVersion = "6";`），以便能够被正确识别。

### 次要变更

- 包含 `window.__reactRouterVersion` 以便 CWV 报告检测 ([#11222](https://github.com/remix-run/react-router/pull/11222))
- 为 `createStaticHandler` 新增 `future.v7_throwAbortReason` 标志，使其在请求被中止时抛出 `request.signal.reason`（默认为 `DOMException`），而不是抛出 `Error`（如 `new Error("query() call aborted: GET /path")`） ([#11104](https://github.com/remix-run/react-router/pull/11104))
  - 请注意 `DOMException` 是在 Node v17 中添加的，因此在 Node 16 及以下版本中你不会得到 `DOMException`。

### 补丁变更

- 如果 `ErrorResponse` 状态码被传递给 `getStaticContextFormError`，则尊重该状态码 ([#11213](https://github.com/remix-run/react-router/pull/11213))

**完整更新日志**: [`v6.21.3...v6.22.0`](https://github.com/remix-run/react-router/compare/react-router@6.21.3...react-router@6.22.0)

## v6.21.3

Date: 2024-01-18

### 补丁变更

- 修复使用 `basename` 时 `NavLink` 的 `isPending` 问题 ([#11195](https://github.com/remix-run/react-router/pull/11195))
- 移除 `Blocker`/`BlockerFunction` 类型中残留的 `unstable_` 前缀 ([#11187](https://github.com/remix-run/react-router/pull/11187))

**完整更新日志**: [`v6.21.2...v6.21.3`](https://github.com/remix-run/react-router/compare/react-router@6.21.2...react-router@6.21.3)

## v6.21.2

Date: 2024-01-11

### 补丁变更

- 在可用时利用 `useId` 生成内部 fetcher key ([#11166](https://github.com/remix-run/react-router/pull/11166))
- 修复动态参数名中无法识别破折号的错误 ([#11160](https://github.com/remix-run/react-router/pull/11160))
- 不尝试反序列化空的 JSON 响应 ([#11164](https://github.com/remix-run/react-router/pull/11164))

**完整更新日志**: [`v6.21.1...v6.21.2`](https://github.com/remix-run/react-router/compare/react-router@6.21.1...react-router@6.21.2)

## v6.21.1

Date: 2023-12-21

### 补丁变更

- 修复指定 `v7_partialHydration` 时 `route.lazy` 在初始 SPA 加载时无法正确工作的错误 ([#11121](https://github.com/remix-run/react-router/pull/11121))
- 修复在 `submitting` 阶段卸载的持久化 fetcher 无法进行重新验证的错误 ([#11102](https://github.com/remix-run/react-router/pull/11102))
- 去重 `resolveTo` 中的相对路径逻辑 ([#11097](https://github.com/remix-run/react-router/pull/11097))

**完整更新日志**: [`v6.21.0...v6.21.1`](https://github.com/remix-run/react-router/compare/react-router@6.21.0...react-router@6.21.1)

## v6.21.0

Date: 2023-12-13

### 重要变更

#### `future.v7_relativeSplatPath`

我们在 `6.19.0` 中修复了一个 splat 路由路径解析错误，但后来发现大量应用依赖了这个错误行为，因此我们在 `6.20.1` 中恢复了该修复（参见 [#10983](https://github.com/remix-run/react-router/issues/10983)、[#11052](https://github.com/remix-run/react-router/issues/11052)、[#11078](https://github.com/remix-run/react-router/issues/11078)）。

该错误行为是：在 splat 路由内部解析相对路径时，默认行为会 _忽略_ 当前路由路径的任何 splat（`*`）部分。启用 future 标志后，splat 部分将被包含在 splat 路由内的相对路径逻辑中。

更多信息请参阅 [`useResolvedPath` 文档](https://reactrouter.com/v6/hooks/use-resolved-path#splat-paths) 和/或[详细的更新日志条目](https://github.com/remix-run/react-router/blob/main/packages/react-router-dom/CHANGELOG.md#6210)。

#### 部分水合

我们为 `@remix-run/router` 新增了 `future.v7_partialHydration` future 标志，在服务端渲染时启用数据路由器的部分水合。这允许你提供的 `hydrationData.loaderData` 仅包含 _部分_ 初始匹配路由的 loader 数据，而不是全部。启用此标志后，路由器将在 `router.initialize()` 期间为没有水合 loader 数据的路由调用 `loader` 函数，并会渲染到最深层提供的 `HydrateFallback`（直到第一个没有水合数据的路由），同时执行未水合的路由。([#11033](https://github.com/remix-run/react-router/pull/11033))

### 次要变更

- 新增 `future.v7_relativeSplatPath` 标志，实现 splat 路由内相对路由的破坏性错误修复。([#11087](https://github.com/remix-run/react-router/pull/11087))
- 新增 `future.v7_partialHydration` future 标志，在服务端渲染时启用数据路由器的部分水合 ([#11033](https://github.com/remix-run/react-router/pull/11033))

### 补丁变更

- 正确处理 `ErrorBoundary` 中的假值错误 ([#11071](https://github.com/remix-run/react-router/pull/11071))
- 捕获并冒泡尝试解包 `loader`/`action` 函数响应时抛出的错误 ([#11061](https://github.com/remix-run/react-router/pull/11061))
- 修复在匹配路由外部渲染 `Link`/`NavLink` 时的 `relative="path"` 问题 ([#11062](https://github.com/remix-run/react-router/pull/11062))

**完整更新日志**: [`v6.20.1...v6.21.0`](https://github.com/remix-run/react-router/compare/react-router@6.20.1...react-router@6.21.0)

## v6.20.1

Date: 2023-12-01

### 补丁变更

- 恢复 splat 路由的 `useResolvedPath` 修复，因为大量应用依赖了该错误行为（参见 [#11052](https://github.com/remix-run/react-router/issues/11052#issuecomment-1836589329)） ([#11078](https://github.com/remix-run/react-router/pull/11078))
  - 我们计划在下一个次要版本中通过 future 标志重新引入此修复（参见[此评论](https://github.com/remix-run/react-router/issues/11052#issuecomment-1836589329)）
  - 此修复包含在 `6.19.0` 和 `6.20.0` 版本中。如果你是从 `6.18.0` 或更早版本升级，则不会受到此修复的影响。

**完整更新日志**: [`v6.20.0...v6.20.1`](https://github.com/remix-run/react-router/compare/react-router@6.20.0...react-router@6.20.1)

## v6.20.0

Date: 2023-11-22

> [!WARNING]
> 请使用 `6.20.1` 或更高版本代替 `6.20.0`。我们发现大量应用依赖了本次发布中修复的错误行为（[#11045](https://github.com/remix-run/react-router/pull/11045)）。我们在 `6.20.1` 中恢复了该修复，并将在后续版本中通过 future 标志重新引入。详见 [#11052](https://github.com/remix-run/react-router/issues/11052#issuecomment-1836589329)。

- 导出 `PathParam` 类型作为公共 API ([#10719](https://github.com/remix-run/react-router/pull/10719))

### 补丁变更

- 启用 `v7_fetcherPersist` 时不再重新验证已卸载的 fetcher ([#11044](https://github.com/remix-run/react-router/pull/11044))
- 修复 splat 路由中 `resolveTo` 的路径解析错误 ([#11045](https://github.com/remix-run/react-router/pull/11045))
  - 这是 [#10983](https://github.com/remix-run/react-router/pull/10983) 的后续修复，处理使用 `getPathContributingMatches` 的其他代码路径
  - 从 `@remix-run/router` 中移除了 `UNSAFE_getPathContributingMatches` 导出，因为 `react-router`/`react-router-dom` 层不再需要它

**完整更新日志**: [`v6.19.0...v6.20.0`](https://github.com/remix-run/react-router/compare/react-router@6.19.0...react-router@6.20.0)

## v6.19.0

Date: 2023-11-16

> [!WARNING]
> 请使用 `6.20.1` 或更高版本代替 `6.19.0`。我们发现大量应用依赖了本次发布中修复的错误行为（[#10983](https://github.com/remix-run/react-router/pull/10983)）。我们在 `6.20.1` 中恢复了该修复，并将在后续版本中通过 future 标志重新引入。详见 [#11052](https://github.com/remix-run/react-router/issues/11052#issuecomment-1836589329)。

### 重要变更

#### `unstable_flushSync` API

本次发布引入了新的 `unstable_flushSync` 选项，可用于命令式 API（`useSubmit`、`useNavigate`、`fetcher.submit`、`fetcher.load`），允许用户选择同步 DOM 更新以实现 pending/乐观 UI。

```js
function handleClick() {
  submit(data, { flushSync: true });
  // Everything is flushed to the DOM so you can focus/scroll to your pending/optimistic UI
  setFocusAndOrScrollToNewlyAddedThing();
}
```

### 次要变更

- 为 `useNavigate`/`useSubmit`/`fetcher.load`/`fetcher.submit` 新增 `unstable_flushSync` 选项，以退出 `React.startTransition` 转而使用 `ReactDOM.flushSync` 进行状态更新 ([#11005](https://github.com/remix-run/react-router/pull/11005))
- 从 [`useBlocker`](https://reactrouter.com/v6/hooks/use-blocker) Hook 中移除 `unstable_` 前缀，因为它已经使用了足够长的时间，我们对该 API 有信心 ([#10991](https://github.com/remix-run/react-router/pull/10991))
  - 我们不计划从 `unstable_usePrompt` 中移除前缀，因为浏览器处理 `window.confirm` 的方式存在差异，导致 React Router 无法保证一致/正确的行为

### 补丁变更

- 修复 `useActionData` 使其返回正确的上下文 action 数据，而不是树中 _任意_ action 数据 ([#11023](https://github.com/remix-run/react-router/pull/11023))
- 修复 `useResolvedPath` 中的错误：在 splat 路由中使用 `useResolvedPath(".")` 会丢失 URL 路径的 splat 部分。([#10983](https://github.com/remix-run/react-router/pull/10983))
  - ⚠️ 这修复了一个相当长期存在的错误，专门针对 splat 路由内的 `"."` 路径，该错误会不正确地丢弃 URL 的 splat 部分。如果你在应用中的 splat 路由内通过 `"."` 进行相对路由，你应该检查你的逻辑是否依赖了这个错误行为并相应更新。
- 修复 `useFetcher` 中变化的 fetcher `key` 在保持挂载时未被正确获取的问题 ([#11009](https://github.com/remix-run/react-router/pull/11009))
- 修复 `useFormAction` 错误地从子路由 `action` 提交中继承 `?index` 查询参数的问题 ([#11025](https://github.com/remix-run/react-router/pull/11025))
- 修复 `NavLink` 当 `to` 地址有尾部斜杠时的 `active` 逻辑 ([#10734](https://github.com/remix-run/react-router/pull/10734))
- 修复类型使 `unstable_usePrompt` 可以接受 `BlockerFunction`，而不仅仅是 `boolean` ([#10991](https://github.com/remix-run/react-router/pull/10991))
- 修复 `relative="path"` 错误：相对路径计算从完整的 location 路径名开始，而不是从当前上下文路由路径名开始。([#11006](https://github.com/remix-run/react-router/pull/11006))

  ```jsx
  <Route path="/a">
    <Route path="/b" element={<Component />}>
      <Route path="/c" />
    </Route>
  </Route>;

  function Component() {
    return (
      <>
        {/* This is now correctly relative to /a/b, not /a/b/c */}
        <Link to=".." relative="path" />
        <Outlet />
      </>
    );
  }
  ```

**完整更新日志**: [`6.18.0...6.19.0`](https://github.com/remix-run/react-router/compare/react-router@6.18.0...react-router@6.19.0)

## v6.18.0

Date: 2023-10-31

### 重要变更

#### 新的 Fetcher API

根据此 [RFC](https://github.com/remix-run/remix/discussions/7698)，我们引入了一些新 API，让你对 fetcher 行为有更细粒度的控制。

- 你现在可以通过 `useFetcher({ key: string })` 指定自己的 fetcher 标识符，这允许你从应用中的不同组件访问同一个 fetcher 实例而无需层层传递 props
- Fetcher 键现在会暴露在 `useFetchers` 返回的 fetcher 上，以便可以通过 `key` 查找
- `Form` 和 `useSubmit` 现在支持可选的 `navigate`/`fetcherKey` props/参数，允许在底层以可选的用户指定 `key` 启动 fetcher 提交
  - `<Form method="post" navigate={false} fetcherKey="my-key">`
  - `submit(data, { method: "post", navigate: false, fetcherKey: "my-key" })`
  - 以这种方式调用的 fetcher 是临时的和无状态的
  - 如果你需要访问这些 fetcher 的状态，需要通过 `useFetchers()` 或 `useFetcher({ key })` 在其他地方查找

#### 持久化 Future 标志（`future.v7_fetcherPersist`）

根据上述相同的 [RFC](https://github.com/remix-run/remix/discussions/7698)，我们引入了新的 `future.v7_fetcherPersist` 标志，允许你启用新的 fetcher 持久化/清理行为。fetcher 不会在卸载时立即被清理，而是会持续存在直到它们返回 `idle` 状态。这使得在源 fetcher 需要卸载的场景中，pending/乐观 UI 变得 _更加容易_。

- 这在某种程度上是一个长期存在的错误修复，因为 `useFetchers()` API 一直应该只反映 **进行中** 的 fetcher 信息用于 pending/乐观 UI——它不应该反映 fetcher 数据或在它们返回 `idle` 状态后仍保留 fetcher
- 启用此标志时请注意以下特定的行为变化，并检查你的应用兼容性：
  - 在仍然挂载时完成的 fetcher 将不再在完成后出现在 `useFetchers()` 中——它们在那里没有用处，因为你可以通过 `useFetcher().data` 访问数据
  - 之前在飞行中卸载的 fetcher 不会立即被中止，而是会在返回 `idle` 状态后被清理
    - 它们在飞行中时仍会通过 `useFetchers` 暴露，以便你卸载后仍可访问 pending/乐观数据
    - 如果 fetcher 完成时不再挂载，其结果不会被后处理——例如重定向不会被跟随，错误不会在 UI 中冒泡
    - 但是，如果 fetcher 使用相同的 `key` 在树的其他地方重新挂载，那么其结果将被处理，即使原始 fetcher 已卸载

### 次要变更

- 新增 fetcher `key` API 和 `navigate=false` 选项 ([#10960](https://github.com/remix-run/react-router/pull/10960))
- 新增 `future.v7_fetcherPersist` 标志 ([#10962](https://github.com/remix-run/react-router/pull/10962))
- 新增 `matchPath` 中对可选路径段的支持 ([#10768](https://github.com/remix-run/react-router/pull/10768))

### 补丁变更

- 修复 `BrowserRouter`、`HashRouter` 和 `MemoryRouter` 上的 `future` prop，使其接受 `Partial<FutureConfig>` 而不是要求所有标志都必须指定 ([#10962](https://github.com/remix-run/react-router/pull/10962))
- 修复 `router.getFetcher`/`router.deleteFetcher` 类型定义中 `key` 被错误地指定为可选参数的问题 ([#10960](https://github.com/remix-run/react-router/pull/10960))

**完整更新日志**: [`6.17.0...6.18.0`](https://github.com/remix-run/react-router/compare/react-router@6.17.0...react-router@6.18.0)

## v6.17.0

Date: 2023-10-16

### 重要变更

#### 视图过渡 🚀

我们很高兴发布对 React Router 中 [View Transitions API](https://developer.mozilla.org/en-US/docs/Web/API/ViewTransition) 的实验性支持！你现在可以触发将导航性 DOM 更新包装在 `document.startViewTransition` 中，以在应用中的 SPA 导航时启用 CSS 动画过渡。

在 React Router 应用中启用视图过渡的最简单方法是通过新的 [`<Link unstable_viewTransition>`](https://reactrouter.com/v6/components/link#unstable_viewtransition) prop。这会将导航 DOM 更新包装在 `document.startViewTransition` 中，从而启用过渡动画。如果不添加额外的 CSS 样式，你将获得基本的淡入淡出动画。

如果你需要为动画应用更细粒度的样式，可以使用 [`unstable_useViewTransitionState`](https://reactrouter.com/v6/hooks/use-view-transition-state) Hook，它会告诉你过渡是否正在进行，你可以用它来应用类或样式：

```jsx
function ImageLink(to, src, alt) {
  const isTransitioning = unstable_useViewTransitionState(to);
  return (
    <Link to={to} unstable_viewTransition>
      <img
        src={src}
        alt={alt}
        style={{
          viewTransitionName: isTransitioning ? "image-expand" : "",
        }}
      />
    </Link>
  );
}
```

你还可以使用 [`<NavLink unstable_viewTransition>`](https://reactrouter.com/v6/components/nav-link#unstable_viewtransition) 简写形式，它会自动管理 Hook 的使用，并在过渡期间自动为 `<a>` 添加 `transitioning` 类名：

```css
a.transitioning img {
  view-transition-name: "image-expand";
}
```

```jsx
<NavLink to={to} unstable_viewTransition>
  <img src={src} alt={alt} />
</NavLink>
```

有关视图过渡的使用示例，请查看我们 [fork 的版本](https://github.com/brophdawg11/react-router-records)（基于优秀的 [Astro Records](https://github.com/Charca/astro-records) 演示）。

有关使用 View Transitions API 的更多信息，请参阅 Google Chrome 团队的[《使用 View Transitions API 实现平滑简单的过渡》](https://developer.chrome.com/docs/web-platform/view-transitions/)指南。

### 次要变更

- 新增视图过渡支持 ([#10916](https://github.com/remix-run/react-router/pull/10916))

### 补丁变更

- 当 `sessionStorage` 不可用时，在 `ScrollRestoration` 中记录警告并优雅降级 ([#10848](https://github.com/remix-run/react-router/pull/10848))
- 修复 `RouterProvider` 的 `future` prop 类型为 `Partial<FutureConfig>`，使得不必指定所有标志 ([#10900](https://github.com/remix-run/react-router/pull/10900))
- 当路径包含 URL 段时，允许 404 检测利用根路由错误边界 ([#10852](https://github.com/remix-run/react-router/pull/10852))
- 修复 `ErrorResponse` 类型以避免泄露内部字段 ([#10876](https://github.com/remix-run/react-router/pull/10876))

**完整更新日志**: [`6.16.0...6.17.0`](https://github.com/remix-run/react-router/compare/react-router@6.16.0...react-router@6.17.0)

## v6.16.0

Date: 2023-09-13

### 次要变更

- 为了在未来推进更严格的 TypeScript 支持，我们计划将用户提供的数据的暴露类型中当前使用的 `any` 替换为 `unknown`。为了在不引入破坏性变更的情况下在 Remix v2 中实现这一点，我们为一些共享类型添加了泛型。这些类型在 React Router 中仍然默认为 `any`，并在 Remix 中被覆盖为 `unknown`。在 React Router v7 中，我们计划将这些作为破坏性变更移至 `unknown`。([#10843](https://github.com/remix-run/react-router/pull/10843))
  - `Location` 现在接受一个泛型用于 `location.state` 值
  - `ActionFunctionArgs`/`ActionFunction`/`LoaderFunctionArgs`/`LoaderFunction` 现在接受一个泛型用于 `context` 参数（仅在通过 `createStaticHandler` 的 SSR 用法中使用）
  - `useMatches` 的返回类型（现导出为 `UIMatch`）接受 `match.data` 和 `match.handle` 的泛型——两者已经设置为 `unknown`
- 将 `@private` 类导出 `ErrorResponse` 移至 `UNSAFE_ErrorResponseImpl` 导出，因为它是实现细节，用户代码中不应该构造 `ErrorResponse` 实例。这使我们能够导出一个 `type ErrorResponse`，通过 `InstanceType` 关联到类的实例。用户代码应该只将 `ErrorResponse` 作为类型使用，并通过 `isRouteErrorResponse` 进行类型缩窄。([#10811](https://github.com/remix-run/react-router/pull/10811))
- 导出 `ShouldRevalidateFunctionArgs` 接口 ([#10797](https://github.com/remix-run/react-router/pull/10797))
- 移除仅 Remix v1 后向兼容层需要的私有/内部 API，Remix v2 中不再需要（`_isFetchActionRedirect`、`_hasFetcherDoneAnything`）([#10715](https://github.com/remix-run/react-router/pull/10715))

### 补丁变更

- 正确编码服务端渲染中的 URI 以避免水合错误 ([#10769](https://github.com/remix-run/react-router/pull/10769))
- 在中止的 `query`/`queryRoute` 调用的错误消息中添加 method/url ([#10793](https://github.com/remix-run/react-router/pull/10793))
- 修复 `route.lazy` 路由上 loader/action 抛出错误的竞态条件 ([#10778](https://github.com/remix-run/react-router/pull/10778))
- 修复 `shouldRevalidate` 参数对象上 `actionResult` 的类型 ([#10779](https://github.com/remix-run/react-router/pull/10779))

**完整更新日志**: [`v6.15.0...v6.16.0`](https://github.com/remix-run/react-router/compare/react-router@6.15.0...react-router@6.16.0)

## v6.15.0

Date: 2023-08-10

### 次要变更

- 新增 `redirectDocument()` 函数，允许用户指定 `loader`/`action` 的重定向应触发文档重新加载（通过 `window.location`）而不是尝试通过 React Router 导航到重定向位置 ([#10705](https://github.com/remix-run/react-router/pull/10705))

### 补丁变更

- 确保 `useRevalidator` 在没有活跃重新验证时在重新渲染之间保持引用稳定 ([#10707](https://github.com/remix-run/react-router/pull/10707))
- 确保 hash 历史记录的 hash 路径名始终包含前导斜杠 ([#10753](https://github.com/remix-run/react-router/pull/10753))
- 修复影响 Firefox 中使用 `URLSearchParams` 和 `useSearchParams` Hook 的 Web 扩展的边缘情况 ([#10620](https://github.com/remix-run/react-router/pull/10620))
- 重新排序 `unstable_usePrompt` 中的效果以避免在解除阻塞并同步执行导航时抛出异常 ([#10687](https://github.com/remix-run/react-router/pull/10687), [#10718](https://github.com/remix-run/react-router/pull/10718))
- SSR：对于未指定 action 的情况，不在 `useFormAction()` 中包含 hash，因为它在服务器端无法确定且会导致水合问题 ([#10758](https://github.com/remix-run/react-router/pull/10758))
- SSR：修复 `queryRoute` 中没有始终识别抛出的 `Response` 实例的问题 ([#10717](https://github.com/remix-run/react-router/pull/10717))
- `react-router-native`：更新 `@ungap/url-search-params` 依赖从 `^0.1.4` 到 `^0.2.2` ([#10590](https://github.com/remix-run/react-router/pull/10590))

**完整更新日志**: [`v6.14.2...v6.15.0`](https://github.com/remix-run/react-router/compare/react-router@6.14.2...react-router@6.15.0)

## v6.14.2

Date: 2023-07-17

### 补丁变更

- 添加缺失的 `<Form state>` prop 以在提交导航时填充 `history.state` ([#10630](https://github.com/remix-run/react-router/pull/10630))
- 如果 `defer` promise 以 `undefined` resolve/reject，则触发错误，以匹配 loader 和 action 必须返回值或 `null` 的行为 ([#10690](https://github.com/remix-run/react-router/pull/10690))
- 正确处理被正常导航中断的 fetcher 重定向 ([#10674](https://github.com/remix-run/react-router/pull/10674))
- 初始加载的 fetcher 不应在 GET 导航时自动重新验证 ([#10688](https://github.com/remix-run/react-router/pull/10688))
- 通过 `<ScrollRestoration>` 模拟 hash 滚动时正确解码元素 ID ([#10682](https://github.com/remix-run/react-router/pull/10682))
- TypeScript：增强 `Route.lazy` 的返回类型以禁止返回空对象 ([#10634](https://github.com/remix-run/react-router/pull/10634))
- SSR：支持 `Error` 子类（如 `ReferenceError`/`TypeError`）的正确水合 ([#10633](https://github.com/remix-run/react-router/pull/10633))

**完整更新日志**: [`v6.14.1...v6.14.2`](https://github.com/remix-run/react-router/compare/react-router@6.14.1...react-router@6.14.2)

## v6.14.1

Date: 2023-06-30

### 补丁变更

- 修复 `unstable_useBlocker` 在使用不稳定的 blocker 函数时的循环问题 ([#10652](https://github.com/remix-run/react-router/pull/10652))
- 修复后续导航中重复使用 blocker 的问题 ([#10656](https://github.com/remix-run/react-router/pull/10656))
- 更新依赖：
  - `@remix-run/router@1.7.1`

**完整更新日志**: [`v6.14.0...v6.14.1`](https://github.com/remix-run/react-router/compare/react-router@6.14.0...react-router@6.14.1)

## v6.14.0

Date: 2023-06-23

### 重要变更

#### JSON/Text 提交

`6.14.0` 通过 `useSubmit`/`fetcher.submit` 新增了对 JSON 和 Text 提交的支持，因为如果你在客户端 SPA 中工作，将数据序列化为 `FormData` 并不总是方便的。要启用这些编码，只需指定相应的 `formEncType`：

**启用 `application/json` 编码：**

```js
function Component() {
  let navigation = useNavigation();
  let submit = useSubmit();
  submit({ key: "value" }, { method: "post", encType: "application/json" });
  // navigation.formEncType => "application/json"
  // navigation.json        => { key: "value" }
}

async function action({ request }) {
  // request.headers.get("Content-Type") => "application/json"
  // await request.json()                => { key: "value" }
}
```

**启用 `text/plain` 编码：**

```js
function Component() {
  let navigation = useNavigation();
  let submit = useSubmit();
  submit("Text submission", { method: "post", encType: "text/plain" });
  // navigation.formEncType => "text/plain"
  // navigation.text        => "Text submission"
}

async function action({ request }) {
  // request.headers.get("Content-Type") => "text/plain"
  // await request.text()                => "Text submission"
}
```

**⚠️ 默认行为将在 v7 中改变**

请注意，为避免破坏性变更，默认行为仍然会将简单的键/值 JSON 对象编码为 `FormData` 实例：

```jsx
function Component() {
  let navigation = useNavigation();
  let submit = useSubmit();
  submit({ key: "value" }, { method: "post" });
  // navigation.formEncType => "application/x-www-form-urlencoded"
  // navigation.formData    => FormData instance
}

async function action({ request }) {
  // request.headers.get("Content-Type") => "application/x-www-form-urlencoded"
  // await request.formData()            => FormData instance
}
```

此行为可能会在 v7 中改变，因此最好对任何 JSON 对象提交明确指定 `formEncType: "application/x-www-form-urlencoded"` 或 `formEncType: "application/json"`，以便于将来迁移到 v7。

### 次要变更

- 新增对 `useSubmit`/`fetcher.submit` 的 `application/json` 和 `text/plain` 编码支持。为反映这些额外类型，`useNavigation`/`useFetcher` 现在还包含 `navigation.json`/`navigation.text` 和 `fetcher.json`/`fetcher.text`（如适用）。([#10413](https://github.com/remix-run/react-router/pull/10413))

### 补丁变更

- 当从 `submitter` 元素提交表单时，在现代浏览器中优先使用内置的 `new FormData(form, submitter)` 而非之前的手动方式（支持新 `submitter` 参数的浏览器） ([#9865](https://github.com/remix-run/react-router/pull/9865))
  - 对于不支持的浏览器，我们继续将提交按钮的条目追加到末尾，并还新增了对 `type="image"` 按钮的基本支持
  - 如果开发者想要对旧版浏览器的完全规范兼容支持，可以使用 `formdata-submitter-polyfill`
- 在更新 React Router 状态 _之前_ 调用 `window.history.pushState/replaceState`（而不是之后），以便在 React 17 同步渲染期间 `window.location` 与 `useLocation` 匹配 ([#10448](https://github.com/remix-run/react-router/pull/10448))
  - ⚠️ 注意：一般来说，应用不应该依赖 `window.location`，而应始终在可能时引用 `useLocation`，因为 `window.location` 不会 100% 的时间保持同步（由于 `popstate` 事件、并发模式等）
- 避免对尚未完成数据加载的 fetcher 调用 `shouldRevalidate` ([#10623](https://github.com/remix-run/react-router/pull/10623))
- 从传递给 `<ScrollRestoration getKey>` 的 `location` 中去除 `basename`，以匹配 `useLocation` 的行为 ([#10550](https://github.com/remix-run/react-router/pull/10550))
- 从传递给 `unstable_useBlocker` 函数的 location 中去除 `basename`，以匹配 `useLocation` 的行为 ([#10573](https://github.com/remix-run/react-router/pull/10573))
- 修复 `StrictMode` 中的 `unstable_useBlocker` key 问题 ([#10573](https://github.com/remix-run/react-router/pull/10573))
- 修复传递数字 `0` 值参数时的 `generatePath` 问题 ([#10612](https://github.com/remix-run/react-router/pull/10612))
- 修复 React 17 上 `tsc --skipLibCheck:false` 的问题 ([#10622](https://github.com/remix-run/react-router/pull/10622))
- 升级 `typescript` 到 5.1 ([#10581](https://github.com/remix-run/react-router/pull/10581))

**完整更新日志**: [`v6.13.0...v6.14.0`](https://github.com/remix-run/react-router/compare/react-router@6.13.0...react-router@6.14.0)

## v6.13.0

Date: 2023-06-14

### 重要变更

`6.13.0` 本质上是一个补丁版本，但由于添加了新的 future 标志，因此使用了 SemVer 次要版本号。

#### `future.v7_startTransition`

简而言之，`6.13.0` 与 [`6.12.0`](https://github.com/remix-run/react-router/releases/tag/react-router%406.12.0) 相同，但我们将 `React.startTransition` 的使用放在了可选启用的 `future.v7_startTransition` [future 标志](https://reactrouter.com/v6/guides/api-development-strategy)之后，因为我们发现现实中有些应用以与 `React.startTransition` 不兼容的方式使用 `Suspense`。

因此，在 `6.13.0` 中默认行为将不再使用 `React.startTransition`：

```jsx
<BrowserRouter>
  <Routes>{/*...*/}</Routes>
</BrowserRouter>

<RouterProvider router={router} />
```

如果你希望启用 `React.startTransition`，请将 future 标志传递给你的路由器组件：

```jsx
<BrowserRouter future={{ v7_startTransition: true }}>
  <Routes>{/*...*/}</Routes>
</BrowserRouter>

<RouterProvider router={router} future={{ v7_startTransition: true }}/>
```

我们建议尽早采用此标志以更好地兼容 React 并发模式，但如果遇到问题，可以继续不使用 `React.startTransition` 直到 v7。问题通常归结为在渲染周期中创建新的 promise，因此如果你在启用 `React.startTransition` 时遇到问题，应该将 promise 的创建移出渲染周期或放在 `useMemo` 之后。

### 次要变更

- 将 `React.startTransition` 的使用放在 future 标志之后 ([#10596](https://github.com/remix-run/react-router/pull/10596))

### 补丁变更

- 解决 webpack/terser 在生产模式下的 `React.startTransition` 压缩错误 ([#10588](https://github.com/remix-run/react-router/pull/10588))

**完整更新日志**: [`v6.12.1...v6.13.0`](https://github.com/remix-run/react-router/compare/react-router@6.12.1...react-router@6.13.0)

## v6.12.1

Date: 2023-06-08

> [!WARNING]
> 请使用 `6.13.0` 或更高版本代替 `6.12.0`/`6.12.1`。这些版本存在 Webpack 构建/压缩问题，导致构建失败或生产包中出现无效的压缩代码。详见 [#10569](https://github.com/remix-run/react-router/pull/10569) 和 [#10579](https://github.com/remix-run/react-router/issues/10579)。

- 调整 `React.startTransition` 的特性检测以修复 webpack + React 17 的编译错误 ([#10569](https://github.com/remix-run/react-router/pull/10569))
  **完整更新日志**: [`v6.12.0...v6.12.1`](https://github.com/remix-run/react-router/compare/react-router@6.12.0...react-router@6.12.1)

## v6.12.0

Date: 2023-06-06

> [!WARNING]
> 请使用 `6.13.0` 或更高版本代替 `6.12.0`/`6.12.1`。这些版本存在 Webpack 构建/压缩问题，导致构建失败或生产包中出现无效的压缩代码。详见 [#10569](https://github.com/remix-run/react-router/pull/10569) 和 [#10579](https://github.com/remix-run/react-router/issues/10579)。

#### 支持 `React.startTransition`

在 `6.12.0` 中，我们通过将内部路由器状态更新包装在 [`React.startTransition`](https://react.dev/reference/react/startTransition) 中，增强了对挂起组件的支持。这意味着，例如，如果目标路由中的某个组件挂起而你没有提供 [`Suspense`](https://react.dev/reference/react/Suspense) 边界来显示回退内容，React 会延迟新 UI 的渲染并显示旧 UI，直到该异步操作解析完成。这对于等待图片或 CSS 文件加载等场景非常有用（从技术上说，是的，你可以用它来加载数据，但我们仍然建议使用 loader 😀）。有关此用法的快速概览，请查看 [Ryan 在 Twitter 上的演示](https://twitter.com/remix_run/status/1658976420767604736)。

### 次要变更

- 将内部路由器状态更新包装在 `React.startTransition` 中 ([#10438](https://github.com/remix-run/react-router/pull/10438))

### 补丁变更

- 允许在提交的 fetcher 被删除时完成 fetcher 重新验证 ([#10535](https://github.com/remix-run/react-router/pull/10535))
- 尝试使用不可序列化的 state 执行 `PUSH` 导航时重新抛出 `DOMException`（`DataCloneError`）。([#10427](https://github.com/remix-run/react-router/pull/10427))
- 确保存在 hash 时仍进行重新验证 ([#10516](https://github.com/remix-run/react-router/pull/10516))
- 升级 `jest` 和 `jsdom` ([#10453](https://github.com/remix-run/react-router/pull/10453))
- 更新依赖：
  - `@remix-run/router@1.6.3`（[更新日志](https://github.com/remix-run/react-router/blob/main/packages/router/CHANGELOG.md#163)）

**完整更新日志**: [`v6.11.2...v6.12.0`](https://github.com/remix-run/react-router/compare/react-router@6.11.2...react-router@6.12.0)

## v6.11.2

Date: 2023-05-17

### 补丁变更

- 修复在 `<RouterProvider>` 中后代 `<Routes>` 内的 `basename` 重复问题 ([#10492](https://github.com/remix-run/react-router/pull/10492))
- 修复存在 hash 时初始数据加载不会启动的错误 ([#10493](https://github.com/remix-run/react-router/pull/10493))
- 导出 `SetURLSearchParams` 类型 ([#10444](https://github.com/remix-run/react-router/pull/10444))
- 通过在 `_internalSetRoutes` 中正确重建新路由和 `manifest` 来修复 Remix HMR 驱动的错误边界 ([#10437](https://github.com/remix-run/react-router/pull/10437))

**完整更新日志**: [`v6.11.1...v6.11.2`](https://github.com/remix-run/react-router/compare/react-router@6.11.1...react-router@6.11.2)

## v6.11.1

Date: 2023-05-03

### 补丁变更

- 修复后代 `<Routes>` 中 `Component` API 的用法问题 ([#10434](https://github.com/remix-run/react-router/pull/10434))
- 修复从 `<RouterProvider>` 内部的 `<Routes>` 调用 `useNavigate` 的错误 ([#10432](https://github.com/remix-run/react-router/pull/10432))
- 修复使用数据路由器时严格模式下 `<Navigate>` 的用法问题 ([#10435](https://github.com/remix-run/react-router/pull/10435))
- 修复在没有路径时导航的 `basename` 处理问题 ([#10433](https://github.com/remix-run/react-router/pull/10433))
- “相同 hash” 导航不再重新运行 loader 以匹配浏览器行为（即 `/path#hash -> /path#hash`） ([#10408](https://github.com/remix-run/react-router/pull/10408))

**完整更新日志**: [`v6.11.0...v6.11.1`](https://github.com/remix-run/react-router/compare/react-router@6.11.0...react-router@6.11.1)

## v6.11.0

Date: 2023-04-28

### 次要变更

- 在 `useFetcher` 中启用 `basename` 支持 ([#10336](https://github.com/remix-run/react-router/pull/10336))
  - 如果你之前通过手动添加 `basename` 前缀来解决此问题，那么你需要从你的 `fetcher` 调用中移除手动添加的 `basename`（`fetcher.load('/basename/route') -> fetcher.load('/route')`）
- 更新依赖：
  - `@remix-run/router@1.6.0`（[更新日志](https://github.com/remix-run/react-router/blob/main/packages/router/CHANGELOG.md#160)）

### 补丁变更

- 使用 `RouterProvider` 时，`useNavigate`/`useSubmit`/`fetcher.submit` 现在在 location 变化时保持稳定，因为我们可以通过 `@remix-run/router` 实例处理相对路由，不再依赖 `useLocation()` ([#10336](https://github.com/remix-run/react-router/pull/10336))
  - 使用 `BrowserRouter` 时，这些 Hook 在 location 变化时仍然不稳定，因为它们仍然依赖 `useLocation()`
- Fetcher 不应再在搜索参数变化或导航到相同 URL 时重新验证，仅在 `action` 提交或 `router.revalidate` 调用时重新验证 ([#10344](https://github.com/remix-run/react-router/pull/10344))
- 修复在路由定义上使用 `Component` 代替 `element` 时的不必要重新渲染 ([#10287](https://github.com/remix-run/react-router/pull/10287))
- 对 `<Link to="//">` 和其他无效 URL 值优雅降级 ([#10367](https://github.com/remix-run/react-router/pull/10367))
- 从 `useSyncExternalStore` 切换到 `useState` 用于 `<RouterProvider>` 中内部 `@remix-run/router` 路由器状态同步。我们发现了一些[微妙的错误](https://codesandbox.io/s/use-sync-external-store-loop-9g7b81)，其中路由器状态更新会在其他正常 `useState` 更新 _之前_ 传播，这可能导致 `useEffect` 调用中的问题。([#10377](https://github.com/remix-run/react-router/pull/10377), [#10409](https://github.com/remix-run/react-router/pull/10409))
- 在开发环境中将被默认错误边界捕获的 loader/action 错误记录到控制台，以便更容易地评估堆栈跟踪 ([#10286](https://github.com/remix-run/react-router/pull/10286))
- 修复当 `RouterProvider` 存在错误时阻止后代 `<Routes>` 渲染的错误 ([#10374](https://github.com/remix-run/react-router/pull/10374))
- 通过在布局效果中设置 `activeRef` 来修复渲染周期中 `useNavigate` 的检测，允许将 `navigate` 函数传递给子组件并在 `useEffect` 中调用 ([#10394](https://github.com/remix-run/react-router/pull/10394))
- 允许 `useRevalidator()` 解决 loader 驱动的错误边界场景 ([#10369](https://github.com/remix-run/react-router/pull/10369))
- 增强 `LoaderFunction`/`ActionFunction` 返回类型以防止 `undefined` 成为有效的返回值 ([#10267](https://github.com/remix-run/react-router/pull/10267))
- 确保对没有 `loader` 的路由的 `fetcher.load` 调用返回正确的 404 错误 ([#10345](https://github.com/remix-run/react-router/pull/10345))
- 解耦重新验证 fetcher 和触发它们的事物之间的 `AbortController` 用法，使得卸载/删除重新验证的 fetcher 不会影响正在进行的触发导航/重新验证 ([#10271](https://github.com/remix-run/react-router/pull/10271))

**完整更新日志**: [`v6.10.0...v6.11.0`](https://github.com/remix-run/react-router/compare/react-router@6.10.0...react-router@6.11.0)

## v6.10.0

Date: 2023-03-29

### 重要变更

我们最近在 Remix 博客上发布了一篇名为 ["给你的 Remix 应用做未来保障"](https://remix.run/blog/future-flags) 的文章，介绍了我们确保 Remix 和 React Router 应用顺利升级的策略。React Router `6.10.0` 为这些标志（用于数据路由器）添加了支持，你可以在创建路由器时指定：

```js
const router = createBrowserRouter(routes, {
  future: {
    // specify future flags here
  },
});
```

你还可以查看[这里](https://reactrouter.com/en/dev/guides/api-development-strategy)和[这里](https://reactrouter.com/en/dev/routers/create-browser-router#future)的文档。

### 次要变更

#### `future.v7_normalizeFormMethod`

引入的第一个 future 标志是 `future.v7_normalizeFormMethod`，它将暴露的 `useNavigation()/useFetcher()` 的 `formMethod` 字段规范化为大写 HTTP 方法，以与 `fetch()`（以及部分 Remix）的行为保持一致。([#10207](https://github.com/remix-run/react-router/pull/10207))

- 当 `future.v7_normalizeFormMethod` 未指定或设置为 `false`（默认 v6 行为）时，
  - `useNavigation().formMethod` 是小写
  - `useFetcher().formMethod` 是小写
- 当 `future.v7_normalizeFormMethod === true` 时：
  - `useNavigation().formMethod` 是大写
  - `useFetcher().formMethod` 是大写

### 补丁变更

- 修复 `createStaticHandler` 以同时检查路由上的 `ErrorBoundary`（除了 `errorElement`） ([#10190](https://github.com/remix-run/react-router/pull/10190))
- 修复在 `createRoutesFromElements` 中使用 Fragment 时的路由 ID 生成问题 ([#10193](https://github.com/remix-run/react-router/pull/10193))
- 如果 fetcher action 重定向，则将 fetcher 提交信息传递给 `shouldRevalidate` ([#10208](https://github.com/remix-run/react-router/pull/10208))
- 正确处理路由器初始化期间的 `lazy()` 错误 ([#10201](https://github.com/remix-run/react-router/pull/10201))
- 移除对 `DeferredData` 的 `instanceof` 检查以适应 SSR 打包场景中的 ESM/CJS 边界 ([#10247](https://github.com/remix-run/react-router/pull/10247))
- 更新到最新的 `@remix-run/web-fetch@4.3.3` ([#10216](https://github.com/remix-run/react-router/pull/10216))

**完整更新日志**: [`v6.9.0...v6.10.0`](https://github.com/remix-run/react-router/compare/react-router@6.9.0...react-router@6.10.0)

## v6.9.0

Date: 2023-03-10

### 重要变更

#### `Component`/`ErrorBoundary` 路由属性

React Router 现在支持一种替代方式来定义路由的 `element` 和 `errorElement` 字段，即使用 React 组件而非 React 元素。你可以将 React 组件传递给新的 `Component` 和 `ErrorBoundary` 字段。两者在功能上没有区别，你可以选择你喜欢的方式 😀。不应该同时定义两者，但如果你这样做了，`Component`/`ErrorBoundary` 会“获胜”

**JSON 语法示例**

```jsx
// Both of these work the same:
const elementRoutes = [{
  path: '/',
  element: <Home />,
  errorElement: <HomeError />,
}]

const componentRoutes = [{
  path: '/',
  Component: Home,
  ErrorBoundary: HomeError,
}]

function Home() { ... }
function HomeError() { ... }
```

**JSX 语法示例**

```jsx
// Both of these work the same:
const elementRoutes = createRoutesFromElements(
  <Route path='/' element={<Home />} errorElement={<HomeError /> } />
);

const componentRoutes = createRoutesFromElements(
  <Route path='/' Component={Home} ErrorBoundary={HomeError} />
);

function Home() { ... }
function HomeError() { ... }
```

#### 引入懒加载路由模块

为了保持你的应用包体积较小并支持路由的代码分割，我们引入了新的 `lazy()` 路由属性。这是一个异步函数，解析路由定义中非路由匹配的部分（`loader`、`action`、`element`/`Component`、`errorElement`/`ErrorBoundary`、`shouldRevalidate`、`handle`）。

懒加载路由在初始加载以及导航或 fetcher 调用的 `loading` 或 `submitting` 阶段被解析。你不能懒加载定义路由匹配属性（`path`、`index`、`children`），因为我们只在匹配已知路由后才执行你的懒加载路由函数。

你的 `lazy` 函数通常会返回动态导入的结果。

```jsx
// In this example, we assume most folks land on the homepage so we include that
// in our critical-path bundle, but then we lazily load modules for /a and /b so
// they don't load until the user navigates to those routes
let routes = createRoutesFromElements(
  <Route path="/" element={<Layout />}>
    <Route index element={<Home />} />
    <Route path="a" lazy={() => import("./a")} />
    <Route path="b" lazy={() => import("./b")} />
  </Route>,
);
```

然后在你的懒加载路由模块中，导出你希望为路由定义的属性：

```jsx
export async function loader({ request }) {
  let data = await fetchData(request);
  return json(data);
}

// Export a `Component` directly instead of needing to create a React Element from it
export function Component() {
  let data = useLoaderData();

  return (
    <>
      <h1>You made it!</h1>
      <p>{data}</p>
    </>
  );
}

// Export an `ErrorBoundary` directly instead of needing to create a React Element from it
export function ErrorBoundary() {
  let error = useRouteError();
  return isRouteErrorResponse(error) ? (
    <h1>
      {error.status} {error.statusText}
    </h1>
  ) : (
    <h1>{error.message || error}</h1>
  );
}
```

可以在仓库的 [`examples/lazy-loading-router-provider`](https://github.com/remix-run/react-router/tree/main/examples/lazy-loading-router-provider) 目录中找到此功能的使用示例。更多信息请查看 [`lazy` 文档](https://reactrouter.com/v6/route/lazy)。

🙌 非常感谢 @rossipedia 的[初始提案](https://github.com/remix-run/react-router/discussions/9826)和 [POC 实现](https://github.com/remix-run/react-router/pull/9830)。

### 次要变更

- 新增 `route.Component`/`route.ErrorBoundary` 属性支持 ([#10045](https://github.com/remix-run/react-router/pull/10045))
- 新增 `route.lazy` 支持 ([#10045](https://github.com/remix-run/react-router/pull/10045))

### 补丁变更

- 改善上下文提供者的记忆化以避免不必要的重新渲染 ([#9983](https://github.com/remix-run/react-router/pull/9983))
- 修复 `generatePath` 在某些情况下错误应用参数的问题 ([#10078](https://github.com/remix-run/react-router/pull/10078))
- `[react-router-dom-v5-compat]` 添加缺失的数据路由器 API 重新导出 ([#10171](https://github.com/remix-run/react-router/pull/10171))

**完整更新日志**: [`v6.8.2...v6.9.0`](https://github.com/remix-run/react-router/compare/react-router@6.8.2...react-router@6.9.0)

## v6.8.2

Date: 2023-02-27

### 补丁变更

- 将 `<Link to>` 中与当前源相同但在路由器 `basename` 之外的绝对 URL 视为外部链接 ([#10135](https://github.com/remix-run/react-router/pull/10135))
- 对于路由器 `basename` 之外的同源绝对 URL，正确执行硬重定向 ([#10076](https://github.com/remix-run/react-router/pull/10076))
- 修复绝对 `<Link to>` URL 的 SSR 问题 ([#10112](https://github.com/remix-run/react-router/pull/10112))
- 正确转义 `StaticRouterProvider` 序列化水合数据中的 HTML 字符 ([#10068](https://github.com/remix-run/react-router/pull/10068))
- 修复 `useBlocker` 在 SSR 期间返回 `IDLE_BLOCKER` 的问题 ([#10046](https://github.com/remix-run/react-router/pull/10046))
- 确保 `createStaticHandler` 的 `query()` 方法中 `defer` loader 响应保留状态码和 headers ([#10077](https://github.com/remix-run/react-router/pull/10077))
- 将 `invariant` 改为 `UNSAFE_invariant` 导出，因为它仅用于内部使用 ([#10066](https://github.com/remix-run/react-router/pull/10066))

**完整更新日志**: [`v6.8.1...v6.8.2`](https://github.com/remix-run/react-router/compare/react-router@6.8.1...react-router@6.8.2)

## v6.8.1

Date: 2023-02-06

### 补丁变更

- 移除 POP 导航的不准确控制台警告并更新活跃 blocker 逻辑 ([#10030](https://github.com/remix-run/react-router/pull/10030))
- 仅在绝对 URL 重定向时检查不同的 origin ([#10033](https://github.com/remix-run/react-router/pull/10033))
- 改进 `Link` 组件中的绝对 URL 检测（现在还支持 `mailto:` URL） ([#9994](https://github.com/remix-run/react-router/pull/9994))
- 修复部分对象（仅包含 search 或 hash）的路径名丢失当前路径值的问题 ([#10029](https://github.com/remix-run/react-router/pull/10029))

**完整更新日志**: [`v6.8.0...v6.8.1`](https://github.com/remix-run/react-router/compare/react-router@6.8.0...react-router@6.8.1)

## v6.8.0

Date: 2023-01-26

### 次要变更

支持 `<Link to>` 中的绝对 URL。如果 URL 是当前源的，仍会执行客户端导航。如果 URL 是不同源的，则会对新源执行全新的文档请求。([#9900](https://github.com/remix-run/react-router/pull/9900))

```tsx
<Link to="https://neworigin.com/some/path">    {/* Document request */}
<Link to="//neworigin.com/some/path">          {/* Document request */}
<Link to="https://www.currentorigin.com/path"> {/* Client-side navigation */}
```

### 补丁变更

- 修复 2 个关于重新验证 fetcher 的 `shouldRevalidate` 调用的独立问题 ([#9948](https://github.com/remix-run/react-router/pull/9948))
  - `shouldRevalidate` 函数之前仅在 _显式_ 重新验证场景（mutation 之后、手动 `useRevalidator` 调用、或 Remix 中用于 cookie 设置的 `X-Remix-Revalidate` header）中被调用。它未被正确地在 _隐式_ 重新验证场景（同样适用于导航 `loader` 重新验证的场景，如搜索参数变化或点击当前页面的链接）中调用。现在在这些额外场景中也会被正确调用。
  - 传递的参数之前不正确且彼此不一致，因为 `current*`/`next*` 参数反映的是静态的 `fetcher.load` URL（因此是相同的）。它们应该反映触发重新验证的导航（就像 `form*` 参数那样）。现在这些参数正确地反映触发导航。
- 修复通过 `useSearchParams` 删除搜索参数的错误 ([#9969](https://github.com/remix-run/react-router/pull/9969))
- 在 `<fetcher.Form>` 上尊重 `preventScrollReset` ([#9963](https://github.com/remix-run/react-router/pull/9963))
- 修复手动 URL 更改时 hash 路由器的导航问题 ([#9980](https://github.com/remix-run/react-router/pull/9980))
- 将 `<ScrollRestoration>` 从 `beforeunload` 改为 `pagehide`。这具有更好的跨浏览器支持，特别是在 Mobile Safari 上。([#9945](https://github.com/remix-run/react-router/pull/9945))
- 不在仅有 hash 变化的 mutation 提交时短路 ([#9944](https://github.com/remix-run/react-router/pull/9944))
- 从 `isRouteErrorResponse` 中移除 `instanceof` 检查以避免服务器端打包问题 ([#9930](https://github.com/remix-run/react-router/pull/9930))
- 检测 `defer` 调用仅包含关键数据时移除 `AbortController` ([#9965](https://github.com/remix-run/react-router/pull/9965))
- 在 URL 编码 `File` `FormData` 条目时发送 name 作为 value ([#9867](https://github.com/remix-run/react-router/pull/9867))
- `react-router-dom-v5-compat` - 修复使用 `CompatRouter` 时 SSR `useLayoutEffect` 的 `console.error` ([#9820](https://github.com/remix-run/react-router/pull/9820))

**完整更新日志**: [`v6.7.0...v6.8.0`](https://github.com/remix-run/react-router/compare/react-router@6.7.0...react-router@6.8.0)

## v6.7.0

Date: 2023-01-18

### 次要变更

- 新增 `unstable_useBlocker`/`unstable_usePrompt` Hook，用于在应用的 location origin 内阻止导航 ([#9709](https://github.com/remix-run/react-router/pull/9709), [#9932](https://github.com/remix-run/react-router/pull/9932))
- 为 `<Form>` 新增 `preventScrollReset` prop ([#9886](https://github.com/remix-run/react-router/pull/9886))

### 补丁变更

- 为 `useBeforeUnload` 添加传透事件监听器选项参数 ([#9709](https://github.com/remix-run/react-router/pull/9709))
- 修复存在可选参数时 `generatePath` 的问题 ([#9764](https://github.com/remix-run/react-router/pull/9764))
- 更新 `<Await>` 以接受 `ReactNode` 作为 children 函数返回结果 ([#9896](https://github.com/remix-run/react-router/pull/9896))
- 改进 action/loader 中绝对重定向 URL 的检测 ([#9829](https://github.com/remix-run/react-router/pull/9829))
- 修复使用 memory history 时的 URL 创建问题 ([#9814](https://github.com/remix-run/react-router/pull/9814))
- 修复提交重定向时的滚动重置问题 ([#9886](https://github.com/remix-run/react-router/pull/9886))
- 修复同源绝对重定向的 404 错误 ([#9913](https://github.com/remix-run/react-router/pull/9913))
- 简化测试中的 `jsdom` 错误解决方案 ([#9824](https://github.com/remix-run/react-router/pull/9824))

**完整更新日志**: [`v6.6.2...v6.7.0`](https://github.com/remix-run/react-router/compare/react-router@6.6.2...react-router@6.7.0)

## v6.6.2

Date: 2023-01-09

### 补丁变更

- 确保 SSR 期间 `useId` 的一致性 ([#9805](https://github.com/remix-run/react-router/pull/9805))

**完整更新日志**: [`v6.6.1...v6.6.2`](https://github.com/remix-run/react-router/compare/react-router@6.6.1...react-router@6.6.2)

## v6.6.1

Date: 2022-12-23

### 补丁变更

- 在 action 重定向时将提交信息包含在 `shouldRevalidate` 中 ([#9777](https://github.com/remix-run/react-router/pull/9777), [#9782](https://github.com/remix-run/react-router/pull/9782))
- 在 action 重定向到当前位置时重置 `actionData` ([#9772](https://github.com/remix-run/react-router/pull/9772))

**完整更新日志**: [`v6.6.0...v6.6.1`](https://github.com/remix-run/react-router/compare/react-router@6.6.0...react-router@6.6.1)

## v6.6.0

Date: 2022-12-21

### 重要变更

本次要版本主要是为了稳定化数据路由器的 SSR API，因为我们已经在 Remix 中作为 [React Router-ing Remix](https://remix.run/blog/react-routering-remix) 工作的一部分接入了新的 `RouterProvider`。

### 次要变更

- 移除 `createStaticHandler`/`createStaticRouter`/`StaticRouterProvider` 的 `unstable_` 前缀 ([#9738](https://github.com/remix-run/react-router/pull/9738))
- 新增 `useBeforeUnload()` Hook ([#9664](https://github.com/remix-run/react-router/pull/9664))

### 补丁变更

- 支持大写的 `<Form method>` 和 `useSubmit` method 值 ([#9664](https://github.com/remix-run/react-router/pull/9664))
- 修复 `<button formmethod>` 表单提交覆盖问题 ([#9664](https://github.com/remix-run/react-router/pull/9664))
- 修复提交时的显式 `replace` 和导航到新路径时的 `PUSH` ([#9734](https://github.com/remix-run/react-router/pull/9734))
- 阻止在 `errorElement` 中使用 `useLoaderData` ([#9735](https://github.com/remix-run/react-router/pull/9735))
- 从 `StaticRouterProvider` 正确水合 `Error` 对象 ([#9664](https://github.com/remix-run/react-router/pull/9664))
- 对于带有 `hydrationData` 的 SSR 应用，跳过初始滚动恢复 ([#9664](https://github.com/remix-run/react-router/pull/9664))
- 修复错误时 loader/action 数据未被正确清除的几个错误 ([#9735](https://github.com/remix-run/react-router/pull/9735))

**完整更新日志**: [`v6.5.0...v6.6.0`](https://github.com/remix-run/react-router/compare/react-router@6.5.0...react-router@6.6.0)

## v6.5.0

Date: 2022-12-16

### 重要变更

本次发布引入了对[可选路由段](https://github.com/remix-run/react-router/issues/9546)的支持。现在，在任何路径段末尾添加 `?` 会使该整个段变为可选的。这对静态段和动态参数都适用。

**可选参数示例**

- `<Route path=":lang?/about>` 将匹配：
  - `/:lang/about`
  - `/about`
- `<Route path="/multistep/:widget1?/widget2?/widget3?">` 将匹配：
  - `/multistep`
  - `/multistep/:widget1`
  - `/multistep/:widget1/:widget2`
  - `/multistep/:widget1/:widget2/:widget3`

**可选静态段示例**

- `<Route path="/home?">` 将匹配：
  - `/`
  - `/home`
- `<Route path="/fr?/about">` 将匹配：
  - `/about`
  - `/fr/about`

### 次要变更

- 允许可选路由和可选静态段 ([#9650](https://github.com/remix-run/react-router/pull/9650))

### 补丁变更

- 停止对部分命名参数的错误匹配，即 `<Route path="prefix-:param">`，以与 splat 参数的行为保持一致。如果你之前依赖了这种行为，建议在 `useParams` 调用处提取路径的静态部分：([#9506](https://github.com/remix-run/react-router/pull/9506))

```jsx
// Old behavior at URL /prefix-123
<Route path="prefix-:id" element={<Comp /> }>

function Comp() {
  let params = useParams(); // { id: '123' }
  let id = params.id; // "123"
  ...
}

// New behavior at URL /prefix-123
<Route path=":id" element={<Comp /> }>

function Comp() {
  let params = useParams(); // { id: 'prefix-123' }
  let id = params.id.replace(/^prefix-/, ''); // "123"
  ...
}
```

- 在 SSR 文档 `action` 请求后，在 `loader` `request` 上保留 `headers` ([#9721](https://github.com/remix-run/react-router/pull/9721))
- 修复发送给重新验证 loader 的请求以确保它们反映 GET 请求 ([#9660](https://github.com/remix-run/react-router/pull/9660))
- 修复深层嵌套可选段的问题 ([#9727](https://github.com/remix-run/react-router/pull/9727))
- GET 表单现在在加载导航中暴露提交信息 ([#9695](https://github.com/remix-run/react-router/pull/9695))
- 修复多个错误冒泡到同一个边界时的错误边界追踪问题 ([#9702](https://github.com/remix-run/react-router/pull/9702))

**完整更新日志**: [`v6.4.5...v6.5.0`](https://github.com/remix-run/react-router/compare/react-router@6.4.5...react-router@6.5.0)

## v6.4.5

Date: 2022-12-07

### 补丁变更

- 修复发送给重新验证 loader 的请求以确保它们反映 `GET` 请求 ([#9680](https://github.com/remix-run/react-router/pull/9680))
- 移除 `instanceof Response` 检查，改用 `isResponse` ([#9690](https://github.com/remix-run/react-router/pull/9690))
- 修复 Cloudflare Pages 或其他非浏览器环境中的 `URL` 创建问题 ([#9682](https://github.com/remix-run/react-router/pull/9682), [#9689](https://github.com/remix-run/react-router/pull/9689))
- 为静态处理器的 `query`/`queryRoute` 添加 `requestContext` 支持 ([#9696](https://github.com/remix-run/react-router/pull/9696))
  - 注意：`queryRoute(path, routeId)` 的不稳定 API 已更改为 `queryRoute(path, { routeId, requestContext })`

**完整更新日志**: [`v6.4.4...v6.4.5`](https://github.com/remix-run/react-router/compare/react-router@6.4.4...react-router@6.4.5)

## v6.4.4

Date: 2022-11-30

### 补丁变更

- 如果 `action`/`loader` 函数返回 `undefined`，则抛出错误，因为重新验证需要知道 loader 是否已经执行过。`undefined` 还会在 SSR 序列化用于水合时引发问题。你应该始终确保 `loader`/`action` 返回一个值，如果不想返回任何内容可以返回 `null`。([#9511](https://github.com/remix-run/react-router/pull/9511))
- 正确处理到外部域名的重定向 ([#9590](https://github.com/remix-run/react-router/pull/9590), [#9654](https://github.com/remix-run/react-router/pull/9654))
- 在 307/308 重定向时保留 HTTP 方法 ([#9597](https://github.com/remix-run/react-router/pull/9597))
- 在静态数据路由器中支持 `basename` ([#9591](https://github.com/remix-run/react-router/pull/9591))
- 增强 `ErrorResponse` 身体内容，在内部 403/404/405 场景中包含更多描述性文本
- 修复 `NavLink` 和后代 `<Routes>` 中编码字符的问题 ([#9589](https://github.com/remix-run/react-router/pull/9589), [#9647](https://github.com/remix-run/react-router/pull/9647))
- 使用内置水合时正确序列化/反序列化 `ErrorResponse` 实例 ([#9593](https://github.com/remix-run/react-router/pull/9593))
- 在静态数据路由器中支持 `basename` ([#9591](https://github.com/remix-run/react-router/pull/9591))
- 更新依赖：
  - `@remix-run/router@1.0.4`
  - `react-router@6.4.4`

**完整更新日志**: [`v6.4.3...v6.4.4`](https://github.com/remix-run/react-router/compare/react-router-dom@6.4.3...react-router-dom@6.4.4)

## v6.4.3

Date: 2022-11-01

### 补丁变更

- 在使用 `createHashRouter` 时生成正确的 `<a href>` 值 ([#9409](https://github.com/remix-run/react-router/pull/9409))
- 更好地处理 URL 和路由路径中特殊字符的编码/匹配 ([#9477](https://github.com/remix-run/react-router/pull/9477), [#9496](https://github.com/remix-run/react-router/pull/9496))
- 当 `index` 路由还有 `path` 时生成正确的 `formAction` 路径名 ([#9486](https://github.com/remix-run/react-router/pull/9486))
- 在 `NavLink` 上尊重 `relative=path` prop ([#9453](https://github.com/remix-run/react-router/pull/9453))
- 修复根 URL 的 `NavLink` 行为 ([#9497](https://github.com/remix-run/react-router/pull/9497))
- 传递 `locationArg` 时 `useRoutes` 应能够返回 `null` ([#9485](https://github.com/remix-run/react-router/pull/9485))
- 修复 `createMemoryRouter` 中 `initialEntries` 的类型 ([#9498](https://github.com/remix-run/react-router/pull/9498))
- 在 `loader`/`action` 重定向中支持 `basename` 和相对路由 ([#9447](https://github.com/remix-run/react-router/pull/9447))
- 在查找正确的提交 `action` 函数时忽略无路径的布局路由 ([#9455](https://github.com/remix-run/react-router/pull/9455))
- 为 `@remix-run/router` 添加 UMD 构建 ([#9446](https://github.com/remix-run/react-router/pull/9446))
- 修复 Firefox 中本地文件执行时的 `createURL` 问题 ([#9464](https://github.com/remix-run/react-router/pull/9464))

**完整更新日志**: [`v6.4.2...v6.4.3`](https://github.com/remix-run/react-router/compare/react-router@6.4.2...react-router@6.4.3)

## v6.4.2

Date: 2022-10-06

### 补丁变更

- 在 `useFormAction` 中尊重 `basename` ([#9352](https://github.com/remix-run/react-router/pull/9352))
- 修复 `IndexRouteObject` 和 `NonIndexRouteObject` 类型，使 `hasErrorElement` 为可选 ([#9394](https://github.com/remix-run/react-router/pull/9394))
- 增强数据路由器 Hook 无效使用时的控制台错误信息 ([#9311](https://github.com/remix-run/react-router/pull/9311))
- 如果 index 路由有 children，会导致运行时错误。我们加强了 `RouteObject`/`RouteProps` 类型以在 TypeScript 中提前暴露此错误。([#9366](https://github.com/remix-run/react-router/pull/9366))

**完整更新日志**: [`v6.4.1...v6.4.2`](https://github.com/remix-run/react-router/compare/react-router@6.4.1...react-router@6.4.2)

## v6.4.1

Date: 2022-09-22

### 补丁变更

- 保留 `initialEntries` 中的 state ([#9288](https://github.com/remix-run/react-router/pull/9288))
- 保留 fetcher get 提交到 index 路由时的 `?index` ([#9312](https://github.com/remix-run/react-router/pull/9312))

**完整更新日志**: [`v6.4.0...v6.4.1`](https://github.com/remix-run/react-router/compare/react-router@6.4.0...react-router@6.4.1)

## v6.4.0

Date: 2022-09-13

### 重要变更

#### Remix 数据 API

哇，这是一个大版本！`6.4.0` 将所有数据加载和变更 API 从 Remix 移植过来。以下是快速概览，但建议你查看[文档](https://reactrouter.com/)，特别是[功能概览](https://reactrouter.com/en/6.4.0/start/overview)和[教程](https://reactrouter.com/en/6.4.0/start/tutorial)。

**新的 `react-router` API**

- 使用 `createMemoryRouter` 创建路由器
- 使用 `<RouterProvider>` 渲染路由器
- 使用路由 `loader` 加载数据，使用路由 `action` 进行变更
- 使用路由 `errorElement` 处理错误
- 使用 `defer` 和 `Await` 延迟加载非关键数据

**新的 `react-router-dom` API**

- 使用 `createBrowserRouter`/`createHashRouter` 创建路由器
- 使用新的 `<Form>` 组件提交数据
- 使用 `useFetcher()` 执行页内数据加载和变更
- 使用 `defer` 和 `Await` 延迟加载非关键数据
- 使用 `<ScrollRestoration>` 管理滚动位置
- 使用 `<Link relative="path">` 执行基于路径的相对导航 ([#9160](https://github.com/remix-run/react-router/pull/9160))

### 补丁变更

- 路径解析现在与尾部斜杠无关 ([#8861](https://github.com/remix-run/react-router/pull/8861))
- `useLocation` 在 `<Routes location>` 组件内返回作用域内的 location ([#9094](https://github.com/remix-run/react-router/pull/9094))
- 如果定义了 `<Link replace>` prop，则尊重该 prop ([#8779](https://github.com/remix-run/react-router/pull/8779))

**完整更新日志**: [`v6.3.0...v6.4.0`](https://github.com/remix-run/react-router/compare/v6.3.0...react-router%406.4.0)

## v6.3.0

Date: 2022-03-31

### 次要变更

- 添加了 v5 到 v6 的向后兼容包 💜 ([#8752](https://github.com/remix-run/react-router/pull/8752))。官方指南可以在[此讨论](https://github.com/remix-run/react-router/discussions/8753)中找到

**完整更新日志**: [`v6.2.2...v6.3.0`](https://github.com/remix-run/react-router/compare/v6.2.2...v6.3.0)

## v6.2.2

Date: 2022-02-28

### 补丁变更

- 修复了以特殊 URL 安全字符开头的嵌套 splat 路由 ([#8563](https://github.com/remix-run/react-router/pull/8563))
- 修复了某些情况下 index 路由缺少路由上下文的错误 ([#8497](https://github.com/remix-run/react-router/pull/8497))

**完整更新日志**: [`v6.2.1...v6.2.2`](https://github.com/remix-run/react-router/compare/v6.2.1...v6.2.2)

## v6.2.1

Date: 2021-12-17

### 补丁变更

- 本次发布更新了内部 `history` 依赖至 `5.2.0`。

**完整更新日志**: [`v6.2.0...v6.2.1`](https://github.com/remix-run/react-router/compare/v6.2.0...v6.2.1)

## v6.2.0

Date: 2021-12-17

### 次要变更

- 我们现在使用静态可分析的 CJS 导出。这允许在 Node ESM 脚本中使用命名导入（[查看提交](https://github.com/remix-run/react-router/commit/29c7fc8b5f853b0b06ecd0f5682a9bbe6eca0715)）。

### 补丁变更

- 修复了 `RouteProps` 的 `element` 类型，应为 `ReactNode` ([#8473](https://github.com/remix-run/react-router/pull/8473))
- 修复了顶层路由的 `useOutlet` 错误 ([#8483](https://github.com/remix-run/react-router/pull/8483))

**完整更新日志**: [`v6.1.1...v6.2.0`](https://github.com/remix-run/react-router/compare/v6.1.1...v6.2.0)

## v6.1.1

Date: 2021-12-11

### 补丁变更

- 在 v6.1.0 中我们无意中发布了一个新的、未记录的 API，这可能会引入错误 ([#7586](https://github.com/remix-run/react-router/pull/7586))。我们已将 `HistoryRouter` 标记为 `unstable_HistoryRouter`，因为此 API 在新的主版本发布前可能需要更改。

**完整更新日志**: [`v6.1.0...v6.1.1`](https://github.com/remix-run/react-router/compare/v6.1.0...v6.1.1)

## v6.1.0

Date: 2021-12-10

### 次要变更

- `<Outlet>` 现在可以接收 `context` prop。该值会传递给子路由，并可通过新的 `useOutletContext` Hook 访问。详见 [API 文档](https://reactrouter.com/docs/en/v6/api#useoutletcontext)。([#8461](https://github.com/remix-run/react-router/pull/8461))
- `<NavLink>` 现在可以接收子函数来访问其 props。([#8164](https://github.com/remix-run/react-router/pull/8164))
- 改进了 `useMatch` 和 `matchPath` 的 TypeScript 类型签名。例如，当你调用 `useMatch("foo/:bar/:baz")` 时，路径会被解析，返回类型将是 `PathMatch<"bar" | "baz">`。([#8030](https://github.com/remix-run/react-router/pull/8030))

### 补丁变更

- 修复了嵌套路由中 base64 编码 ID 支持的错误 ([#8291](https://github.com/remix-run/react-router/pull/8291))
- 一些错误信息的改进 ([#8202](https://github.com/remix-run/react-router/pull/8202))

**完整更新日志**: [`v6.0.2...v6.1.0`](https://github.com/remix-run/react-router/compare/v6.0.2...v6.1.0)

## v6.0.2

Date: 2021-11-09

### 补丁变更

- 为 `<Link>` 添加了 `reloadDocument` prop。这允许 `<Link>` 在导航后重新加载文档（像普通锚点标签一样），同时保持相对 `to` 解析 ([#8283](https://github.com/remix-run/react-router/pull/8283))

**完整更新日志**: [`v6.0.1...v6.0.2`](https://github.com/remix-run/react-router/compare/v6.0.1...v6.0.2)

## v6.0.1

Date: 2021-11-05

### 补丁变更

- 为 `<StaticRouter location>` 添加默认值 ([#8243](https://github.com/remix-run/react-router/pull/8243))
- 添加在 `<Routes>` 内使用 `<Route>` 的 invariant 检查以帮助用户进行迁移 ([#8238](https://github.com/remix-run/react-router/pull/8238))

**完整更新日志**: [`v6.0.0...v6.0.1`](https://github.com/remix-run/react-router/compare/v6.0.0...v6.0.1)

## v6.0.0

Date: 2021-11-03

React Router v6 来了！

请阅读[我们的博客文章了解 v6 中所有精彩内容](https://remix.run/blog/react-router-v6)，包括[从 React Router v5 升级的注意事项](https://remix.run/blog/react-router-v6#upgrading-to-react-router-v6)以及 Reach Router 的迁移指南。
