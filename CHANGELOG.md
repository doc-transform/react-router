<!-- markdownlint-disable no-duplicate-header no-emphasis-as-heading no-inline-html -->

# React Router 版本发布记录

本页面列出了 React Router 从 `v6.0.0` 至今的所有版本发布记录。v6 之前的版本请参阅 [Github Releases 页面](https://github.com/remix-run/react-router/releases)。

我们选择在此文件中管理版本发布记录，而非使用分页的 Github Releases 页面，原因有二：

- Github UI 的分页功能意味着你无法一次性搜索大量版本的发布说明
- 分页的 Github 界面在列表视图中也会截断较长的发布说明而不给出提示，你需要点击进入详情视图才能看到完整的发布说明

<details>
  <summary>目录</summary>

- [React Router 版本发布记录](#react-router-版本发布记录)
  - [v7.13.1](#v7131)
  - [What's Changed](#whats-changed)
    - [URL Masking (unstable)](#url-masking-unstable)
    - [补丁变更](#补丁变更)
    - [不稳定变更](#不稳定变更)
  - [v7.13.0](#v7130)
    - [次要变更](#次要变更)
    - [补丁变更](#补丁变更-1)
  - [v7.12.0](#v7120)
    - [安全通知](#安全通知)
    - [次要变更](#次要变更-1)
    - [补丁变更](#补丁变更-2)
    - [不稳定变更](#不稳定变更-1)
  - [v7.11.0](#v7110)
    - [重要变更](#重要变更)
      - [`vite preview` Support](#vite-preview-support)
      - [Stabilized Client-side `onError`](#stabilized-client-side-onerror)
      - [Call-site Revalidation Opt-out (unstable)](#call-site-revalidation-opt-out-unstable)
    - [次要变更](#次要变更-2)
    - [补丁变更](#补丁变更-3)
    - [不稳定变更](#不稳定变更-2)
  - [v7.10.1](#v7101)
    - [补丁变更](#补丁变更-4)
  - [v7.10.0](#v7100)
    - [重要变更](#重要变更-1)
      - [Stabilized `future.v8_splitRouteModules`](#stabilized-futurev8_splitroutemodules)
      - [Stabilized `future.v8_viteEnvironmentApi`](#stabilized-futurev8_viteenvironmentapi)
      - [Stabilized `fetcher.reset()`](#stabilized-fetcherreset)
      - [Stabilized `DataStrategyMatch.shouldCallHandler()`](#stabilized-datastrategymatchshouldcallhandler)
    - [次要变更](#次要变更-3)
    - [补丁变更](#补丁变更-5)
    - [不稳定变更](#不稳定变更-3)
  - [v7.9.6](#v796)
    - [安全通知](#安全通知-1)
    - [补丁变更](#补丁变更-6)
    - [不稳定变更](#不稳定变更-4)
  - [v7.9.5](#v795)
    - [重要变更](#重要变更-2)
      - [Instrumentation (unstable)](#instrumentation-unstable)
    - [补丁变更](#补丁变更-7)
    - [不稳定变更](#不稳定变更-5)
  - [v7.9.4](#v794)
    - [安全通知](#安全通知-2)
    - [重要变更](#重要变更-3)
      - [`useRoute()` (unstable)](#useroute-unstable)
    - [补丁变更](#补丁变更-8)
    - [不稳定变更](#不稳定变更-6)
  - [v7.9.3](#v793)
    - [补丁变更](#补丁变更-9)
  - [v7.9.2](#v792)
    - [重要变更](#重要变更-4)
      - [RSC Framework Mode (unstable)](#rsc-framework-mode-unstable)
      - [Fetcher Reset (unstable)](#fetcher-reset-unstable)
    - [补丁变更](#补丁变更-10)
    - [不稳定变更](#不稳定变更-7)
  - [v7.9.1](#v791)
    - [补丁变更](#补丁变更-11)
  - [v7.9.0](#v790)
    - [安全通知](#安全通知-3)
    - [重要变更](#重要变更-5)
      - [Stable Middleware and Context APIs](#stable-middleware-and-context-apis)
    - [次要变更](#次要变更-4)
    - [补丁变更](#补丁变更-12)
    - [不稳定变更](#不稳定变更-8)
  - [v7.8.2](#v782)
    - [补丁变更](#补丁变更-13)
    - [不稳定变更](#不稳定变更-9)
  - [v7.8.1](#v781)
    - [补丁变更](#补丁变更-14)
    - [不稳定变更](#不稳定变更-10)
  - [v7.8.0](#v780)
    - [重要变更](#重要变更-6)
      - [Consistently named `loaderData` values](#consistently-named-loaderdata-values)
      - [Improvements/fixes to the middleware APIs (unstable)](#improvementsfixes-to-the-middleware-apis-unstable)
    - [次要变更](#次要变更-5)
    - [补丁变更](#补丁变更-15)
    - [不稳定变更](#不稳定变更-11)
    - [按包分类的变更](#按包分类的变更)
  - [v7.7.1](#v771)
    - [补丁变更](#补丁变更-16)
    - [不稳定变更](#不稳定变更-12)
  - [v7.7.0](#v770)
    - [重要变更](#重要变更-7)
      - [Unstable RSC APIs](#unstable-rsc-apis)
    - [次要变更](#次要变更-6)
    - [补丁变更](#补丁变更-17)
    - [不稳定变更](#不稳定变更-13)
    - [按包分类的变更](#按包分类的变更-1)
  - [v7.6.3](#v763)
    - [补丁变更](#补丁变更-18)
  - [v7.6.2](#v762)
    - [补丁变更](#补丁变更-19)
  - [v7.6.1](#v761)
    - [补丁变更](#补丁变更-20)
    - [不稳定变更](#不稳定变更-14)
  - [v7.6.0](#v760)
    - [重要变更](#重要变更-8)
      - [`routeDiscovery` Config Option](#routediscovery-config-option)
      - [Automatic Types for Future Flags](#automatic-types-for-future-flags)
    - [次要变更](#次要变更-7)
    - [补丁变更](#补丁变更-21)
    - [不稳定变更](#不稳定变更-15)
    - [按包分类的变更](#按包分类的变更-2)
  - [v7.5.3](#v753)
    - [补丁变更](#补丁变更-22)
  - [v7.5.2](#v752)
    - [安全通知](#安全通知-4)
    - [补丁变更](#补丁变更-23)
  - [v7.5.1](#v751)
    - [补丁变更](#补丁变更-24)
    - [不稳定变更](#不稳定变更-16)
  - [v7.5.0](#v750)
    - [重要变更](#重要变更-9)
      - [`route.lazy` Object API](#routelazy-object-api)
    - [次要变更](#次要变更-8)
    - [补丁变更](#补丁变更-25)
    - [不稳定变更](#不稳定变更-17)
    - [按包分类的变更](#按包分类的变更-3)
  - [v7.4.1](#v741)
    - [安全通知](#安全通知-5)
    - [补丁变更](#补丁变更-26)
    - [不稳定变更](#不稳定变更-18)
  - [v7.4.0](#v740)
    - [次要变更](#次要变更-9)
    - [补丁变更](#补丁变更-27)
    - [不稳定变更](#不稳定变更-19)
    - [按包分类的变更](#按包分类的变更-4)
  - [v7.3.0](#v730)
    - [次要变更](#次要变更-10)
    - [补丁变更](#补丁变更-28)
    - [不稳定变更](#不稳定变更-20)
      - [Client-side `context` (unstable)](#client-side-context-unstable)
      - [Middleware (unstable)](#middleware-unstable)
        - [Middleware `context` parameter](#middleware-context-parameter)
      - [`unstable_SerializesTo`](#unstable_serializesto)
    - [按包分类的变更](#按包分类的变更-5)
  - [v7.2.0](#v720)
    - [重要变更](#重要变更-10)
      - [Type-safe `href` utility](#type-safe-href-utility)
      - [Prerendering with a SPA Fallback](#prerendering-with-a-spa-fallback)
      - [Allow a root `loader` in SPA Mode](#allow-a-root-loader-in-spa-mode)
    - [次要变更](#次要变更-11)
    - [补丁变更](#补丁变更-29)
    - [不稳定变更](#不稳定变更-21)
      - [Split Route Modules (unstable)](#split-route-modules-unstable)
    - [按包分类的变更](#按包分类的变更-6)
  - [v7.1.5](#v715)
    - [补丁变更](#补丁变更-30)
  - [v7.1.4](#v714)
    - [补丁变更](#补丁变更-31)
  - [v7.1.3](#v713)
    - [补丁变更](#补丁变更-32)
  - [v7.1.2](#v712)
    - [补丁变更](#补丁变更-33)
  - [v7.1.1](#v711)
    - [补丁变更](#补丁变更-34)
  - [v7.1.0](#v710)
    - [次要变更](#次要变更-12)
    - [补丁变更](#补丁变更-35)
    - [按包分类的变更](#按包分类的变更-7)
  - [v7.0.2](#v702)
    - [补丁变更](#补丁变更-36)
  - [v7.0.1](#v701)
    - [补丁变更](#补丁变更-37)
  - [v7.0.0](#v700)
    - [Breaking Changes](#breaking-changes)
      - [Package Restructuring](#package-restructuring)
      - [Removed Adapter Re-exports](#removed-adapter-re-exports)
      - [Removed APIs](#removed-apis)
      - [Minimum Versions](#minimum-versions)
      - [Adopted Future Flag Behaviors](#adopted-future-flag-behaviors)
      - [Vite Compiler](#vite-compiler)
      - [Exposed Router Promises](#exposed-router-promises)
    - [Other Notable Changes](#other-notable-changes)
      - [`routes.ts`](#routests)
      - [Type-safety improvements](#type-safety-improvements)
      - [Prerendering](#prerendering)
    - [Major Changes (`react-router`)](#major-changes-react-router)
    - [Major Changes (`@react-router/*`)](#major-changes-react-router-1)
    - [次要变更](#次要变更-13)
    - [补丁变更](#补丁变更-38)
    - [按包分类的变更](#按包分类的变更-8)
- [React Router v6 Releases](#react-router-v6-releases)
  - [v6.30.3](#v6303)
    - [安全通知](#安全通知-6)
    - [补丁变更](#补丁变更-39)
  - [v6.30.2](#v6302)
    - [安全通知](#安全通知-7)
    - [补丁变更](#补丁变更-40)
  - [v6.30.1](#v6301)
    - [补丁变更](#补丁变更-41)
  - [v6.30.0](#v6300)
    - [次要变更](#次要变更-14)
    - [补丁变更](#补丁变更-42)
  - [v6.29.0](#v6290)
    - [次要变更](#次要变更-15)
    - [补丁变更](#补丁变更-43)
  - [v6.28.2](#v6282)
    - [补丁变更](#补丁变更-44)
  - [v6.28.1](#v6281)
    - [补丁变更](#补丁变更-45)
  - [v6.28.0](#v6280)
    - [重要变更](#重要变更-11)
    - [次要变更](#次要变更-16)
    - [补丁变更](#补丁变更-46)
  - [v6.27.0](#v6270)
    - [重要变更](#重要变更-12)
      - [Stabilized APIs](#stabilized-apis)
    - [次要变更](#次要变更-17)
    - [补丁变更](#补丁变更-47)
  - [v6.26.2](#v6262)
    - [补丁变更](#补丁变更-48)
  - [v6.26.1](#v6261)
    - [补丁变更](#补丁变更-49)
  - [v6.26.0](#v6260)
    - [次要变更](#次要变更-18)
    - [补丁变更](#补丁变更-50)
  - [v6.25.1](#v6251)
    - [补丁变更](#补丁变更-51)
  - [v6.25.0](#v6250)
    - [重要变更](#重要变更-13)
      - [Stabilized `v7_skipActionErrorRevalidation`](#stabilized-v7_skipactionerrorrevalidation)
    - [次要变更](#次要变更-19)
    - [补丁变更](#补丁变更-52)
  - [v6.24.1](#v6241)
    - [补丁变更](#补丁变更-53)
  - [v6.24.0](#v6240)
    - [重要变更](#重要变更-14)
      - [Lazy Route Discovery (a.k.a. "Fog of War")](#lazy-route-discovery-aka-fog-of-war)
    - [次要变更](#次要变更-20)
    - [补丁变更](#补丁变更-54)
  - [v6.23.1](#v6231)
    - [补丁变更](#补丁变更-55)
  - [v6.23.0](#v6230)
    - [重要变更](#重要变更-15)
      - [Data Strategy (unstable)](#data-strategy-unstable)
      - [Skip Action Error Revalidation (unstable)](#skip-action-error-revalidation-unstable)
    - [次要变更](#次要变更-21)
  - [v6.22.3](#v6223)
    - [补丁变更](#补丁变更-56)
  - [v6.22.2](#v6222)
    - [补丁变更](#补丁变更-57)
  - [v6.22.1](#v6221)
    - [补丁变更](#补丁变更-58)
  - [v6.22.0](#v6220)
    - [重要变更](#重要变更-16)
      - [Core Web Vitals Technology Report Flag](#core-web-vitals-technology-report-flag)
    - [次要变更](#次要变更-22)
    - [补丁变更](#补丁变更-59)
  - [v6.21.3](#v6213)
    - [补丁变更](#补丁变更-60)
  - [v6.21.2](#v6212)
    - [补丁变更](#补丁变更-61)
  - [v6.21.1](#v6211)
    - [补丁变更](#补丁变更-62)
  - [v6.21.0](#v6210)
    - [重要变更](#重要变更-17)
      - [`future.v7_relativeSplatPath`](#futurev7_relativesplatpath)
      - [Partial Hydration](#partial-hydration)
    - [次要变更](#次要变更-23)
    - [补丁变更](#补丁变更-63)
  - [v6.20.1](#v6201)
    - [补丁变更](#补丁变更-64)
  - [v6.20.0](#v6200)
    - [次要变更](#次要变更-24)
    - [补丁变更](#补丁变更-65)
  - [v6.19.0](#v6190)
    - [重要变更](#重要变更-18)
      - [`unstable_flushSync` API](#unstable_flushsync-api)
    - [次要变更](#次要变更-25)
    - [补丁变更](#补丁变更-66)
  - [v6.18.0](#v6180)
    - [重要变更](#重要变更-19)
      - [New Fetcher APIs](#new-fetcher-apis)
      - [Persistence Future Flag (`future.v7_fetcherPersist`)](#persistence-future-flag-futurev7_fetcherpersist)
    - [次要变更](#次要变更-26)
    - [补丁变更](#补丁变更-67)
  - [v6.17.0](#v6170)
    - [重要变更](#重要变更-20)
      - [View Transitions 🚀](#view-transitions-)
    - [次要变更](#次要变更-27)
    - [补丁变更](#补丁变更-68)
  - [v6.16.0](#v6160)
    - [次要变更](#次要变更-28)
    - [补丁变更](#补丁变更-69)
  - [v6.15.0](#v6150)
    - [次要变更](#次要变更-29)
    - [补丁变更](#补丁变更-70)
  - [v6.14.2](#v6142)
    - [补丁变更](#补丁变更-71)
  - [v6.14.1](#v6141)
    - [补丁变更](#补丁变更-72)
  - [v6.14.0](#v6140)
    - [重要变更](#重要变更-21)
      - [JSON/Text Submissions](#jsontext-submissions)
    - [次要变更](#次要变更-30)
    - [补丁变更](#补丁变更-73)
  - [v6.13.0](#v6130)
    - [重要变更](#重要变更-22)
      - [`future.v7_startTransition`](#futurev7_starttransition)
    - [次要变更](#次要变更-31)
    - [补丁变更](#补丁变更-74)
  - [v6.12.1](#v6121)
    - [补丁变更](#补丁变更-75)
  - [v6.12.0](#v6120)
    - [重要变更](#重要变更-23)
      - [`React.startTransition` support](#reactstarttransition-support)
    - [次要变更](#次要变更-32)
    - [补丁变更](#补丁变更-76)
  - [v6.11.2](#v6112)
    - [补丁变更](#补丁变更-77)
  - [v6.11.1](#v6111)
    - [补丁变更](#补丁变更-78)
  - [v6.11.0](#v6110)
    - [次要变更](#次要变更-33)
    - [补丁变更](#补丁变更-79)
  - [v6.10.0](#v6100)
    - [重要变更](#重要变更-24)
    - [次要变更](#次要变更-34)
      - [`future.v7_normalizeFormMethod`](#futurev7_normalizeformmethod)
    - [补丁变更](#补丁变更-80)
  - [v6.9.0](#v690)
    - [重要变更](#重要变更-25)
      - [`Component`/`ErrorBoundary` route properties](#componenterrorboundary-route-properties)
      - [Introducing Lazy Route Modules](#introducing-lazy-route-modules)
    - [次要变更](#次要变更-35)
    - [补丁变更](#补丁变更-81)
  - [v6.8.2](#v682)
    - [补丁变更](#补丁变更-82)
  - [v6.8.1](#v681)
    - [补丁变更](#补丁变更-83)
  - [v6.8.0](#v680)
    - [次要变更](#次要变更-36)
    - [补丁变更](#补丁变更-84)
  - [v6.7.0](#v670)
    - [次要变更](#次要变更-37)
    - [补丁变更](#补丁变更-85)
  - [v6.6.2](#v662)
    - [补丁变更](#补丁变更-86)
  - [v6.6.1](#v661)
    - [补丁变更](#补丁变更-87)
  - [v6.6.0](#v660)
    - [重要变更](#重要变更-26)
    - [次要变更](#次要变更-38)
    - [补丁变更](#补丁变更-88)
  - [v6.5.0](#v650)
    - [重要变更](#重要变更-27)
    - [次要变更](#次要变更-39)
    - [补丁变更](#补丁变更-89)
  - [v6.4.5](#v645)
    - [补丁变更](#补丁变更-90)
  - [v6.4.4](#v644)
    - [补丁变更](#补丁变更-91)
  - [v6.4.3](#v643)
    - [补丁变更](#补丁变更-92)
  - [v6.4.2](#v642)
    - [补丁变更](#补丁变更-93)
  - [v6.4.1](#v641)
    - [补丁变更](#补丁变更-94)
  - [v6.4.0](#v640)
    - [重要变更](#重要变更-28)
      - [Remix Data APIs](#remix-data-apis)
    - [补丁变更](#补丁变更-95)
  - [v6.3.0](#v630)
    - [次要变更](#次要变更-40)
  - [v6.2.2](#v622)
    - [补丁变更](#补丁变更-96)
  - [v6.2.1](#v621)
    - [补丁变更](#补丁变更-97)
  - [v6.2.0](#v620)
    - [次要变更](#次要变更-41)
    - [补丁变更](#补丁变更-98)
  - [v6.1.1](#v611)
    - [补丁变更](#补丁变更-99)
  - [v6.1.0](#v610)
    - [次要变更](#次要变更-42)
    - [补丁变更](#补丁变更-100)
  - [v6.0.2](#v602)
    - [补丁变更](#补丁变更-101)
  - [v6.0.1](#v601)
    - [补丁变更](#补丁变更-102)
  - [v6.0.0](#v600)

</details>

<!-- 添加新版本时，请复制以下模板：

## v7.X.Y

日期：YYYY-MM-DD

### 重要变更

#### 重大新特性 1

#### 重大新特性 2

### 次要变更

### 补丁变更

### 不稳定变更

⚠️  _[Unstable features](https://reactrouter.com/community/api-development-strategy#unstable-flags) are not recommended for production use_

**完整更新日志**: [`v7.X.Y...v7.X.Y`](https://github.com/remix-run/react-router/compare/react-router@7.X.Y...react-router@7.X.Y)
-->

## v7.13.1

日期：2026-02-23

## 重要变更

### URL 遮罩（不稳定）

本次发布包含一个新的 `<Link unstable_mask>` API，为 Framework/Data 模式带来了一流的 URL 遮罩支持（[RFC](https://github.com/remix-run/react-router/discussions/9864)）。这允许你实现与声明式模式中通过[手动 `backgroundLocation` 管理](https://github.com/remix-run/react-router/tree/main/examples/modal)相同类型的 UI。该示例已使用新 API 转换为 Data 模式，[点此查看](https://github.com/remix-run/react-router/tree/main/examples/modal-data-router)。

### 补丁变更

- `react-router` - `turbo-stream` 编码完成时清除超时 ([#14810](https://github.com/remix-run/react-router/pull/14810))
- `react-router` - 改进 `Origin` header 无效时的错误消息 ([#14743](https://github.com/remix-run/react-router/pull/14743))
- `react-router` - 修复 `matchPath` 在没有 `"/"` 分隔符时对可选参数的错误匹配。 ([#14689](https://github.com/remix-run/react-router/pull/14689))
  - `matchPath("/users/:id?", "/usersblah")` 现在返回 null
  - `matchPath("/test_route/:part?", "/test_route_more")` 现在返回 null.
- `react-router` - 修复初始懒加载路由发现期间带有匹配的 splat 路由时 `HydrateFallback` 的渲染问题 ([#14740](https://github.com/remix-run/react-router/pull/14740))
- `react-router` - 在 manifest 版本不匹配重新加载时保留查询参数和 hash ([#14813](https://github.com/remix-run/react-router/pull/14813))

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

- `react-router` - RSC：修复错误代码路径中导致无效路由树比较的空引用异常 ([#14780](https://github.com/remix-run/react-router/pull/14780))
- `react-router` - RSC：新增 `unstable_getRequest` API ([#14758](https://github.com/remix-run/react-router/pull/14758))
- `react-router` - RSC：将失败的 origin 检查更新为返回 400 状态和适当的 UI，而非通用的 500 ([#14755](https://github.com/remix-run/react-router/pull/14755))
- `react-router` - 在 Framework/Data 模式中新增 `<Link unstable_mask>` 支持，允许用户在路由器中导航到某个 URL 但「遮罩」浏览器中显示的 URL ([#14716](https://github.com/remix-run/react-router/pull/14716))
  - 这对于上下文路由用法很有用，例如在画廊上方的模态框中显示图片，但在浏览器中显示直接指向图片的 URL，可以在没有背景画廊的情况下共享和加载
  - 遮罩后的 location（如果存在）将在 `useLocation().unstable_mask` 上可用，以便你检测当前是否处于遮罩状态
  - 遮罩 URL 仅适用于 SPA 场景，在 SSR 期间将从 `history.state` 中移除
  - 这提供了一个一流的 API，用于在 Framework/Data 模式中遮罩 URL，以实现与声明式模式中通过[手动 `backgroundLocation` 管理](https://github.com/remix-run/react-router/tree/main/examples/modal).

    ```tsx
    // routes/gallery.tsx
    export function clientLoader({ request }: Route.LoaderArgs) {
      let sp = new URL(request.url).searchParams;
      return {
        images: getImages(),
        // 当路由器 location 包含 image 参数时，加载模态框数据
        modalImage: sp.has("image") ? getImage(sp.get("image")!) : null,
      };
    }

    export default function Gallery({ loaderData }: Route.ComponentProps) {
      return (
        <>
          <GalleryGrid>
            {loaderData.images.map((image) => (
              <Link
                key={image.id}
                {/* 将路由器导航到 /gallery?image=N */}}
                to={`/gallery?image=${image.id}`}
                {/* 但在 URL 栏中显示 /images/N */}}
                unstable_mask={`/images/${image.id}`}
              >
                <img src={image.url} alt={image.alt} />
              </Link>
            ))}
          </GalleryGrid>

          {/* 当模态框数据存在时，显示模态框 */}
          {data.modalImage ? (
            <dialog open>
              <img src={data.modalImage.url} alt={data.modalImage.alt} />
            </dialog>
          ) : null}
        </>
      );
    }
    ```

**完整更新日志**: [`v7.13.0...v7.13.1`](https://github.com/remix-run/react-router/compare/react-router@7.13.0...react-router@7.13.1)

## v7.13.0

日期：2026-01-23

### 次要变更

- `react-router` - 为 `Links` 组件新增 `crossOrigin` prop ([#14687](https://github.com/remix-run/react-router/pull/14687))

### 补丁变更

- `react-router` - 修复带有冒号的 `useNavigate` 路径的双斜杠规范化问题 ([#14718](https://github.com/remix-run/react-router/pull/14718))
- `react-router` - 修复内联 `criticalCss` 缺少 `nonce` 的问题 ([#14691](https://github.com/remix-run/react-router/pull/14691))
- `react-router` - 更新失败的 origin 检查，返回 400 状态而非 500 ([#14737](https://github.com/remix-run/react-router/pull/14737))
- `react-router` - 放宽 `allowedActionOrigins` 的 glob 检查，使 `**` 匹配所有域名 ([#14722](https://github.com/remix-run/react-router/pull/14722))
- `@react-router/dev` - 升级 `@remix-run/node-fetch-server` 依赖 ([#14704](https://github.com/remix-run/react-router/pull/14704))
- `@react-router/fs-routes` - 修复路由目录在应用目录之外时的路由文件路径问题 ([#13937](https://github.com/remix-run/react-router/pull/13937))

**完整更新日志**: [`v7.12.0...v7.13.0`](https://github.com/remix-run/react-router/compare/react-router@7.12.0...react-router@7.13.0)

## v7.12.0

日期：2026-01-07

### 安全通知

本次发布修复了 3 个安全漏洞：

- [React Router Action/Server Action 请求处理中的 CSRF](https://github.com/remix-run/react-router/security/advisories/GHSA-h5cw-625j-3rxh)
- [通过开放重定向的 XSS](https://github.com/remix-run/react-router/security/advisories/GHSA-2w69-qvjg-hvjx)
- [ScrollRestoration 中的 React Router SSR XSS](https://github.com/remix-run/react-router/security/advisories/GHSA-8v8x-cx79-35w7)

### 次要变更

- `react-router` - 新增额外的 CSRF 保护层，拒绝来自外部源的 UI 路由提交 ([#14708](https://github.com/remix-run/react-router/pull/14708))
  - 如果你需要允许特定外部源的访问，可以在 `react-router.config.ts` 中使用新的 `allowedActionOrigins` 配置字段来指定外部源

### 补丁变更

- `react-router` - 修复 `generatePath` 与后缀参数（如 `/books/:id.json`）一起使用时的问题 ([#14269](https://github.com/remix-run/react-router/pull/14269))
- `react-router` - 转义滚动恢复键中的 HTML ([#14705](https://github.com/remix-run/react-router/pull/14705))
- `react-router` - 验证重定向位置 ([#14706](https://github.com/remix-run/react-router/pull/14706))
- `@react-router/dev` - 修复当 HMR 针对循环导入代码触发时出现的 `Maximum call stack size exceeded` 错误 ([#14522](https://github.com/remix-run/react-router/pull/14522))
- `@react-router/dev` - 在 SPA 模式的 `vite preview` 服务器中跳过 SSR 中间件 ([#14673](https://github.com/remix-run/react-router/pull/14673))

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

- `react-router` - 使用 `<HydratedRouter unstable_instrumentations>` 时保留 `clientLoader.hydrate=true` ([#14674](https://github.com/remix-run/react-router/pull/14674))
- `react-router` - 使用 `future.unstable_subResourceIntegrity` 时将 `<Scripts nonce>` 值传递到底层的 `importmap` `script` 标签 ([#14675](https://github.com/remix-run/react-router/pull/14675))
- `react-router` - 与 `UNSAFE_createBrowserHistory` 一起导出 `UNSAFE_createMemoryHistory` 和 `UNSAFE_createHashHistory` 以保持一致性 ([#14663](https://github.com/remix-run/react-router/pull/14663))
  - 这些不是为新应用设计的，而是为了帮助使用 `unstable_HistoryRouter` 的应用从 v6 迁移到 v7，以便采用更新的 API
- `@react-router/dev` - 新增 `future.unstable_trailingSlashAwareDataRequests` 标志，在浏览器 URL 中存在尾部斜杠时，为文档和数据请求中 `middleware`、`loader` 和 `action` 函数内的 `request.pathname` 提供一致的行为。 ([#14644](https://github.com/remix-run/react-router/pull/14644))
  - 目前，你的 HTTP 和 `request` 路径名如下所示 for `/a/b/c` and `/a/b/c/`

    | URL `/a/b/c` | **HTTP pathname** | **`request` pathname`** |
    | ------------ | ----------------- | ----------------------- |
    | **Document** | `/a/b/c`          | `/a/b/c` ✅             |
    | **Data**     | `/a/b/c.data`     | `/a/b/c` ✅             |

    | URL `/a/b/c/` | **HTTP pathname** | **`request` pathname`** |
    | ------------- | ----------------- | ----------------------- |
    | **Document**  | `/a/b/c/`         | `/a/b/c/` ✅            |
    | **Data**      | `/a/b/c.data`     | `/a/b/c` ⚠️             |

  - 启用此标志后，这些路径名将通过客户端 `.data` 请求的新 `_.data` 格式保持一致：

    | URL `/a/b/c` | **HTTP pathname** | **`request` pathname`** |
    | ------------ | ----------------- | ----------------------- |
    | **Document** | `/a/b/c`          | `/a/b/c` ✅             |
    | **Data**     | `/a/b/c.data`     | `/a/b/c` ✅             |

    | URL `/a/b/c/` | **HTTP pathname**  | **`request` pathname`** |
    | ------------- | ------------------ | ----------------------- |
    | **Document**  | `/a/b/c/`          | `/a/b/c/` ✅            |
    | **Data**      | `/a/b/c/_.data` ⬅️ | `/a/b/c/` ✅            |

  - 这是一个 bug 修复，但我们将其放在选择性启用标志后面，因为如果你的其他应用或缓存逻辑依赖于 URL 格式，这可能是一个"破坏性的 bug 修复"
  - 启用此标志还会将导航到 `/` 时客户端 `.data` 请求的格式从 `/_root.data` 更改为 `/_.data` 以与新格式对齐 - 这不影响 `request` 路径名，在所有情况下仍然是 `/`

**完整更新日志**: [`v7.11.0...v7.12.0`](https://github.com/remix-run/react-router/compare/react-router@7.11.0...react-router@7.12.0)

## v7.11.0

日期：2025-12-17

### 重要变更

我们新增了 `vite preview` 支持并稳定化了客户端 `onError` API - 如果你已在之前的版本中采用了 `unstable_onError` API，请进行相应更改。

#### `vite preview` 支持

我们在 Framework 模式中新增了 [`vite preview`](https://vite.dev/guide/cli#vite-preview) 支持，方便你预览生产构建。

#### 稳定化客户端 `onError`

现有的 `<RouterProvider unstable_onError>`/`<HydratedRouter unstable_onError>` API 已稳定化为 `<RouterProvider onError>`/`<HydratedRouter onError>`。更多信息请参阅[错误报告](https://reactrouter.com/7.11.0/how-to/error-reporting#client-errors)文档。

#### 调用点重新验证退出（不稳定）

我们通过新的 `unstable_defaultShouldRevalidate` 标志（[RFC](https://github.com/remix-run/react-router/discussions/10006)）新增了调用点重新验证退出的初始不稳定支持。此标志可用于所有导航/fetcher 提交 API 以更改标准重新验证行为。如果任何路由包含 `shouldRevalidate` 函数，标志值将传递给该函数，以便路由对重新验证行为有最终决定权。

```tsx
<Form method="post" unstable_defaultShouldRevalidate={false} />
submit(data, { method: "post", unstable_defaultShouldRevalidate: false })
<fetcher.Form method="post" unstable_defaultShouldRevalidate={false} />
fetcher.submit(data, { method: "post", unstable_defaultShouldRevalidate: false })
```

This flag is also available on non-submission navigational use cases - for example, you may want to opt-out of revalidation when adding a search param that doesn't impact the UI:

```tsx
<Link to="?analytics-param=1" unstable_defaultShouldRevalidate={false} />;
navigate("?analytics-param=1", { unstable_defaultShouldRevalidate: false });
setSearchParams(params, { unstable_defaultShouldRevalidate: false });
```

### 次要变更

- `react-router` - Stabilize `<HydratedRouter onError>`/`<RouterProvider onError>` ([#14546](https://github.com/remix-run/react-router/pull/14546))
- `@react-router/dev` - 新增`vite preview` support ([#14507](https://github.com/remix-run/react-router/pull/14507))

### 补丁变更

- `react-router` - 修复 `<Router>` 组件上的 `unstable_useTransitions` prop 以允许省略从而保持向后兼容 ([#14646](https://github.com/remix-run/react-router/pull/14646))
- `react-router` - 允许从客户端中间件返回重定向 ([#14598](https://github.com/remix-run/react-router/pull/14598))
- `react-router` - 处理返回不完整结果集的 `dataStrategy` 实现，为没有可用结果的路由添加错误 ([#14627](https://github.com/remix-run/react-router/pull/14627))
- `@react-router/serve` - 更新 `compression` 和 `morgan` 依赖以修复 `on-headers` CVE：[GHSA-76c9-3jph-rj3q](https://github.com/advisories/GHSA-76c9-3jph-rj3q) ([#14652](https://github.com/remix-run/react-router/pull/14652))

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

- `react-router` - RSC：支持在服务器组件渲染阶段抛出 `data()` 和 Response ([#14632](https://github.com/remix-run/react-router/pull/14632))
  - 响应体不会被序列化，因为在错误编码阶段不允许异步工作。
  - 如果你希望将数据传输到错误边界，请改用抛出 `data()`
- `react-router` - RSC：支持在渲染时抛出 `redirect` Response ([#14596](https://github.com/remix-run/react-router/pull/14596))
- `react-router` - RSC：`routeRSCServerRequest` 将 `fetchServer` 替换为 `serverResponse` ([#14597](https://github.com/remix-run/react-router/pull/14597))
- `@react-router/dev` - RSC（Framework 模式）：手动分块 `react` 和 `react-router` 依赖 ([#14655](https://github.com/remix-run/react-router/pull/14655))
- `@react-router/dev` - RSC（Framework 模式）：如果项目 `package.json` 中存在 `react-server-dom-webpack` 则进行优化 ([#14656](https://github.com/remix-run/react-router/pull/14656))
- `@react-router/{dev,serve}` - RSC（Framework 模式）：支持自定义入口点 ([#14643](https://github.com/remix-run/react-router/pull/14643))
- `react-router` - 为各种 API 新增 `unstable_defaultShouldRevalidate` 标志以允许退出标准重新验证行为 ([#14542](https://github.com/remix-run/react-router/pull/14542))

**完整更新日志**: [`v7.10.1...v7.11.0`](https://github.com/remix-run/react-router/compare/react-router@7.10.1...react-router@7.11.0)

## v7.10.1

日期：2025-12-04

### 补丁变更

- `react-router` - 更新我们为 React 18 用户提供的 `useOptimistic` 桩以使用稳定的 setter 函数，避免潜在的 `useEffect` 循环 - 特别是在使用 `<Link viewTransition>` 时 ([#14628](https://github.com/remix-run/react-router/pull/14628))
- `@react-router/dev` - 使用动态 `import()` 导入 ESM 包 `pkg-types` 以修复 Node 20.18 上的问题 ([#14624](https://github.com/remix-run/react-router/pull/14624))
- `@react-router/dev` - 更新 `valibot` 依赖至 `^1.2.0` 以修复 [GHSA-vqpr-j7v3-hqw9](https://github.com/advisories/GHSA-vqpr-j7v3-hqw9) ([#14608](https://github.com/remix-run/react-router/pull/14608))

**完整更新日志**: [`v7.10.0...v7.10.1`](https://github.com/remix-run/react-router/compare/react-router@7.10.0...react-router@7.10.1)

## v7.10.0

日期：2025-12-02

### 重要变更

我们在本次发布中稳定化了一些现有 API 和 future 标志，如果你已在不稳定状态下采用了这些 API，请进行相应更改！

#### 稳定化 `future.v8_splitRouteModules`

现有的 `future.unstable_splitRouteModules` 标志已在 `react-router.config.ts` 中稳定化为 `future.v8_splitRouteModules`。请参阅[文档](https://reactrouter.com/7.10.0/upgrading/future#futurev8_splitroutemodules)了解更多关于采用此标志的信息。

#### 稳定化 `future.v8_viteEnvironmentApi`

现有的 `future.unstable_viteEnvironmentApi` 标志已在 `react-router.config.ts` 中稳定化为 `future.v8_viteEnvironmentApi`。请参阅[文档](https://reactrouter.com/7.10.0/upgrading/future#futurev8_viteenvironmentapi)了解更多关于采用此标志的信息。

#### 稳定化 `fetcher.reset()`

现有的 `fetcher.unstable_reset()` API 已稳定化为 `fetcher.reset()`。

#### 稳定化 `DataStrategyMatch.shouldCallHandler()`

现有的底层 `DataStrategyMatch.unstable_shouldCallHandler()`/`DataStrategyMatch.unstable_shouldRevalidateArgs` API 已稳定化为 `DataStrategyMatch.shouldCallHandler()`/`DataStrategyMatch.shouldRevalidateArgs`。请参阅[文档](https://reactrouter.com/7.10.0/how-to/data-strategy)了解如何使用自定义 `dataStrategy` 以及如何从已弃用的 `DataStrategyMatch.shouldLoad` API 迁移。

### 次要变更

- `react-router` - 稳定化 `fetcher.reset()` ([#14545](https://github.com/remix-run/react-router/pull/14545))
  - ⚠️ 如果你已开始使用 `fetcher.unstable_reset()`，这是一个破坏性变更 - 请更新你的代码以使用 `fetcher.reset()`
- `react-router` - 稳定化 `dataStrategy` 的 `match.shouldCallHandler()`/`match.shouldRevalidateArgs` API ([#14592](https://github.com/remix-run/react-router/pull/14592))
  - `match.shouldLoad` API 现已标记为弃用，推荐使用这些更强大的替代方案
  - ⚠️ 如果你已开始使用 `match.unstable_shouldCallHandler()`/`match.unstable_shouldRevalidateArgs`，这是一个破坏性变更 - 请更新你的代码以使用 `match.shouldCallHandler()`/`match.shouldRevalidateArgs`
- `@react-router/dev` - 稳定化 `future.v8_splitRouteModules`，替换 `future.unstable_splitRouteModules` ([#14595](https://github.com/remix-run/react-router/pull/14595))
  - ⚠️ 如果你已开始使用 `future.unstable_splitRouteModules`，这是一个破坏性变更 - 请更新你的 `react-router.config.ts`
- `@react-router/dev` - 稳定化 `future.v8_viteEnvironmentApi`，替换 `future.unstable_viteEnvironmentApi` ([#14595](https://github.com/remix-run/react-router/pull/14595))
  - ⚠️ 如果你已开始使用 `future.unstable_viteEnvironmentApi`，这是一个破坏性变更 - 请更新你的 `react-router.config.ts`

### 补丁变更

- `react-router` - 修复 Framework 模式中的一个 bug，`action` 返回 4xx/5xx 响应后 `shouldRevalidate` 的 `defaultShouldRevalidate` 参数不正确（应该是 `false` 时却是 `true`） ([#14592](https://github.com/remix-run/react-router/pull/14592))
  - 如果你的 `shouldRevalidate` 函数依赖该参数，你可能会看到意外的重新验证
- `react-router` - 修复 `fetcher.submit` 在包含 `tagName` 属性的普通对象上失败的问题 ([#14534](https://github.com/remix-run/react-router/pull/14534))
- `react-router` - 修复 Framework/Data 模式中 `useNavigate` 返回的 promise，使其正确跟踪 `popstate` 导航（即 `navigate(-1)`）的持续时间 ([#14524](https://github.com/remix-run/react-router/pull/14524))
- `react-router` - 从路由处理器抛出 `data()` 时在 `ErrorResponse` 实例上保留 `statusText` ([#14555](https://github.com/remix-run/react-router/pull/14555))
- `react-router` - 优化 `href()` 以避免在 splat 上的回溯正则表达式 ([#14329](https://github.com/remix-run/react-router/pull/14329))
- `@react-router/dev` - 修复禁用 `skipLibCheck` 时出现的 `useRoute` 类型内部类型错误 ([#14577](https://github.com/remix-run/react-router/pull/14577))
- `@react-router/dev` - 在执行 `routes.ts` 之前加载环境变量 ([#14446](https://github.com/remix-run/react-router/pull/14446))
  - 例如，你现在可以基于 [`VITE_` 前缀的环境变量](https://vite.dev/guide/env-and-mode#env-variables)来计算路由

    ```ts
    // app/routes.ts
    import { type RouteConfig, route } from "@react-router/dev/routes";

    const routes: RouteConfig = [];

    // 仅在设置了 VITE_ENV_ROUTE 时添加路由
    if (import.meta.env.VITE_ENV_ROUTE === "my-route") {
      routes.push(route("my-route", "routes/my-route.tsx"));
    }

    export default routes;
    ```

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

- `react-router` - 为客户端 `unstable_onError` 的参数新增 `unstable_pattern` ([#14573](https://github.com/remix-run/react-router/pull/14573))
- `react-router` - 重构 `RouterProvider` 内部调用 `unstable_onError` 的方式以避免潜在的严格模式问题 ([#14573](https://github.com/remix-run/react-router/pull/14573))
- `react-router` - 为路由器新增 `unstable_useTransitions` 标志，让用户控制 [`React.startTransition`](https://react.dev/reference/react/startTransition) 和 [`React.useOptimistic`](https://react.dev/reference/react/useOptimistic) 的使用 ([#14524](https://github.com/remix-run/react-router/pull/14524))
  - 更多信息请参阅[文档](https://reactrouter.com/7.10.0/explanation/react-transitions)
  - Framework 模式 + Data 模式：
    - `<HydratedRouter unstable_transition>`/`<RouterProvider unstable_transition>`
    - 未设置时（当前默认行为）
      - 路由器状态更新被包装在 `React.startTransition` 中
      - ⚠️ 如果你将自己的导航/fetcher 包装在 `React.startTransition` 中，这可能会导致有问题的行为
      - 如果遇到这种情况，你应该将标志设置为 `true` 以获得增强的 `useOptimistic` 行为（需要 React 19）
    - 设置为 `true` 时
      - 路由器状态更新仍然被包装在 `React.startTransition` 中（与未设置标志时相同）
      - `Link`/`Form` 导航将被包装在 `React.startTransition` 中
        - 如果你希望退出导航的外层 `React.startTransition` 调用，可以使用 `useNavigate`/`useSubmit`
      - 路由器状态信息的子集将在导航_期间_通过 `React.useOptimistic` 呈现到 UI（即 `useNavigation()`、`useFetchers()` 等）
        - ⚠️ 这是 React 19 的 API，因此在 Framework/Data 模式中使用此标志也需要 React 19
    - 设置为 `false` 时
      - 路由器不会在任何导航或状态变更中使用 `React.startTransition` 或 `React.useOptimistic`
  - 声明式模式
    - `<BrowserRouter unstable_useTransitions>`
    - 未设置时
      - 路由器状态更新被包装在 `React.startTransition` 中
    - 设置为 `true` 时
      - 路由器状态更新仍然被包装在 `React.startTransition` 中（与未设置标志时相同）
      - `Link`/`Form` 导航将被包装在 `React.startTransition` 中
    - 设置为 `false` 时
      - 路由器不会在任何导航或状态变更中使用 `React.startTransition`

**完整更新日志**: [`v7.9.6...v7.10.0`](https://github.com/remix-run/react-router/compare/react-router@7.9.6...react-router@7.10.0)

## v7.9.6

日期：2025-11-13

### 安全通知

本次发布修复了 1 个安全漏洞：

- [通过不可信路径的意外外部重定向](https://github.com/remix-run/react-router/security/advisories/GHSA-9jcx-v3wj-wh4m)

### 补丁变更

- `react-router` - 正确处理 fetcher 提交中 `next()` 之前祖先抛出的中间件错误 ([#14517](https://github.com/remix-run/react-router/pull/14517))
- `react-router` - 修复 splat 路由干扰多次调用 `patchRoutesOnNavigation` 的问题 ([#14487](https://github.com/remix-run/react-router/pull/14487))
- `react-router` - 规范化 `resolvePath` 中的双斜杠 ([#14529](https://github.com/remix-run/react-router/pull/14529))
- `@react-router/dev` - 使用动态 `import()` 加载纯 ESM 的 `p-map` 依赖以避免 Node 20.18 及以下版本的问题 ([#14492](https://github.com/remix-run/react-router/pull/14492))
- `@react-router/dev` - 在默认的 `entry.server.tsx` 中调用 `renderToPipeableStream` 之前短路 `HEAD` 文档请求，以更好地符合 [规范](https://httpwg.org/specs/rfc9110.html#HEAD) ([#14488](https://github.com/remix-run/react-router/pull/14488))

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

- `react-router` - 为客户端 `unstable_onError` 新增 `location`/`params` 参数以支持增强的错误报告 ([#14509](https://github.com/remix-run/react-router/pull/14509))
  - ⚠️ 如果你已经采用了 `unstable_onError`，这是一个破坏性变更
  - 第二个参数已更改为包含 `errorInfo`、`location` 和 `params` 的对象：

    ```tsx
    // <RouterProvider unstable_onError={errorHandler} />
    // <HydratedRouter unstable_onError={errorHandler} />

    // Before
    function errorHandler(error: unknown, errorInfo?: React.errorInfo) {
      /*...*/
    }

    // After
    function errorHandler(
      error: unknown,
      info: {
        location: Location;
        params: Params;
        errorInfo?: React.ErrorInfo;
      },
    ) {
      /*...*/
    }
    ```

**完整更新日志**: [`v7.9.5...v7.9.6`](https://github.com/remix-run/react-router/compare/react-router@7.9.5...react-router@7.9.6)

## v7.9.5

日期：2025-10-29

### 重要变更

#### 监测工具（不稳定）

本次发布新增了 `unstable_instrumentation` API，允许你为应用的各个方面添加运行时监测逻辑（服务器处理器、客户端导航/请求、loader、action、中间件、`route.lazy`）。更多信息请参阅[文档](https://reactrouter.com/7.9.5/how-to/instrumentation)。

### 补丁变更

- `react-router` - 确保即使没有 loader，带有中间件的路由也能运行 action 处理器 ([#14443](https://github.com/remix-run/react-router/pull/14443))
- `@react-router/dev` - 确保路由导航不会移除动态导入使用的 CSS `link` 元素 ([#14463](https://github.com/remix-run/react-router/pull/14463))
- `@react-router/dev` - 类型生成：仅为应用目录内的路由注册路由模块类型 ([#14439](https://github.com/remix-run/react-router/pull/14439))

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

- `react-router` - 将 `unstable_RSCHydratedRouter` 和工具函数移动到 `react-router/dom` 导出 ([#14457](https://github.com/remix-run/react-router/pull/14457))
- `react-router` - 为 `unstable_useRoute()` 新增类型安全的 `handle` 字段 ([#14462](https://github.com/remix-run/react-router/pull/14462))

  For example:

  ```ts
  // app/routes/admin.tsx
  const handle = { hello: "world" };
  ```

  ```ts
  // app/routes/some-other-route.tsx
  export default function Component() {
    const admin = useRoute("routes/admin");
    if (!admin) throw new Error("Not nested within 'routes/admin'");
    console.log(admin.handle);
    //                ^? { hello: string }
  }
  ```

- `react-router` - 新增 `unstable_instrumentations` API，允许用户通过监测路由 loader、action、中间件、lazy 以及服务器端请求处理器和客户端导航/请求来为应用添加可观测性 ([#14412](https://github.com/remix-run/react-router/pull/14412))
  - Framework Mode:
    - `entry.server.tsx`: `export const unstable_instrumentations = [...]`
    - `entry.client.tsx`: `<HydratedRouter unstable_instrumentations={[...]} />`
  - Data Mode
    - `createBrowserRouter(routes, { unstable_instrumentations: [...] })`
- `react-router` - 为 loader/action/中间件新增 `unstable_pattern` 参数，包含未插值的路由模式（即 `/blog/:slug`），对于在监测代码中按路由聚合日志/指标很有用 ([#14412](https://github.com/remix-run/react-router/pull/14412))
- `@react-router/dev` - 引入 `prerender.unstable_concurrency` 选项，支持并发运行预渲染，可能加速构建 ([#14380](https://github.com/remix-run/react-router/pull/14380))

**完整更新日志**: [`v7.9.4...v7.9.5`](https://github.com/remix-run/react-router/compare/react-router@7.9.4...react-router@7.9.5)

## v7.9.4

日期：2025-10-08

### 安全通知

本次发布修复了 1 个安全漏洞：

- [使用 `createFileSessionStorage()` 配合未签名 cookie 时的未授权文件访问](https://github.com/remix-run/react-router/security/advisories/GHSA-9583-h5hc-x8cw)

### 重要变更

#### `useRoute()`（不稳定）

本次发布包含一个新的 `unstable_useRoute()` hook，提供了一种类型安全的方式来访问 Framework 模式中特定路由的 `loaderData`/`actionData`。可以将其视为 `useRouteLoaderData` 的改进版本，支持类型生成系统并且也支持 `actionData`。更多信息请查看下面的变更日志条目。

### 补丁变更

- `@react-router/dev` - 更新 `valibot` 依赖至 `^1.1.0` ([#14379](https://github.com/remix-run/react-router/pull/14379))
- `@react-router/node` - 验证 `createFileSessionStorage` 中传入的会话 ID 格式 ([#14426](https://github.com/remix-run/react-router/pull/14426))

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

- `react-router` - 处理来自服务器 action 的外部重定向 ([#14400](https://github.com/remix-run/react-router/pull/14400))
- `react-router` - 新的（不稳定）`useRoute` hook，用于从特定路由访问数据 ([#14407](https://github.com/remix-run/react-router/pull/14407))

  For example, let's say you have an `admin` route somewhere in your app and you want any child routes of `admin` to all have access to the `loaderData` and `actionData` from `admin.`

  ```tsx
  // app/routes/admin.tsx
  import { Outlet } from "react-router";

  export const loader = () => ({ message: "Hello, loader!" });

  export const action = () => ({ count: 1 });

  export default function Component() {
    return (
      <div>
        {/* ... */}
        <Outlet />
        {/* ... */}
      </div>
    );
  }
  ```

  You might even want to create a reusable widget that all of the routes nested under `admin` could use:

  ```tsx
  import { unstable_useRoute as useRoute } from "react-router";

  export function AdminWidget() {
    // How to get `message` and `count` from `admin` route?
  }
  ```

  In framework mode, `useRoute` knows all your app's routes and gives you TS errors when invalid route IDs are passed in:

  ```tsx
  export function AdminWidget() {
    const admin = useRoute("routes/dmin");
    //                      ^^^^^^^^^^^
  }
  ```

  `useRoute` returns `undefined` if the route is not part of the current page:

  ```tsx
  export function AdminWidget() {
    const admin = useRoute("routes/admin");
    if (!admin) {
      throw new Error(`AdminWidget used outside of "routes/admin"`);
    }
  }
  ```

  Note: the `root` route is the exception since it is guaranteed to be part of the current page.
  As a result, `useRoute` never returns `undefined` for `root`.

  `loaderData` and `actionData` are marked as optional since they could be accessed before the `action` is triggered or after the `loader` threw an error:

  ```tsx
  export function AdminWidget() {
    const admin = useRoute("routes/admin");
    if (!admin) {
      throw new Error(`AdminWidget used outside of "routes/admin"`);
    }
    const { loaderData, actionData } = admin;
    console.log(loaderData);
    //          ^? { message: string } | undefined
    console.log(actionData);
    //          ^? { count: number } | undefined
  }
  ```

  If instead of a specific route, you wanted access to the _current_ route's `loaderData` and `actionData`, you can call `useRoute` without arguments:

  ```tsx
  export function AdminWidget() {
    const currentRoute = useRoute();
    currentRoute.loaderData;
    currentRoute.actionData;
  }
  ```

  This usage is equivalent to calling `useLoaderData` and `useActionData`, but consolidates all route data access into one hook: `useRoute`.

  Note: when calling `useRoute()` (without a route ID), TS has no way to know which route is the current route.
  As a result, `loaderData` and `actionData` are typed as `unknown`.
  If you want more type-safety, you can either narrow the type yourself with something like `zod` or you can refactor your app to pass down typed props to your `AdminWidget`:

  ```tsx
  export function AdminWidget({
    message,
    count,
  }: {
    message: string;
    count: number;
  }) {
    /* ... */
  }
  ```

**完整更新日志**: [`v7.9.3...v7.9.4`](https://github.com/remix-run/react-router/compare/react-router@7.9.3...react-router@7.9.4)

## v7.9.3

日期：2025-09-26

### 补丁变更

- `react-router` - 修复 Data 模式回归问题，当存在 `middleware` 但没有任何 `loader` 函数时，初始加载会导致 404 ([#14393](https://github.com/remix-run/react-router/pull/14393))
- `react-router` - 不要尝试使用 `turbo-stream` 解码从未到达服务器的 CDN 错误 ([#14385](https://github.com/remix-run/react-router/pull/14385))
  - 这是我们在 Remix v2 中使用的逻辑，在采用 Single Fetch 时丢失了
  - 这允许实际的 CDN 错误冒泡到 `ErrorBoundary`，而不是通用的 _"Unable to decode turbo-stream response"_ 错误

**完整更新日志**: [`v7.9.2...v7.9.3`](https://github.com/remix-run/react-router/compare/react-router@7.9.2...react-router@7.9.3)

## v7.9.2

日期：2025-09-24

### 重要变更

本次发布包含一些 bug 修复，但我们认为你最期待的是新的不稳定特性 😉.

#### RSC Framework 模式（不稳定）

本次发布包含我们在 Framework 模式中首次发布的 RSC 不稳定支持！你可以在我们的[博客文章](https://remix.run/blog/rsc-framework-mode-preview)和[文档](https://reactrouter.com/how-to/react-server-components#rsc-framework-mode)中了解更多。

#### Fetcher 重置（不稳定）

本次发布还包含一个新的（长期以来被请求的）`fetcher.unstable_reset()` API，用于将 fetcher 重置回初始 `idle` 状态。

### 补丁变更

- `react-router` - 确保客户端路由器在初始化数据加载期间（如果需要）即使没有 loader 也运行客户端 `middleware` ([#14348](https://github.com/remix-run/react-router/pull/14348))
- `react-router` - 修复通过 `createRoutesFromElements` 与数据路由器一起使用时 `<Route>` 不支持 `middleware` prop 的问题 ([#14357](https://github.com/remix-run/react-router/pull/14357))
- `react-router` - 更新 `createRoutesStub` 以支持 `middleware` ([#14348](https://github.com/remix-run/react-router/pull/14348))
  - 你需要设置 `<RoutesStub future={{ v8_middleware: true }} />` 标志以启用正确的 `context` 类型
- `react-router` - 更新懒加载路由发现的 manifest 请求，使用单个逗号分隔的 `paths` 查询参数替代重复的 `p` 查询参数 ([#14321](https://github.com/remix-run/react-router/pull/14321))
  - 这是因为 Cloudflare 在用作缓存键时对 URL 搜索参数键/值对有 100 个的硬性限制
  - 如果包含超过 100 个路径，缓存键将不完整，可能产生误报的缓存命中
- `react-router` - 如果 `sessionStorage` 访问被阻止，在 manifest 版本不匹配逻辑中优雅失败 ([#14335](https://github.com/remix-run/react-router/pull/14335))
- `react-router` - 更新 `useOutlet` 返回的元素，使其在路由变更之间具有稳定的标识 ([#13382](https://github.com/remix-run/react-router/pull/13382))
- `react-router` - 处理祖先 splat 路由中编码的问号和 hash 字符 ([#14249](https://github.com/remix-run/react-router/pull/14249))
- `@react-router/dev` - 将内部 vite 插件的 Response 逻辑切换为使用 `@remix-run/node-fetch-server` ([#13927](https://github.com/remix-run/react-router/pull/13927))
- `@react-router/dev` - 修复配置解析期间 `presets` `future` 标志被忽略的问题 ([#14369](https://github.com/remix-run/react-router/pull/14369))

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

- `react-router` - 新增 `fetcher.unstable_reset()` API ([#14206](https://github.com/remix-run/react-router/pull/14206))
- `react-router` - 在 RSC Data 模式中，处理 SSR 的客户端错误并在浏览器中重试 ([#14342](https://github.com/remix-run/react-router/pull/14342))
- `react-router` - 为 RSC 路由器启用完整的 transition 支持 ([#14362](https://github.com/remix-run/react-router/pull/14362))
- `@react-router/dev` - 新增 RSC Framework 模式的不稳定支持 ([#14336](https://github.com/remix-run/react-router/pull/14336))
- `@react-router/serve` - 在 RSC Framework 模式中禁用 `compression()` 中间件 ([#14381](https://github.com/remix-run/react-router/pull/14381))

**完整更新日志**: [`v7.9.1...v7.9.2`](https://github.com/remix-run/react-router/compare/react-router@7.9.1...react-router@7.9.2)

## v7.9.1

日期：2025-09-12

### 补丁变更

- 修复内部 `Future` 接口命名 `middleware` -> `v8_middleware` ([#14327](https://github.com/remix-run/react-router/pull/14327))

**完整更新日志**: [`v7.9.0...v7.9.1`](https://github.com/remix-run/react-router/compare/react-router@7.9.0...react-router@7.9.1)

## v7.9.0

日期：2025-09-12

### 安全通知

本次发布修复了 1 个安全漏洞：

- [通过 Meta 组件生成 script:ld+json 标签时的 XSS](https://github.com/remix-run/react-router/security/advisories/GHSA-3cgp-3xvw-98x8)

### 重要变更

#### 稳定的中间件和上下文 API

我们已从以下 API 中移除 `unstable_` 前缀，它们现在被认为是稳定的并可用于生产环境：

- [`RouterContextProvider`](https://reactrouter.com/api/utils/RouterContextProvider)
- [`createContext`](https://reactrouter.com/api/utils/createContext)
- `createBrowserRouter` [`getContext`](https://reactrouter.com/api/data-routers/createBrowserRouter#optsgetcontext) option
- `<HydratedRouter>` [`getContext`](https://reactrouter.com/api/framework-routers/HydratedRouter#getcontext) prop

更多信息请参阅[中间件文档](https://reactrouter.com/how-to/middleware)、[中间件 RFC](https://github.com/remix-run/remix/discussions/7642) 和[客户端上下文 RFC](https://github.com/remix-run/react-router/discussions/9856)。

### 次要变更

- 稳定化中间件和上下文 API ([#14215](https://github.com/remix-run/react-router/pull/14215))

### 补丁变更

- `react-router` - 更新 `href()` 以正确处理参数后有扩展名或是单个可选参数的路由 ([#13797](https://github.com/remix-run/react-router/pull/13797))
- `react-router` - 转义 `meta()` JSON-LD 内容中的 HTML ([#14316](https://github.com/remix-run/react-router/pull/14316))

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

- `react-router` - RSC：添加 react-server 的 `Await` 组件实现 ([#14261](https://github.com/remix-run/react-router/pull/14261))
- `react-router` - RSC：修复在 Data 模式中使用 RSC 和自定义 basename 时仅有客户端 loader 的路由的 hydration 错误 ([#14264](https://github.com/remix-run/react-router/pull/14264))
- `react-router` - RSC：使 `href` 函数在 `react-server` 上下文中可用 ([#14262](https://github.com/remix-run/react-router/pull/14262))
- `react-router` - RSC：每次调用 `getPayload()` 时进行解码，以允许"上下文内"解码和上下文资源提升 ([#14248](https://github.com/remix-run/react-router/pull/14248))

**完整更新日志**: [`v7.8.2...v7.9.0`](https://github.com/remix-run/react-router/compare/react-router@7.8.2...react-router@7.9.0)

## v7.8.2

日期：2025-08-22

### 补丁变更

- `react-router` - 在服务器响应数据中保持 `ReadonlyMap` 和 `ReadonlySet` 类型。 ([#13092](https://github.com/remix-run/react-router/pull/13092))
- `react-router` - 修复数据路由器中不带前导斜杠的 `basename` 使用问题 ([#11671](https://github.com/remix-run/react-router/pull/11671))
- `react-router` - 修复在没有部分匹配时从 `patchRoutesOnNavigation` 抛出时的 `TypeError` ([#14198](https://github.com/remix-run/react-router/pull/14198))
- `react-router` - 正确转义 `generatePath()` 中的插值参数值 ([#13530](https://github.com/remix-run/react-router/pull/13530))
- `@react-router/dev` - 修复默认 `entry.server` 中的潜在内存泄漏 ([#14200](https://github.com/remix-run/react-router/pull/14200))

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

**客户端 `onError`**

- `react-router` - 为客户端错误报告新增 `<RouterProvider unstable_onError>`/`<HydratedRouter unstable_onError>` prop ([#14162](https://github.com/remix-run/react-router/pull/14162))

**中间件**

- `react-router` - 延迟 `.data` 重定向到 202 响应的序列化，直到中间件链之后 ([#14205](https://github.com/remix-run/react-router/pull/14205))
- `react-router` - 更新客户端中间件使其沿链返回 `dataStrategy` 结果，允许更高级的后处理中间件 ([#14151](https://github.com/remix-run/react-router/pull/14151), [#14212](https://github.com/remix-run/react-router/pull/14212))
- `react-router` - 从 `createBrowserRouter` 中移除 Data 模式的 `future.unstable_middleware` 标志 ([#14213](https://github.com/remix-run/react-router/pull/14213))
  - 这仅作为 Framework 模式标志需要，因为路由模块和 `getLoadContext` 类型行为的变更
  - 在 Data 模式中，这是一个可选特性，因为它只是路由对象上的一个新属性，不需要标志来触发行为变更

**RSC**

- `react-router` - 允许通过隐藏的 `$SKIP_REVALIDATION` 输入退出服务器 action 的重新验证 ([#14154](https://github.com/remix-run/react-router/pull/14154))

**完整更新日志**: [`v7.8.1...v7.8.2`](https://github.com/remix-run/react-router/compare/react-router@7.8.1...react-router@7.8.2)

## v7.8.1

日期：2025-08-15

### 补丁变更

- `react-router` - 修复使用绝对路径定义的嵌套路由中可选路径段的使用问题 ([#14135](https://github.com/remix-run/react-router/pull/14135))
- `react-router` - 修复 `matchPath` 中可选静态段的匹配问题 ([#11813](https://github.com/remix-run/react-router/pull/11813))
- `react-router` - 修复设置 `basename` 且 `ssr:false` 时的预渲染问题 ([#13791](https://github.com/remix-run/react-router/pull/13791))
- `react-router` - 在资源路由和中间件中正确地将返回/抛出的 `data()` 值通过 `Response.json()` 转换为 `Response` 实例 ([#14159](https://github.com/remix-run/react-router/pull/14159), [#14181](https://github.com/remix-run/react-router/pull/14181))
- `@react-router/dev` - 更新生成的 `Route.MetaArgs` 类型，使 `loaderData` 仅在存在 `ErrorBoundary` 导出时可能为 undefined ([#14173](https://github.com/remix-run/react-router/pull/14173))

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

**中间件**

- `react-router` - 将客户端 `next` 之前的中间件错误冒泡到需要加载的最浅祖先，而非严格的带有 loader 的最浅祖先 ([#14150](https://github.com/remix-run/react-router/pull/14150))
- `react-router` - 在文档/数据请求中将从中间件抛出的非重定向 `Response` 值传播到错误边界 ([#14182](https://github.com/remix-run/react-router/pull/14182))

**RSC**

- `react-router` - 在 `react-server` 环境中提供 `isRouteErrorResponse` 工具函数 ([#14166](https://github.com/remix-run/react-router/pull/14166))
- `react-router` - 在 RSC Data 模式中处理 `meta` 和 `links` 路由导出 ([#14136](https://github.com/remix-run/react-router/pull/14136))

**完整更新日志**: [`v7.8.0...v7.8.1`](https://github.com/remix-run/react-router/compare/react-router@7.8.0...react-router@7.8.1)

## v7.8.0

日期：2025-08-07

### 重要变更

#### 统一命名的 `loaderData` 值

你有没有注意到框架提供给你的 loader 数据值在命名上的不一致？比如我们在组件 props 中叫它 `loaderData`，但在 match 中却叫 `match.data`？是的，我们也注意到了——以及一些敏锐的 React Router 用户在提案中提出了这个问题。我们在一些遗留位置在现有 `data` 字段旁新增了 `loaderData` 字段，以与新 `Route.*` API 中使用的 `loaderData` 命名保持一致。

#### 中间件 API 的改进/修复（不稳定）

`7.8.0` 中最大的变更集中在 `unstable_middleware` API 上，我们正在接近将其稳定化。如果你已采用中间件 API 进行早期测试，请仔细阅读下面的中间件变更。我们希望尽快稳定这些 API，请就其当前状态向我们提供任何反馈！

### 次要变更

- `react-router` - 为 `Links` 和 `PrefetchPageLinks` 新增 `nonce` prop ([#14048](https://github.com/remix-run/react-router/pull/14048))
- `react-router` - 在现有 `data` 参数/属性旁新增 `loaderData` 参数/属性，以在全局范围内提供 `loaderData` 和 `actionData` 之间的一致性和清晰度 ([#14047](https://github.com/remix-run/react-router/pull/14047))
  - 更新的类型：`Route.MetaArgs`、`Route.MetaMatch`、`MetaArgs`、`MetaMatch`、`Route.ComponentProps.matches`、`UIMatch`
  - 现有 `data` 属性已添加 `@deprecated` 警告，引导用户使用新的 `loaderData` 属性，为在未来主要版本中移除 `data` 属性做准备

### 补丁变更

- `react-router` - 防止在 `fetcher.submit` 重新验证期间导航时出现 _"Did not find corresponding fetcher result"_ 控制台错误 ([#14114](https://github.com/remix-run/react-router/pull/14114))
- `react-router` - 将懒加载路由发现的 manifest URL 生成切换为使用独立的 `URLSearchParams` 实例而非 `URL.searchParams`，以避免 Chrome 中的重大性能瓶颈 ([#14084](https://github.com/remix-run/react-router/pull/14084))
- `react-router` - 调整 RSC 内部对 `React.use` 的使用以避免使用 React 18 时的 Webpack 编译错误 ([#14113](https://github.com/remix-run/react-router/pull/14113))
- `react-router` - 移除 TypeScript 声明文件中对 `@types/node` 的依赖 ([#14059](https://github.com/remix-run/react-router/pull/14059))
- `react-router` - 修复 `UIMatch` 的类型以反映 `loaderData`/`data` 属性可能为 `undefined` ([#12206](https://github.com/remix-run/react-router/pull/12206))
  - 当渲染 `ErrorBoundary` 时，并非所有活跃的 match 都会有可用的 loader 数据，因为可能是它们的 `loader` 抛出触发了错误边界
  - `UIMatch.data` 类型没有正确处理这种情况，总是反映数据存在，导致渲染 `ErrorBoundary` 时出现意外的运行时错误
  - ⚠️ This may cause some type errors to show up in your code for unguarded `match.data` accesses - you should properly guard for `undefined` values in those scenarios.

    ```tsx
    // app/root.tsx
    export function loader() {
      someFunctionThatThrows(); // ❌ Throws an Error
      return { title: "My Title" };
    }

    export function Layout({ children }: { children: React.ReactNode }) {
      let matches = useMatches();
      let rootMatch = matches[0] as UIMatch<Awaited<ReturnType<typeof loader>>>;
      //  ^ rootMatch.data is currently incorrectly typed here, so TypeScript does
      //    not complain if you do the following which throws an error at runtime:
      let { title } = rootMatch.data; // 💥

      return <html>...</html>;
    }
    ```

- `@react-router/dev` - 修复 Vite 插件中没有 mkdir 的重命名问题 ([#14105](https://github.com/remix-run/react-router/pull/14105))

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

**RSC**

- `react-router` - 修复 Data 模式中从 `shouldRevalidate` 返回 `false` 的路由会被 `<Outlet />` 替换的问题 ([#14071](https://github.com/remix-run/react-router/pull/14071))
- `react-router` - 为文档和 `callServer` 请求代理服务器 action 副作用重定向 ([#14131](https://github.com/remix-run/react-router/pull/14131))

**中间件**

- `react-router` - 更改 `RouterProvider`、`HydratedRouter` 和 `unstable_RSCHydratedRouter` 上的 `unstable_getContext` 签名，使其返回 `unstable_RouterContextProvider` 实例而非内部用于构造实例的 `Map` ([#14097](https://github.com/remix-run/react-router/pull/14097))
  - 更多信息请参阅[文档](https://reactrouter.com/api/data-routers/createBrowserRouter#optsunstable_getcontext)
  - ⚠️ 如果你已采用 `unstable_getContext` prop，这是一个破坏性变更
- `react-router` - 即使没有 loader，也在客户端导航时运行客户端中间件 ([#14106](https://github.com/remix-run/react-router/pull/14106))
- `react-router` - 将内部中间件实现转换为使用新的 `unstable_generateMiddlewareResponse` API ([#14103](https://github.com/remix-run/react-router/pull/14103))
- `react-router` - 确保启用中间件时资源路由错误通过 `handleError` 处理 ([#14078](https://github.com/remix-run/react-router/pull/14078))
- `react-router` - 如果未调用 `next`，传播服务器中间件返回的 `Response` ([#14093](https://github.com/remix-run/react-router/pull/14093))
- `react-router` - 允许服务器中间件返回 `data()` 值，这些值将被转换为 `Response` ([#14093](https://github.com/remix-run/react-router/pull/14093), [#14128](https://github.com/remix-run/react-router/pull/14128))
- `react-router` - 更新中间件错误处理，使 `next` 函数不再抛出异常，而是在适当的 `ErrorBoundary` 处处理中间件错误，并通过祖先 `next` 函数向上返回 `Response` ([#14118](https://github.com/remix-run/react-router/pull/14118))
  - 更多信息请参阅[错误处理文档](https://reactrouter.com/how-to/middleware#next-and-error-handling)
  - ⚠️ 这改变了现有功能，如果你当前将 `next` 调用包装在 `try`/`catch` 中，应该可以移除这些
- `react-router` - 将 `next` 之前的客户端中间件错误冒泡到适当的祖先错误边界 ([#14138](https://github.com/remix-run/react-router/pull/14138))
- `react-router` - 启用中间件时，将 `context` 参数设为只读（`Readonly<unstable_RouterContextProvider>`），以便 TypeScript 不允许你在 loader、action 或中间件中向其写入任意字段。 ([#14097](https://github.com/remix-run/react-router/pull/14097))
- `react-router` - 重命名并修改 `staticHandler.query`/`staticHandler.queryRoute` 中 `unstable_respond` API 的签名/功能 ([#14103](https://github.com/remix-run/react-router/pull/14103))
  - 这仅影响使用 `createStaticHandler()` 进行非 Framework 模式 SSR 手动数据加载的用户
  - 该 API 已重命名为 `unstable_generateMiddlewareResponse` 以提高清晰度
  - 主要功能变更是：不再在调用 `unstable_respond` 之前运行 loader/action 并将结果交给你，而是将 `query`/`queryRoute` 函数作为参数传递，你在回调中执行 loader/action，从而拥有完整的预处理和错误处理访问权限
  - The `query` version of the API now has a signature of `(query: (r: Request) => Promise<StaticHandlerContext | Response>) => Promise<Response>`
  - The `queryRoute` version of the API now has a signature of `(queryRoute: (r: Request) => Promise<Response>) => Promise<Response>`
  - 这允许更高级的用法，如在调用 `query` 前/后运行逻辑以及直接处理从 query 抛出的错误
  - ⚠️ 如果你已采用 `staticHandler` 的 `unstable_respond` API，这是一个破坏性变更

    ```tsx
    let response = await staticHandler.query(request, {
      requestContext: new unstable_RouterContextProvider(),
      async unstable_generateMiddlewareResponse(query) {
        try {
          // 此时我们已自上而下运行了中间件，需要调用
          // 处理器并生成 Response 以冒泡回中间件
          let result = await query(request);
          if (isResponse(result)) {
            return result; // 重定向等
          }
          return await generateHtmlResponse(result);
        } catch (error: unknown) {
          return generateErrorResponse(error);
        }
      },
    });
    ```

- `@react-router/{architect,cloudflare,express,node}` - 启用 `future.unstable_middleware` 时更改 `getLoadContext` 签名（`type GetLoadContextFunction`），使其返回 `unstable_RouterContextProvider` 实例而非内部用于构造实例的 `Map` ([#14097](https://github.com/remix-run/react-router/pull/14097))
  - 这也移除了 `type unstable_InitialContext` 导出
  - 更多信息请参阅[中间件 `getLoadContext` 文档](https://reactrouter.com/how-to/middleware#changes-to-getloadcontextapploadcontext)
  - ⚠️ 如果你已采用中间件并使用带 `getLoadContext` 函数的自定义服务器，这是一个破坏性变更

### 按包分类的变更

- [`create-react-router`](https://github.com/remix-run/react-router/blob/react-router%407.8.0/packages/create-react-router/CHANGELOG.md#780)
- [`react-router`](https://github.com/remix-run/react-router/blob/react-router%407.8.0/packages/react-router/CHANGELOG.md#780)
- [`@react-router/architect`](https://github.com/remix-run/react-router/blob/react-router%407.8.0/packages/react-router-architect/CHANGELOG.md#780)
- [`@react-router/cloudflare`](https://github.com/remix-run/react-router/blob/react-router%407.8.0/packages/react-router-cloudflare/CHANGELOG.md#780)
- [`@react-router/dev`](https://github.com/remix-run/react-router/blob/react-router%407.8.0/packages/react-router-dev/CHANGELOG.md#780)
- [`@react-router/express`](https://github.com/remix-run/react-router/blob/react-router%407.8.0/packages/react-router-express/CHANGELOG.md#780)
- [`@react-router/fs-routes`](https://github.com/remix-run/react-router/blob/react-router%407.8.0/packages/react-router-fs-routes/CHANGELOG.md#780)
- [`@react-router/node`](https://github.com/remix-run/react-router/blob/react-router%407.8.0/packages/react-router-node/CHANGELOG.md#780)
- [`@react-router/remix-config-routes-adapter`](https://github.com/remix-run/react-router/blob/react-router%407.8.0/packages/react-router-remix-config-routes-adapter/CHANGELOG.md#780)
- [`@react-router/serve`](https://github.com/remix-run/react-router/blob/react-router%407.8.0/packages/react-router-serve/CHANGELOG.md#780)

**完整更新日志**: [`v7.7.1...v7.8.0`](https://github.com/remix-run/react-router/compare/react-router@7.7.1...react-router@7.8.0)

## v7.7.1

日期：2025-07-24

### 补丁变更

- `@react-router/dev` - 运行 `react-router reveal --no-typescript` 时更新为 Prettier v3 进行格式化 ([#14049](https://github.com/remix-run/react-router/pull/14049))

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

- `react-router` - RSC Data 模式：修复当 `shouldRevalidate` 返回 `false` 时有错误的路由未被强制重新验证的 bug ([#14026](https://github.com/remix-run/react-router/pull/14026))
- `react-router` - RSC Data 模式：修复渲染错误边界时出现的 `Matched leaf route at location "/..." does not have an element or Component` 警告 ([#14021](https://github.com/remix-run/react-router/pull/14021))

**完整更新日志**: [`v7.7.0...v7.7.1`](https://github.com/remix-run/react-router/compare/react-router@7.7.0...react-router@7.7.1)

## v7.7.0

日期：2025-07-16

### 重要变更

#### 不稳定的 RSC API

我们很高兴通过以下新 API 引入 Data 模式中 RSC 的实验性支持：

- [`unstable_RSCHydratedRouter`](https://reactrouter.com/api/rsc/RSCHydratedRouter)
- [`unstable_RSCStaticRouter`](https://reactrouter.com/api/rsc/RSCStaticRouter)
- [`unstable_createCallServer`](https://reactrouter.com/api/rsc/createCallServer)
- [`unstable_getRSCStream`](https://reactrouter.com/api/rsc/getRSCStream)
- [`unstable_matchRSCServerRequest`](https://reactrouter.com/api/rsc/matchRSCServerRequest)
- [`unstable_routeRSCServerRequest`](https://reactrouter.com/api/rsc/routeRSCServerRequest)

更多信息请查看[博客文章](https://remix.run/blog/react-router-and-react-server-components)和 [RSC 文档](https://reactrouter.com/how-to/react-server-components)。

### 次要变更

- `create-react-router` - 新增 Deno 作为受支持和可检测的包管理器。注意此检测仅适用于 Deno 2.0.5 及以上版本。如果你使用较旧版本的 Deno，必须指定 --package-manager CLI 标志为 `deno`。 ([#12327](https://github.com/remix-run/react-router/pull/12327))
- `@react-router/remix-config-routes-adapter` - 与 `DefineRoutesFunction` 一起导出 `DefineRouteFunction` 类型 ([#13945](https://github.com/remix-run/react-router/pull/13945))

### 补丁变更

- `react-router` - 验证 cookie 签名时处理 `InvalidCharacterError` ([#13847](https://github.com/remix-run/react-router/pull/13847))
- `react-router` - 向 `setSearchParams` 回调函数传递 `searchParams` 的副本以避免内部 `searchParams` 实例的变异 ([#12784](https://github.com/remix-run/react-router/pull/12784))
  - 如果你在导航被阻止时变异当前有状态的 `searchParams`，会导致 bug，因为内部实例会与 `useLocation().search` 不同步
- `react-router` - 在 `turbo-stream` v2 fork 中支持无效的 `Date` ([#13684](https://github.com/remix-run/react-router/pull/13684))
- `react-router` - 在 Framework 模式中，初始渲染后在开发环境中清除关键 CSS ([#13872](https://github.com/remix-run/react-router/pull/13872), [#13995](https://github.com/remix-run/react-router/pull/13995))
- `react-router` - 为 fetcher 调用从 `patchRoutesOnNavigation` 的 `path` 参数中剥离搜索参数 ([#13911](https://github.com/remix-run/react-router/pull/13911))
- `react-router` - 跳过 `useRevalidator()` 调用的滚动恢复，因为它们不是新位置 ([#13671](https://github.com/remix-run/react-router/pull/13671))
- `react-router` - 在 `ssr` 设为 `false` 的预渲染配置中支持未编码的 UTF-8 路由 ([#13699](https://github.com/remix-run/react-router/pull/13699))
- `react-router` - 如果 URL hash 不是有效的 URI 组件则不抛出异常 ([#13247](https://github.com/remix-run/react-router/pull/13247))
- `react-router` - 从 Single Fetch 响应中移除 `Content-Length` header ([#13902](https://github.com/remix-run/react-router/pull/13902))
- `react-router` - 修复中间件特性引入的 `createRoutesStub` 回归问题 ([#13946](https://github.com/remix-run/react-router/pull/13946))
  - 作为该工作的一部分，我们修改了签名以与新的中间件 API 对齐，但没有使其与之前的 `AppLoadContext` API 向后兼容
  - 这允许 `createRoutesStub` 在你选择使用中间件和更新的 `context` 类型时工作，但对尚未选择中间件的用户来说 `createRoutesStub` 出现了问题
  - 我们已恢复此更改并以两类用户都能使用的方式重新实现
  - ⚠️ 如果你已采用不稳定的中间件特性并使用更新的 API 调用 `createRoutesStub`，这可能是一个破坏性的 bug 修复。

    ```tsx
    // 如果你没有选择使用中间件，旧 API 应该再次可用
    let context: AppLoadContext = {
      /*...*/
    };
    let Stub = createRoutesStub(routes, context);

    // 如果你选择了中间件，现在应该传递一个实例化的
    // `unstable_routerContextProvider` 而非 `getContext` 工厂函数。
    let context = new unstable_RouterContextProvider();
    context.set(SomeContext, someValue);
    let Stub = createRoutesStub(routes, context);
    ```

- `@react-router/dev` - 更新 `vite-node` 至 `^3.2.2` 以支持 Vite 7 ([#13781](https://github.com/remix-run/react-router/pull/13781))
- `@react-router/dev` - 正确处理开发模式中的 `https` 协议 ([#13746](https://github.com/remix-run/react-router/pull/13746))
- `@react-router/dev` - 修复禁用 Vite 的 `build.cssCodeSplit` 选项时样式缺失的问题 ([#13943](https://github.com/remix-run/react-router/pull/13943))
- `@react-router/dev` - 允许路由配置文件使用 `.mts` 和 `.mjs` 扩展名 ([#13931](https://github.com/remix-run/react-router/pull/13931))
- `@react-router/dev` - 修复 `cwd` 与项目根目录不同时的预渲染文件位置 ([#13824](https://github.com/remix-run/react-router/pull/13824))
- `@react-router/dev` - 改进构建期间找不到 chunk 时的错误日志 ([#13799](https://github.com/remix-run/react-router/pull/13799))
- `@react-router/dev` - 修复错误配置的 `externalConditions`，该配置为外部包启用了 `module` 条件，导致某些包（如 Emotion）的构建失败 ([#13871](https://github.com/remix-run/react-router/pull/13871))

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

- 为 Data 模式新增不稳定的 RSC 支持 ([#13700](https://github.com/remix-run/react-router/pull/13700))
  - 更多信息请参阅 [RSC 文档](https://reactrouter.com/how-to/react-server-components)

### 按包分类的变更

- [`create-react-router`](https://github.com/remix-run/react-router/blob/react-router%407.7.0/packages/create-react-router/CHANGELOG.md#770)
- [`react-router`](https://github.com/remix-run/react-router/blob/react-router%407.7.0/packages/react-router/CHANGELOG.md#770)
- [`@react-router/architect`](https://github.com/remix-run/react-router/blob/react-router%407.7.0/packages/react-router-architect/CHANGELOG.md#770)
- [`@react-router/cloudflare`](https://github.com/remix-run/react-router/blob/react-router%407.7.0/packages/react-router-cloudflare/CHANGELOG.md#770)
- [`@react-router/dev`](https://github.com/remix-run/react-router/blob/react-router%407.7.0/packages/react-router-dev/CHANGELOG.md#770)
- [`@react-router/express`](https://github.com/remix-run/react-router/blob/react-router%407.7.0/packages/react-router-express/CHANGELOG.md#770)
- [`@react-router/fs-routes`](https://github.com/remix-run/react-router/blob/react-router%407.7.0/packages/react-router-fs-routes/CHANGELOG.md#770)
- [`@react-router/node`](https://github.com/remix-run/react-router/blob/react-router%407.7.0/packages/react-router-node/CHANGELOG.md#770)
- [`@react-router/remix-config-routes-adapter`](https://github.com/remix-run/react-router/blob/react-router%407.7.0/packages/react-router-remix-config-routes-adapter/CHANGELOG.md#770)
- [`@react-router/serve`](https://github.com/remix-run/react-router/blob/react-router%407.7.0/packages/react-router-serve/CHANGELOG.md#770)

**完整更新日志**: [`v7.6.3...v7.7.0`](https://github.com/remix-run/react-router/compare/react-router@7.6.3...react-router@7.7.0)

## v7.6.3

日期：2025-06-27

### 补丁变更

- `react-router` - 不序列化 `useRouteLoaderData<typeof clientLoader>` 的类型 ([#13752](https://github.com/remix-run/react-router/pull/13752))
  - 为了让类型区分 `clientLoader` 和 `serverLoader`，你**必须**标注 `clientLoader` 的参数：

    ```ts
    //                                   👇 需要标注以跳过类型序列化
    export function clientLoader({}: Route.ClientLoaderArgs) {
      return { fn: () => "earth" };
    }

    function SomeComponent() {
      const data = useRouteLoaderData<typeof clientLoader>("routes/this-route");
      const planet = data?.fn() ?? "world";
      return <h1>Hello, {planet}!</h1>;
    }
    ```

- `@react-router/cloudflare` - 从 `peerDependencies` 中移除 `tsup` ([#13757](https://github.com/remix-run/react-router/pull/13757))
- `@react-router/dev` - 新增 Vite 7 支持 ([#13748](https://github.com/remix-run/react-router/pull/13748))
- `@react-router/dev` - 当提供自定义 `entry.server.(j|t)sx` 文件时跳过 `package.json` 解析检查 ([#13744](https://github.com/remix-run/react-router/pull/13744))
- `@react-router/dev` - 新增路由 id 不能为 'root' 的验证 ([#13792](https://github.com/remix-run/react-router/pull/13792))
- `@react-router/fs-routes` `@react-router/remix-config-routes-adapter` - 使用 `replaceAll` 规范化 Windows 文件系统斜杠 ([#13738](https://github.com/remix-run/react-router/pull/13738))
- `@react-router/node` - 移除旧的 "install" 包导出 ([#13762](https://github.com/remix-run/react-router/pull/13762))

**完整更新日志**: [`v7.6.2...v7.6.3`](https://github.com/remix-run/react-router/compare/react-router@7.6.2...react-router@7.6.3)

## v7.6.2

日期：2025-06-03

### 补丁变更

- `create-react-router` - 更新 `tar-fs` ([#13675](https://github.com/remix-run/react-router/pull/13675))
- `react-router` - （内部）轻微重构内部 `headers()` 函数处理以用于 RSC ([#13639](https://github.com/remix-run/react-router/pull/13639))
- `react-router` `@react-router/dev` - 通过将路由模块组件 prop 逻辑从 Vite 插件移到 `react-router` 来避免 Framework 模式中额外的 `with-props` chunk ([#13650](https://github.com/remix-run/react-router/pull/13650))
- `@react-router/dev` - 当启用 `future.unstable_viteEnvironmentApi` 且配置了绝对 Vite `base` 时，确保开发期间正确处理关键 CSS ([#13598](https://github.com/remix-run/react-router/pull/13598))
- `@react-router/dev` - 更新 `vite-node` ([#13673](https://github.com/remix-run/react-router/pull/13673))
- `@react-router/dev` - 修复非 {.js,.jsx,.ts,.tsx} 路由（如 .mdx）的类型生成 ([#12453](https://github.com/remix-run/react-router/pull/12453))
- `@react-router/dev` - 修复可选动态参数的 href 类型 ([#13725](https://github.com/remix-run/react-router/pull/13725))

  7.6.1 引入了使用可选静态段时 `href` 的修复，
  但这些修复导致了 7.6.0 中可选动态参数工作方式的回归：

  ```ts
  // 7.6.0
  href("/users/:id?"); // ✅
  href("/users/:id?", { id: 1 }); // ✅

  // 7.6.1
  href("/users/:id?"); // ❌
  href("/users/:id?", { id: 1 }); // ❌
  ```

  现在，可选静态段会为 `href` 展开为不同的路径，但可选动态参数不会。
  这样 `href` 可以明确地引用确切的 URL 路径，同时将路径选项数量保持在最低限度。

  ```ts
  // 7.6.2

  // path: /users/:id?/edit?
  href("
  //    ^ suggestions when cursor is here:
  //
  //    /users/:id?
  //    /users/:id?/edit
  ```

  此外，你可以从组件 props 传递 `params` 而无需手动缩窄它们：

  ```ts
  declare const params: { id?: number };

  // 7.6.0
  href("/users/:id?", params);

  // 7.6.1
  href("/users/:id?", params); // ❌
  "id" in params ? href("/users/:id", params) : href("/users"); // works... but is annoying

  // 7.6.2
  href("/users/:id?", params); // restores behavior of 7.6.0
  ```

**完整更新日志**: [`v7.6.1...v7.6.2`](https://github.com/remix-run/react-router/compare/react-router@7.6.1...react-router@7.6.2)

## v7.6.1

日期：2025-05-25

### 补丁变更

- `react-router` - 部分恢复 `7.1.4` 中添加的减少 `matchRoutes` 调用的优化，因为它暴露了其他问题 ([#13562](https://github.com/remix-run/react-router/pull/13562))
- `react-router` - 更新 `Route.MetaArgs` 以反映 `data` 可能为 `undefined` ([#13563](https://github.com/remix-run/react-router/pull/13563))
  - 这主要是针对路由 `loader` 向其自身的 `ErrorBoundary` 抛出错误的情况，也适用于 404 渲染根 `ErrorBoundary`/`meta` 但根 `loader` 因没有匹配路由而未运行的情况
- `react-router` - 避免懒加载路由发现被导航中断时的初始 fetcher 执行 404 错误 ([#13564](https://github.com/remix-run/react-router/pull/13564))
- `react-router` - 正确地让 `href` 替换 splat `*` ([#13593](https://github.com/remix-run/react-router/pull/13593))
  - `href("/products/*", { "*": "/1/edit" }); // -> /products/1/edit`
- `@react-router/architect` - 更新 `@architect/functions` 从 `^5.2.0` 至 `^7.0.0` ([#13556](https://github.com/remix-run/react-router/pull/13556))
- `@react-router/dev` - 防止对 `app/` 目录外的路由文件进行类型生成 ([#12996](https://github.com/remix-run/react-router/pull/12996))
- `@react-router/dev` - 清理服务器构建中的资源时为 `build` 命令输出新增额外日志 ([#13547](https://github.com/remix-run/react-router/pull/13547))
- `@react-router/dev` - 当 Vite 配置中启用了 `build.ssrEmitAssets` 时不要清理服务器构建中的资源 ([#13547](https://github.com/remix-run/react-router/pull/13547))
- `@react-router/dev` - 修复同一路由在多个路径使用时的类型生成 ([#13574](https://github.com/remix-run/react-router/pull/13574))
  - For example, `routes/route.tsx` is used at 4 different paths here:

    ```ts
    import { type RouteConfig, route } from "@react-router/dev/routes";
    export default [
      route("base/:base", "routes/base.tsx", [
        route("home/:home", "routes/route.tsx", { id: "home" }),
        route("changelog/:changelog", "routes/route.tsx", { id: "changelog" }),
        route("splat/*", "routes/route.tsx", { id: "splat" }),
      ]),
      route("other/:other", "routes/route.tsx", { id: "other" }),
    ] satisfies RouteConfig;
    ```

  - 之前，类型生成会任意选择其中一个路径作为"获胜者"，并基于该路径为路由模块生成类型
  - 现在，类型生成会根据需要为同一路由文件的替代路径创建联合类型

- `@react-router/dev` - 更好的 `params` 类型 ([#13543](https://github.com/remix-run/react-router/pull/13543))
  - For example:

    ```ts
    // routes.ts
    import { type RouteConfig, route } from "@react-router/dev/routes";

    export default [
      route("parent/:p", "routes/parent.tsx", [
        route("route/:r", "routes/route.tsx", [
          route("child1/:c1a/:c1b", "routes/child1.tsx"),
          route("child2/:c2a/:c2b", "routes/child2.tsx"),
        ]),
      ]),
    ] satisfies RouteConfig;
    ```

  - Previously, `params` for `routes/route` were calculated as `{ p: string, r: string }`.
  - This incorrectly ignores params that could come from child routes
  - If visiting `/parent/1/route/2/child1/3/4`, the actual params passed to `routes/route` will have a type of `{ p: string, r: string, c1a: string, c1b: string }`
  - Now, `params` are aware of child routes and autocompletion will include child params as optionals:

    ```ts
    params.|
    //     ^ cursor is here and you ask for autocompletion
    // p: string
    // r: string
    // c1a?: string
    // c1b?: string
    // c2a?: string
    // c2b?: string
    ```

  - You can also narrow the types for `params` as it is implemented as a normalized union of params for each page that includes `routes/route`:

    ```ts
    if (typeof params.c1a === 'string') {
      params.|
      //     ^ cursor is here and you ask for autocompletion
      // p: string
      // r: string
      // c1a: string
      // c1b: string
    }
    ```

- `@react-router/dev` - 修复可选段的 `href` ([#13595](https://github.com/remix-run/react-router/pull/13595))
  - 类型生成现在将带有可选项的路径展开为对应的非可选路径
  - For example, the path `/user/:id?` gets expanded into `/user` and `/user/:id` to more closely model visitable URLs
  - `href` then uses these expanded (non-optional) paths to construct type-safe paths for your app:

    ```ts
    // original: /user/:id?
    // expanded: /user & /user/:id
    href("/user"); // ✅
    href("/user/:id", { id: 1 }); // ✅
    ```

  - This becomes even more important for static optional paths where there wasn't a good way to indicate whether the optional should be included in the resulting path:

    ```ts
    // original: /products/:id/detail?

    // before
    href("/products/:id/detail?"); // ❌ How can we tell `href` to include or omit `detail?` segment with a complex API?

    // now
    // expanded: /products/:id & /products/:id/detail
    href("/product/:id"); // ✅
    href("/product/:id/detail"); // ✅
    ```

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

- `@react-router/dev` - 将内部 `react-router/route-module` 导出重命名为 `react-router/internal` ([#13543](https://github.com/remix-run/react-router/pull/13543))
- `@react-router/dev` - 从生成的 `+types/*` 文件中移除 `Info` 导出 ([#13543](https://github.com/remix-run/react-router/pull/13543))
- `@react-router/dev` - 在生成 SRI manifest 时跨 Node 版本规范化目录条目路径 ([#13591](https://github.com/remix-run/react-router/pull/13591))

**完整更新日志**: [`v7.6.0...v7.6.1`](https://github.com/remix-run/react-router/compare/react-router@7.6.0...react-router@7.6.1)

## v7.6.0

日期：2025-05-08

### 重要变更

#### `routeDiscovery` Config Option

我们在 `7.6.0` 中新增了一个配置选项，让你对懒加载路由发现特性有更多控制。如果你在同一服务器上运行多个 RR 应用，现在可以配置 `/__manifest` 路径，或者如果你的应用足够小不需要该特性，也可以完全禁用它。

```ts
// react-router.config.ts

export default {
  // 你可以修改使用的 manifest 路径：
  routeDiscovery: { mode: "lazy", manifestPath: "/custom-manifest" }

  // 或者你可以完全禁用此特性并在初始文档加载时
  // 包含所有路由到 manifest 中：
  routeDiscovery: { mode: "initial" }

  // 如果不指定任何内容，默认配置如下，启用
  // 懒加载路由发现并向 `/__manifest` 路径发送 manifest 请求：
  // routeDiscovery: { mode: "lazy", manifestPath: "/__manifest" }
} satisfies Config;
```

#### Future Flags 自动类型

某些 future 标志会改变 React Router 中类型的工作方式。之前你必须记住手动选择新类型。例如对于 `future.unstable_middleware`：

```ts
// react-router.config.ts

// 步骤 1：启用中间件
export default {
  future: {
    unstable_middleware: true,
  },
};

// 步骤 2：启用中间件类型
declare module "react-router" {
  interface Future {
    unstable_middleware: true; // 👈 启用中间件类型
  }
}
```

你需要自己保持运行时 future 标志与这些标志的类型同步。这既令人困惑又容易出错。

现在，React Router 将自动为 future 标志启用类型。这意味着你只需要指定运行时的 future 标志：

```ts
// react-router.config.ts

// 步骤 1：启用中间件
export default {
  future: {
    unstable_middleware: true,
  },
};

// 不需要步骤 2！就这么简单！
```

在幕后，React Router 将在 `.react-router/types` 中生成相应的 `declare module`。目前这在 `.react-router/types/+register.ts` 中完成，但这是可能在未来更改的实现细节。

### 次要变更

- `react-router` - 在 `react-router.config.ts` 中新增 `routeDiscovery` 选项以配置懒加载路由发现行为 ([#13451](https://github.com/remix-run/react-router/pull/13451))
- `react-router` - 在 `createRoutesStub` 中新增路由组件 props 支持 ([#13528](https://github.com/remix-run/react-router/pull/13528))
  - 这允许你使用 props 而非 hooks 对路由组件进行单元测试：

    ```tsx
    let RoutesStub = createRoutesStub([
      {
        path: "/",
        Component({ loaderData }) {
          let data = loaderData as { message: string };
          return <pre data-testid="data">Message: {data.message}</pre>;
        },
        loader() {
          return { message: "hello" };
        },
      },
    ]);

    render(<RoutesStub />);

    await waitFor(() => screen.findByText("Message: hello"));
    ```

- `@react-router/dev` - Future 标志自动类型 ([#13506](https://github.com/remix-run/react-router/pull/13506))

### 补丁变更

你可能注意到这个列表比平时大一些！团队上周认真工作，花了一周时间[修复 bug](https://x.com/BrooksLybrand/status/1918406062920589731)，以降低自 v7 发布以来膨胀的 issue 数量。

- `react-router` - 修复 `react-router` 对 `NodeNext` 的模块增强 ([#13498](https://github.com/remix-run/react-router/pull/13498))
- `react-router` - 不要在 `react-router/dom` CJS 导出中打包 `react-router` ([#13497](https://github.com/remix-run/react-router/pull/13497))
- `react-router` - 修复提交中的 `fetcher` 在重新验证的 `loader` 重定向时卡在 `loading` 状态的 bug ([#12873](https://github.com/remix-run/react-router/pull/12873))
- `react-router` - 修复服务器 `loader` 返回 `undefined` 时的 hydration 错误 ([#13496](https://github.com/remix-run/react-router/pull/13496))
- `react-router` - 修复 data 模式中初始加载 404 的场景 ([#13500](https://github.com/remix-run/react-router/pull/13500))
- `react-router` - 稳定化 `useRevalidator` 的 `revalidate` 函数 ([#13542](https://github.com/remix-run/react-router/pull/13542))
- `react-router` - 在 framework 模式中如果 `clientAction` 抛出 `data()` 结果则保留状态码 ([#13522](https://github.com/remix-run/react-router/pull/13522))
- `react-router` - 防御路径中的前导双斜杠以避免 URL 构造函数的 `Invalid URL` 错误 ([#13510](https://github.com/remix-run/react-router/pull/13510))
  - 注意我们不会清理/规范化这些路径 - 我们只检测它们以避免 `new URL("//", window.location.origin)` 会抛出的错误
- `react-router` - 移除 `navigator.connection.saveData` 的 `Navigator` 声明，以避免影响用户代码中 `saveData` 之外的其他类型 ([#13512](https://github.com/remix-run/react-router/pull/13512))
- `react-router` - 修复以动态参数作为最后 URL 段的路由在 `.data` 请求上的 `handleError` `params` 值 ([#13481](https://github.com/remix-run/react-router/pull/13481))
- `react-router` - 在懒加载路由发现中检测到 manifest 版本不匹配时，不要在重新加载之前触发 `ErrorBoundary` UI ([#13480](https://github.com/remix-run/react-router/pull/13480))
- `react-router` - 内联 `turbo-stream@2.4.1` 依赖并修复 `Map`/`Set` 实例的解码顺序 ([#13518](https://github.com/remix-run/react-router/pull/13518))
- `react-router` - 仅在开发环境中渲染开发警告 ([#13461](https://github.com/remix-run/react-router/pull/13461))
- `react-router` - 对中止的 `dataStrategy` 请求短路后处理 ([#13521](https://github.com/remix-run/react-router/pull/13521))
  - 这解决了形如 `Cannot read properties of undefined (reading 'result')` 的非面向用户的控制台错误
- `@react-router/dev` - 如果 `package.json` 存在于父目录中，支持没有 `package.json` 的项目根目录 ([#13472](https://github.com/remix-run/react-router/pull/13472))
- `@react-router/dev` - 通过 CLI `--config`/`-c` 标志提供自定义 Vite 配置路径时，如果未明确提供则默认项目根目录为包含 Vite 配置的目录 ([#13472](https://github.com/remix-run/react-router/pull/13472))
- `@react-router/dev` - 在 `routes.ts` 上下文中，确保 `--mode` 标志对 `import.meta.env.MODE` 生效 ([#13485](https://github.com/remix-run/react-router/pull/13485))
  - 之前，在 `routes.ts` 上下文中 `import.meta.env.MODE` 对于 `dev` 和 `typegen --watch` 命令始终是 `"development"`，否则解析为 `"production"`。这些默认值仍然保留，但如果提供了 `--mode` 标志，现在将优先使用。
- `@react-router/dev` - 确保 CLI 命令中一致的项目根目录解析逻辑 ([#13472](https://github.com/remix-run/react-router/pull/13472))
- `@react-router/dev` - 使用 `vite-node` 执行 `react-router.config.ts` 和 `routes.ts` 时，确保忽略 PostCSS 配置文件 ([#13489](https://github.com/remix-run/react-router/pull/13489))
- `@react-router/dev` - 在开发期间提取关键 CSS 时，确保从客户端环境加载以避免不同处理 SSR 环境的插件的问题 ([#13503](https://github.com/remix-run/react-router/pull/13503))
- `@react-router/dev` - 修复使用 HTTPS 时开发期间的 "Status message is not supported by HTTP/2" 错误 ([#13460](https://github.com/remix-run/react-router/pull/13460))
- `@react-router/dev` - 在开发期间创建或删除 `react-router.config.ts` 时更新配置 ([#12319](https://github.com/remix-run/react-router/pull/12319))
- `@react-router/dev` - 在 Vite 构建开始前跳过不必要的 `routes.ts` 执行 ([#13513](https://github.com/remix-run/react-router/pull/13513))
- `@react-router/dev` - 修复生成的类型导致的 `TS2300: Duplicate identifier` 错误 ([#13499](https://github.com/remix-run/react-router/pull/13499))
- 之前，具有相同完整路径的路由会在生成的 `href` 类型（`.react-router/types/+register.ts`）中产生重复条目，导致类型检查错误

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

- `react-router` - 修复中间件用例中错误冒泡的一些 bug ([#13538](https://github.com/remix-run/react-router/pull/13538))
- `@react-router/dev` - 启用 `future.unstable_viteEnvironmentApi` 时，确保在未配置 `environments.client.build.assetsDir` 时尊重 Vite 配置中的 `build.assetsDir` ([#13491](https://github.com/remix-run/react-router/pull/13491))

### 按包分类的变更

- [`create-react-router`](https://github.com/remix-run/react-router/blob/react-router%407.6.0/packages/create-react-router/CHANGELOG.md#760)
- [`react-router`](https://github.com/remix-run/react-router/blob/react-router%407.6.0/packages/react-router/CHANGELOG.md#760)
- [`@react-router/architect`](https://github.com/remix-run/react-router/blob/react-router%407.6.0/packages/react-router-architect/CHANGELOG.md#760)
- [`@react-router/cloudflare`](https://github.com/remix-run/react-router/blob/react-router%407.6.0/packages/react-router-cloudflare/CHANGELOG.md#760)
- [`@react-router/dev`](https://github.com/remix-run/react-router/blob/react-router%407.6.0/packages/react-router-dev/CHANGELOG.md#760)
- [`@react-router/express`](https://github.com/remix-run/react-router/blob/react-router%407.6.0/packages/react-router-express/CHANGELOG.md#760)
- [`@react-router/fs-routes`](https://github.com/remix-run/react-router/blob/react-router%407.6.0/packages/react-router-fs-routes/CHANGELOG.md#760)
- [`@react-router/node`](https://github.com/remix-run/react-router/blob/react-router%407.6.0/packages/react-router-node/CHANGELOG.md#760)
- [`@react-router/remix-config-routes-adapter`](https://github.com/remix-run/react-router/blob/react-router%407.6.0/packages/react-router-remix-config-routes-adapter/CHANGELOG.md#760)
- [`@react-router/serve`](https://github.com/remix-run/react-router/blob/react-router%407.6.0/packages/react-router-serve/CHANGELOG.md#760)

**完整更新日志**: [`v7.5.3...v7.6.0`](https://github.com/remix-run/react-router/compare/react-router@7.5.3...react-router@7.6.0)

## v7.5.3

日期：2025-04-28

### 补丁变更

- `react-router` - 修复冒泡的 action 错误导致在处理的 `ErrorBoundary` 路由上 `loaderData` 被清除的 bug ([#13476](https://github.com/remix-run/react-router/pull/13476))
- `react-router` - 处理 `clientLoader.hydrate` 初始加载执行中的重定向 ([#13477](https://github.com/remix-run/react-router/pull/13477))

**完整更新日志**: [`v7.5.2...v7.5.3`](https://github.com/remix-run/react-router/compare/react-router@7.5.2...react-router@7.5.3)

## v7.5.2

日期：2025-04-24

### 安全通知

修复了 2 个安全漏洞，通过发送用于 SPA 模式和预渲染的构建时使用的特定头可能导致缓存投毒攻击（[GHSA-f46r-rw29-r322](https://github.com/remix-run/react-router/security/advisories/GHSA-f46r-rw29-r322)、[GHSA-cpj6-fhp6-mr6j](https://github.com/remix-run/react-router/security/advisories/GHSA-cpj6-fhp6-mr6j)）。

### 补丁变更

- `react-router` - 调整通过 header 的预渲染/SPA 模式方法 ([#13453](https://github.com/remix-run/react-router/pull/13453))
- `react-router` - 更新 Single Fetch 以处理 Remix v2 中 `?_data` 请求使用的 204 重定向 ([#13364](https://github.com/remix-run/react-router/pull/13364))
  - 这允许应用从 React Router 范围之外（即 `express`/`hono` 中间件）以 Single Fetch 实现之前 Remix v2 中的相同方式在 `.data` 请求上触发重定向
  - 这是一个逃生舱 - 推荐的处理方式是从根路由中间件重定向
  - 要使用此功能，你可以从 `.data` 请求返回如下响应：
    - 设置 204 状态码
    - Set an `X-Remix-Redirect: <new-location>` header
    - 可选地，设置 `X-Remix-Replace: true` 或 `X-Remix-Reload-Document: true` header 以复制 `replace()`/`redirectDocument()` 功能
  - ⚠️ 请注意这些响应依赖于可能在没有 SemVer 主要版本的情况下更改的实现细节，建议你为应用设置集成测试以确认此功能在每次未来的 React Router 升级中都正常工作

**完整更新日志**: [`v7.5.1...v7.5.2`](https://github.com/remix-run/react-router/compare/react-router@7.5.1...react-router@7.5.2)

## v7.5.1

日期：2025-04-17

### 补丁变更

- `react-router` - 当using the object-based `route.lazy` API, the `HydrateFallback` and `hydrateFallbackElement` properties are now skipped when lazy loading routes after hydration ([#13376](https://github.com/remix-run/react-router/pull/13376))
  - If you move the code for these properties into a separate file, since the hydrate properties were unused already (if the route wasn't present during hydration), you can avoid downloading them at all. For example:

    ```ts
    createBrowserRouter([
      {
        path: "/show/:showId",
        lazy: {
          loader: async () => (await import("./show.loader.js")).loader,
          Component: async () =>
            (await import("./show.component.js")).Component,
          HydrateFallback: async () =>
            (await import("./show.hydrate-fallback.js")).HydrateFallback,
        },
      },
    ]);
    ```

- `react-router` - 修复single fetch bug where no revalidation request would be made when navigating upwards to a reused parent route ([#13253](https://github.com/remix-run/react-router/pull/13253))
- `react-router` - 正确地revalidate pre-rendered paths when param values change when using `ssr:false` + `prerender` configs ([#13380](https://github.com/remix-run/react-router/pull/13380))
- `react-router` - 修复pre-rendering when a loader returns a redirect ([#13365](https://github.com/remix-run/react-router/pull/13365))
- `react-router` - 不automatically add `null` to `staticHandler.query()` `context.loaderData` if routes do not have loaders ([#13223](https://github.com/remix-run/react-router/pull/13223))
  - This was a Remix v2 implementation detail inadvertently left in for React Router v7
  - Now that we allow returning `undefined` from loaders, our prior check of `loaderData[routeId] !== undefined` was no longer sufficient and was changed to a `routeId in loaderData` check - these `null` values can cause issues for this new check
  - ⚠️ This could be a "breaking bug fix" for you if you are doing manual SSR with `createStaticHandler()`/`<StaticRouterProvider>`, and using `context.loaderData` to control `<RouterProvider>` hydration behavior on the client

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

- `react-router` - 新增better error messaging when `getLoadContext` is not updated to return a `Map` ([#13242](https://github.com/remix-run/react-router/pull/13242))
- `react-router` - 更新context type for `LoaderFunctionArgs`/`ActionFunctionArgs` when middleware is enabled ([#13381](https://github.com/remix-run/react-router/pull/13381))
- `react-router` - 新增a new `unstable_runClientMiddleware` argument to `dataStrategy` to enable middleware execution in custom `dataStrategy` implementations ([#13395](https://github.com/remix-run/react-router/pull/13395))
- `react-router` - 新增support for the new `unstable_shouldCallHandler`/`unstable_shouldRevalidateArgs` APIs in `dataStrategy` ([#13253](https://github.com/remix-run/react-router/pull/13253))

**完整更新日志**: [`v7.5.0...v7.5.1`](https://github.com/remix-run/react-router/compare/react-router@7.5.0...react-router@7.5.1)

## v7.5.0

日期：2025-04-04

### 重要变更

#### `route.lazy` 对象 API

我们引入了一个新的 `route.lazy` API，它为你提供对路由属性懒加载更精细的控制，这是通过 `route.lazy()` 函数签名无法实现的。这对于 Framework 模式和性能关键的库模式应用非常有用。

```ts
createBrowserRouter([
  {
    path: "/show/:showId",
    lazy: {
      loader: async () => (await import("./show.loader.js")).loader,
      action: async () => (await import("./show.action.js")).action,
      Component: async () => (await import("./show.component.js")).Component,
    },
  },
]);
```

⚠️ 如果你已采用 `route.unstable_lazyMiddleware` API，这是一个破坏性变更，该 API 已被移除，改为使用 `route.lazy.unstable_middleware`。更多信息请参阅下面的「不稳定变更」部分。

### 次要变更

- `react-router` - 新增基于对象的细粒度 `route.lazy` API 以支持单个路由属性的懒加载 ([#13294](https://github.com/remix-run/react-router/pull/13294))

### 补丁变更

- `@react-router/dev` - 更新可选的 `wrangler` 对等依赖范围以支持 `wrangler` v4 ([#13258](https://github.com/remix-run/react-router/pull/13258))
- `@react-router/dev` - 恢复子编译器中的依赖优化以修复使用 `vite-plugin-cloudflare` 和导入 Node.js 内置模块时的 `depsOptimizer is required in dev mode` 错误 ([#13317](https://github.com/remix-run/react-router/pull/13317))

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

- `react-router` - 引入 `future.unstable_subResourceIntegrity` 标志，为浏览器将加载的脚本启用带有 `integrity` 的 `importmap` 生成 ([#13163](https://github.com/remix-run/react-router/pull/13163))
- `react-router` - 移除对 `route.unstable_lazyMiddleware` 属性的支持 ([#13294](https://github.com/remix-run/react-router/pull/13294))
  - 要懒加载中间件，可以使用新的基于对象的 `route.lazy.unstable_middleware` API
- `@react-router/dev` - 启用 `future.unstable_viteEnvironmentApi` 时，确保配置了自定义 Vite `base` 时开发环境中的关键 CSS 正常工作 ([#13305](https://github.com/remix-run/react-router/pull/13305))

### 按包分类的变更

- [`create-react-router`](https://github.com/remix-run/react-router/blob/react-router%407.5.0/packages/create-react-router/CHANGELOG.md#750)
- [`react-router`](https://github.com/remix-run/react-router/blob/react-router%407.5.0/packages/react-router/CHANGELOG.md#750)
- [`@react-router/architect`](https://github.com/remix-run/react-router/blob/react-router%407.5.0/packages/react-router-architect/CHANGELOG.md#750)
- [`@react-router/cloudflare`](https://github.com/remix-run/react-router/blob/react-router%407.5.0/packages/react-router-cloudflare/CHANGELOG.md#750)
- [`@react-router/dev`](http://github.com/remix-run/react-router/blob/react-router%407.5.0/packages/react-router-dev/CHANGELOG.md#750)
- [`@react-router/express`](https://github.com/remix-run/react-router/blob/react-router%407.5.0/packages/react-router-express/CHANGELOG.md#750)
- [`@react-router/fs-routes`](https://github.com/remix-run/react-router/blob/react-router%407.5.0/packages/react-router-fs-routes/CHANGELOG.md#750)
- [`@react-router/node`](https://github.com/remix-run/react-router/blob/react-router%407.5.0/packages/react-router-node/CHANGELOG.md#750)
- [`@react-router/remix-config-routes-adapter`](https://github.com/remix-run/react-router/blob/react-router%407.5.0/packages/react-router-remix-config-routes-adapter/CHANGELOG.md#750)
- [`@react-router/serve`](https://github.com/remix-run/react-router/blob/react-router%407.5.0/packages/react-router-serve/CHANGELOG.md#750)

**完整更新日志**: [`v7.4.1...v7.5.0`](https://github.com/remix-run/react-router/compare/react-router@7.4.1...react-router@7.5.0)

## v7.4.1

日期：2025-03-28

### 安全通知

修复了一个安全漏洞，该漏洞因端口清理不充分，允许通过 `Host` 和 `X-Forwarded-Host` 头进行 URL 操纵和潜在的缓存污染 ([GHSA-4q56-crqp-v477/CVE-2025-31137](https://github.com/remix-run/react-router/security/advisories/GHSA-4q56-crqp-v477)).

### 补丁变更

- `react-router` - 对 `route.lazy` 函数调用进行去重 ([#13260](https://github.com/remix-run/react-router/pull/13260))
- `@react-router/dev` - 修复预渲染错误消息中的路径 ([#13257](https://github.com/remix-run/react-router/pull/13257))
- `@react-router/dev` - 修复 `moduleDetection` 设为 `force` 时虚拟模块的类型生成 ([#13267](https://github.com/remix-run/react-router/pull/13267))
- `@react-router/express` - 更好的 `x-forwarded-host` header 验证以防止潜在的安全问题 ([#13309](https://github.com/remix-run/react-router/pull/13309))

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

- `react-router` - 修复 `unstable_MiddlewareFunction` 的类型以避免中间件不返回值时的类型错误 ([#13311](https://github.com/remix-run/react-router/pull/13311))
- `react-router` - 新增 `route.unstable_lazyMiddleware` 函数支持以允许中间件逻辑的懒加载 ([#13210](https://github.com/remix-run/react-router/pull/13210))
  - ⚠️ 我们目前不建议采用此 API，因为我们可能会在中间件稳定发布之前对其进行更改
  - ⚠️ 如果你的应用当前从 `route.lazy` 返回 `unstable_middleware`，这可能是一个破坏性变更
  - `route.lazy` 返回值中不再支持 `route.unstable_middleware` 属性
  - 如果要懒加载中间件，必须使用 `route.unstable_lazyMiddleware`
- `@react-router/dev` - 当同时启用 `future.unstable_middleware` 和 `future.unstable_splitRouteModules` 时，尽可能将 `unstable_clientMiddleware` 路由导出拆分为单独的 chunk ([#13210](https://github.com/remix-run/react-router/pull/13210))
- `@react-router/dev` - 通过确保路由模块仅在定义了 `unstable_clientMiddleware` 时才在中间件阶段阻塞来改进 `future.unstable_middleware` 的性能 ([#13210](https://github.com/remix-run/react-router/pull/13210))

**完整更新日志**: [`v7.4.0...v7.4.1`](https://github.com/remix-run/react-router/compare/react-router@7.4.0...react-router@7.4.1)

## v7.4.0

日期：2025-03-19

### 次要变更

- `@react-router/dev` - 为 `virtual:react-router/server-build` 模块生成类型 ([#13152](https://github.com/remix-run/react-router/pull/13152))

### 补丁变更

- `react-router` - 修复 SPA 模式中初始加载重定向时的根 loader 数据 ([#13222](https://github.com/remix-run/react-router/pull/13222))
- `react-router` - 在懒加载路由发现中加载祖先无路径/索引路由以支持向上的非贪婪发现路由 ([#13203](https://github.com/remix-run/react-router/pull/13203))
- `react-router` - 修复 `ssr:true` 应用中仅有 `clientLoader` 的路由的 `shouldRevalidate` 行为 ([#13221](https://github.com/remix-run/react-router/pull/13221))
- `@react-router/dev` - 修复与使用 `configureServer` 和/或 `configurePreviewServer` hook 的其他 Vite 插件的冲突 ([#13184](https://github.com/remix-run/react-router/pull/13184))

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

- `react-router` - 如果中间件抛出错误，确保我们只通过 `next()` 冒泡错误本身，不再泄露 `MiddlewareError` 实现细节 ([#13180](https://github.com/remix-run/react-router/pull/13180))
  - ⚠️ 如果你在中间件中 `catch` 了 `next()` 函数抛出的错误，这可能是一个破坏性变更
- `react-router` - 修复启用中间件时 `RequestHandler` 的 `loadContext` 参数类型 ([#13204](https://github.com/remix-run/react-router/pull/13204))
- `react-router` - 更新 `Route.unstable_MiddlewareFunction` 的返回值为 `Response | undefined` 而非 `Response | void` ([#13199](https://github.com/remix-run/react-router/pull/13199))
- `@react-router/dev` - 当 `future.unstable_splitRouteModules` 设为 `"enforce"` 时，允许根路由导出同时包含可拆分和不可拆分的导出，因为它始终在单个 chunk 中 ([#13238](https://github.com/remix-run/react-router/pull/13238))
- `@react-router/dev` - 启用 `future.unstable_viteEnvironmentApi` 时，允许覆盖默认 SSR 环境的插件（如 `@cloudflare/vite-plugin`）放在 React Router 插件的前面或后面 ([#13183](https://github.com/remix-run/react-router/pull/13183))

### 按包分类的变更

- [`create-react-router`](https://github.com/remix-run/react-router/blob/react-router%407.4.0/packages/create-react-router/CHANGELOG.md#740)
- [`react-router`](https://github.com/remix-run/react-router/blob/react-router%407.4.0/packages/react-router/CHANGELOG.md#740)
- [`@react-router/architect`](https://github.com/remix-run/react-router/blob/react-router%407.4.0/packages/react-router-architect/CHANGELOG.md#740)
- [`@react-router/cloudflare`](https://github.com/remix-run/react-router/blob/react-router%407.4.0/packages/react-router-cloudflare/CHANGELOG.md#740)
- [`@react-router/dev`](https://github.com/remix-run/react-router/blob/react-router%407.4.0/packages/react-router-dev/CHANGELOG.md#740)
- [`@react-router/express`](https://github.com/remix-run/react-router/blob/react-router%407.4.0/packages/react-router-express/CHANGELOG.md#740)
- [`@react-router/fs-routes`](https://github.com/remix-run/react-router/blob/react-router%407.4.0/packages/react-router-fs-routes/CHANGELOG.md#740)
- [`@react-router/node`](https://github.com/remix-run/react-router/blob/react-router%407.4.0/packages/react-router-node/CHANGELOG.md#740)
- [`@react-router/remix-config-routes-adapter`](https://github.com/remix-run/react-router/blob/react-router%407.4.0/packages/react-router-remix-config-routes-adapter/CHANGELOG.md#740)
- [`@react-router/serve`](https://github.com/remix-run/react-router/blob/react-router%407.4.0/packages/react-router-serve/CHANGELOG.md#740)

**完整更新日志**: [`v7.3.0...v7.4.0`](https://github.com/remix-run/react-router/compare/react-router@7.3.0...react-router@7.4.0)

## v7.3.0

日期：2025-03-06

### 次要变更

- 为 `patchRoutesOnNavigation` 新增 `fetcherKey` 参数 ([#13061](https://github.com/remix-run/react-router/pull/13061))

### 补丁变更

- `react-router` - 检测并处理活跃会话期间新部署的 manifest 偏差问题 ([#13061](https://github.com/remix-run/react-router/pull/13061))
  - 在 framework 模式中，懒加载路由发现现在将在新部署后检测活跃会话中的 manifest 版本不匹配
  - 在导航到未发现的路由时，此不匹配将触发目标路径的文档重新加载
  - 在 `fetcher` 调用未发现的路由时，此不匹配将触发当前路径的文档重新加载
- `react-router` - 在 SPA 模式的开发服务器中跳过资源路由流程 ([#13113](https://github.com/remix-run/react-router/pull/13113))
- `react-router` - 修复使用 `basename` 时的 single fetch `_root.data` 请求 ([#12898](https://github.com/remix-run/react-router/pull/12898))
- `react-router` - 修复包含 `Record` 的 `loaderData` 和 `actionData` 的类型 ([#13139](https://github.com/remix-run/react-router/pull/13139))
  - ⚠️ 对于已采用 `unstable_SerializesTo` 的用户这是一个破坏性变更 - 更多信息请参阅下面「不稳定变更」部分的说明
- `@react-router/dev` - 修复自定义客户端 `build.rollupOptions.output.entryFileNames` 的支持 ([#13098](https://github.com/remix-run/react-router/pull/13098))
- `@react-router/dev` - 修复当 `serverBundles` 选项已配置或由预设提供（如 `@vercel/react-router` 的 `vercelPreset`）时 `prerender` 选项的使用 ([#13082](https://github.com/remix-run/react-router/pull/13082))
- `@react-router/dev` - 修复自定义 `build.assetsDir` 的支持 ([#13077](https://github.com/remix-run/react-router/pull/13077))
- `@react-router/dev` - 移除未使用的依赖 ([#13134](https://github.com/remix-run/react-router/pull/13134))
- `@react-router/dev` - 在 "SPA 模式" 服务器构建中桩化根路由以外的所有路由，以避免路由模块或其依赖导入非 SSR 友好模块时的问题 ([#13023](https://github.com/remix-run/react-router/pull/13023))
- `@react-router/dev` - 移除未使用的 Vite 文件系统监听器 ([#13133](https://github.com/remix-run/react-router/pull/13133))
- `@react-router/dev` - 修复配置了 `serverBundles` 选项时自定义 SSR 构建输入的支持 ([#13107](https://github.com/remix-run/react-router/pull/13107))
  - ⚠️ 注意对于同时使用 `future.unstable_viteEnvironmentApi` 和 `serverBundles` 选项的用户，服务器包 ID 中不再支持连字符，因为它们也需要是有效的 Vite 环境名称。
- `@react-router/dev` - 通过从开发服务器请求中剥离 HTTP/2 伪头修复使用 HTTPS 时的开发服务器 ([#12830](https://github.com/remix-run/react-router/pull/12830))
- `@react-router/dev` - 使用 `cloudflareDevProxy` Vite 插件时在第一个开发服务器请求时延迟加载 Cloudflare 平台代理以避免创建不必要的 `workerd` 进程 ([#13016](https://github.com/remix-run/react-router/pull/13016))
- `@react-router/dev` - 修复布局路由及其对应索引路由的类型生成中的重复条目 ([#13140](https://github.com/remix-run/react-router/pull/13140))
- `@react-router/express` - 更新 `express` `peerDependency` 以包含 v5 (https://github.com/remix-run/react-router/pull/13064) ([#12961](https://github.com/remix-run/react-router/pull/12961))

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

- `react-router` - 为客户端数据路由器新增 `context` 支持（不稳定） ([#12941](https://github.com/remix-run/react-router/pull/12941))
- `react-router` - 支持路由上的中间件（不稳定） ([#12941](https://github.com/remix-run/react-router/pull/12941))
- `@react-router/dev` - 修复当 `ssr` 环境被另一个插件配置为自定义 `Vite.DevEnvironment` 而非默认 `Vite.RunnableDevEnvironment` 时 `future.unstable_viteEnvironmentApi` 的错误 ([#13008](https://github.com/remix-run/react-router/pull/13008))
- `@react-router/dev` - 启用 `future.unstable_viteEnvironmentApi` 且 `ssr` 环境禁用了 `optimizeDeps.noDiscovery` 时，定义 `optimizeDeps.entries` 和 `optimizeDeps.include` ([#13007](https://github.com/remix-run/react-router/pull/13007))

#### 客户端 `context`（不稳定）

你的应用 `clientLoader`/`clientAction` 函数（或库模式中的 `loader`/`action`）现在将在客户端接收一个 `context` 参数。这是一个 `unstable_RouterContextProvider` 实例，你可以将其与类型安全的上下文一起使用（类似于 `React.createContext`），与相应的 `unstable_clientMiddleware` API 配合使用最为有用：

```ts
import { unstable_createContext } from "react-router";

type User = {
  /*...*/
};

const userContext = unstable_createContext<User>();

const sessionMiddleware: Route.unstable_ClientMiddlewareFunction = async ({
  context,
}) => {
  let user = await getUser();
  context.set(userContext, user);
};

export const unstable_clientMiddleware = [sessionMiddleware];

export function clientLoader({ context }: Route.ClientLoaderArgs) {
  let user = context.get(userContext);
  let profile = await getProfile(user.id);
  return { profile };
}
```

与服务器端请求类似，每次导航（或 `fetcher` 调用）都会创建一个新的 `context`。如果你有想要为每次请求在上下文中填充的初始数据，可以在应用根部提供一个 `unstable_getContext` 函数：

- Library mode - `createBrowserRouter(routes, { unstable_getContext })`
- Framework mode - `<HydratedRouter unstable_getContext>`

此函数应返回一个 `unstable_InitialContext` 类型的值，它是上下文和初始值的 `Map<unstable_RouterContext, unknown>`：

```ts
const loggerContext = unstable_createContext<(...args: unknown[]) => void>();

function logger(...args: unknown[]) {
  console.log(new Date.toISOString(), ...args);
}

function unstable_getContext() {
  let map = new Map();
  map.set(loggerContext, logger);
  return map;
}
```

#### 中间件（不稳定）

中间件在 `future.unstable_middleware` 标志后面实现。要启用，你必须在 `react-router.config.ts` 文件中启用标志和类型：

```ts
import type { Config } from "@react-router/dev/config";
import type { Future } from "react-router";

declare module "react-router" {
  interface Future {
    unstable_middleware: true; // 👈 启用中间件类型
  }
}

export default {
  future: {
    unstable_middleware: true, // 👈 启用中间件
  },
} satisfies Config;
```

⚠️ 中间件是不稳定的，不应在生产环境中采用。`clientMiddleware` 的路由模块加载中至少存在一个已知的性能退化，我们将在稳定发布之前解决此问题。

⚠️ 启用中间件包含对传递给 `loader`/`action` 函数的 `context` 参数的破坏性变更 - 更多信息请参阅下文。

启用后，路由可以定义一个中间件函数数组，这些函数将在路由处理器运行之前按顺序执行。 这些函数接受与 `loader`/`action` 相同的参数，外加一个用于运行剩余数据管线的 `next` 参数。这允许中间件在处理器执行前后执行逻辑。

```tsx
// Framework 模式
export const unstable_middleware = [serverLogger, serverAuth]; // server
export const unstable_clientMiddleware = [clientLogger]; // client

// 库模式
const routes = [
  {
    path: "/",
    // 库模式 SPA 的中间件在客户端运行
    unstable_middleware: [clientLogger, clientAuth],
    loader: rootLoader,
    Component: Root,
  },
];
```

以下是一个简单的客户端日志中间件示例，可以放置在根路由上：

```tsx
const clientLogger: Route.unstable_ClientMiddlewareFunction = async (
  { request },
  next,
) => {
  let start = performance.now();

  // 运行剩余的中间件和所有路由 loader
  await next();

  let duration = performance.now() - start;
  console.log(`Navigated to ${request.url} (${duration}ms)`);
};
```

注意在上面的示例中，`next`/`middleware` 函数不返回任何内容。这是设计使然，因为在客户端没有像服务器上运行的中间件那样需要通过网络发送的 "response"。数据全部由有状态的 `router` 在幕后处理。

对于服务器端中间件，`next` 函数将返回 React Router 将通过网络发送的 HTTP `Response`，从而让你有机会根据需要进行更改。你可以抛出新的响应来短路并立即响应，也可以返回新的或修改后的响应来覆盖 `next()` 返回的默认值。

```tsx
const serverLogger: Route.unstable_MiddlewareFunction = async (
  { request, params, context },
  next,
) => {
  let start = performance.now();

  // 👇 在这里获取响应
  let res = await next();

  let duration = performance.now() - start;
  console.log(`Navigated to ${request.url} (${duration}ms)`);

  // 👇 在这里返回（如果不修改响应则可选）
  return res;
};
```

You can throw a `redirect` from a middleware to short circuit any remaining processing:

```tsx
import { sessionContext } from "../context";
const serverAuth: Route.unstable_MiddlewareFunction = (
  { request, params, context },
  next,
) => {
  let session = context.get(sessionContext);
  let user = session.get("user");
  if (!user) {
    session.set("returnTo", request.url);
    throw redirect("/login", 302);
  }
};
```

_Note that in cases like this where you don't need to do any post-processing you don't need to call the `next` function or return a `Response`._

Here's another example of using a server middleware to detect 404s and check the CMS for a redirect:

```tsx
const redirects: Route.unstable_MiddlewareFunction = async ({
  request,
  next,
}) => {
  // 尝试处理请求
  let res = await next();

  // 如果是 404，检查 CMS 是否有重定向，最后执行
  // 因为这很耗时
  if (res.status === 404) {
    let cmsRedirect = await checkCMSRedirects(request.url);
    if (cmsRedirect) {
      throw redirect(cmsRedirect, 302);
    }
  }

  return res;
};
```

有关 `middleware` API/设计的更多信息，请参阅 [decision doc](https://github.com/remix-run/react-router/blob/release-next/decisions/0014-context-middleware.md).

##### 中间件 `context` 参数

启用中间件后，你的应用将在 loader 和 action 中使用不同类型的 `context` 参数以提供更好的类型安全。 Instead of `AppLoadContext`, `context` will now be an instance of `ContextProvider` that you can use with type-safe contexts (similar to `React.createContext`):

```ts
import { unstable_createContext } from "react-router";
import { Route } from "./+types/root";
import type { Session } from "./sessions.server";
import { getSession } from "./sessions.server";

let sessionContext = unstable_createContext<Session>();

const sessionMiddleware: Route.unstable_MiddlewareFunction = ({
  context,
  request,
}) => {
  let session = await getSession(request);
  context.set(sessionContext, session);
  //                          ^ must be of type Session
};

// ... 然后在某个下游中间件中
const loggerMiddleware: Route.unstable_MiddlewareFunction = ({
  context,
  request,
}) => {
  let session = context.get(sessionContext);
  //  ^ typeof Session
  console.log(session.get("userId"), request.method, request.url);
};

// ... 或某个下游 loader
export function loader({ context }: Route.LoaderArgs) {
  let session = context.get(sessionContext);
  let profile = await getProfile(session.get("userId"));
  return { profile };
}
```

If you are using a custom server with a `getLoadContext` function, the return value for initial context values passed from the server adapter layer is no longer an object and should now return an `unstable_InitialContext` (`Map<RouterContext, unknown>`):

```ts
let adapterContext = unstable_createContext<MyAdapterContext>();

function getLoadContext(req, res): unstable_InitialContext {
  let map = new Map();
  map.set(adapterContext, getAdapterContext(req));
  return map;
}
```

#### `unstable_SerializesTo`

`unstable_SerializesTo` added a way to register custom serialization types in Single Fetch for other library and framework authors like Apollo. It was implemented with branded type whose branded property that was made optional so that casting arbitrary values was easy:

```ts
// without the brand being marked as optional
let x1 = 42 as unknown as unstable_SerializesTo<number>;
//          ^^^^^^^^^^

// with the brand being marked as optional
let x2 = 42 as unstable_SerializesTo<number>;
```

However, this broke type inference in `loaderData` and `actionData` for any `Record` types as those would now (incorrectly) match `unstable_SerializesTo`. This affected all users, not just those that depended on `unstable_SerializesTo`. To fix this, the branded property of `unstable_SerializesTo` is marked as required instead of optional.

For library and framework authors using `unstable_SerializesTo`, you may need to add `as unknown` casts before casting to `unstable_SerializesTo`.

### 按包分类的变更

- [`create-react-router`](https://github.com/remix-run/react-router/blob/react-router%407.3.0/packages/create-react-router/CHANGELOG.md#730)
- [`react-router`](https://github.com/remix-run/react-router/blob/react-router%407.3.0/packages/react-router/CHANGELOG.md#730)
- [`@react-router/architect`](https://github.com/remix-run/react-router/blob/react-router%407.3.0/packages/react-router-architect/CHANGELOG.md#730)
- [`@react-router/cloudflare`](https://github.com/remix-run/react-router/blob/react-router%407.3.0/packages/react-router-cloudflare/CHANGELOG.md#730)
- [`@react-router/dev`](https://github.com/remix-run/react-router/blob/react-router%407.3.0/packages/react-router-dev/CHANGELOG.md#730)
- [`@react-router/express`](https://github.com/remix-run/react-router/blob/react-router%407.3.0/packages/react-router-express/CHANGELOG.md#730)
- [`@react-router/fs-routes`](https://github.com/remix-run/react-router/blob/react-router%407.3.0/packages/react-router-fs-routes/CHANGELOG.md#730)
- [`@react-router/node`](https://github.com/remix-run/react-router/blob/react-router%407.3.0/packages/react-router-node/CHANGELOG.md#730)
- [`@react-router/remix-config-routes-adapter`](https://github.com/remix-run/react-router/blob/react-router%407.3.0/packages/react-router-remix-config-routes-adapter/CHANGELOG.md#730)
- [`@react-router/serve`](https://github.com/remix-run/react-router/blob/react-router%407.3.0/packages/react-router-serve/CHANGELOG.md#730)

**完整更新日志**: [`v7.2.0...v7.3.0`](https://github.com/remix-run/react-router/compare/react-router@7.2.0...react-router@7.3.0)

## v7.2.0

日期：2025-02-18

### 重要变更

#### 类型安全的 `href` 工具函数

在框架模式下，我们现在为你提供了一个完全类型安全的 `href` 工具函数，为你的应用中的链接提供路径自动补全和参数验证的良好体验：

```tsx
import { href } from "react-router";

export default function Component() {
  const link = href("/blog/:slug", { slug: "my-first-post" });
  //                ^ type-safe!     ^ Also type-safe!

  return (
    <main>
      <Link to={href("/products/:id", { id: "asdf" })} />
      <NavLink to={href("/:lang?/about", { lang: "en" })} />
    </main>
  );
}
```

如果你传入了错误的路径值或参数值，现在将会得到类型错误：

```ts
const badPath = href("/not/a/valid/path");
//                   ^ Error!

const badParam = href("/blog/:slug", { oops: "bad param" });
//                                     ^ Error!
```

#### 带 SPA 回退的预渲染

本次发布增强了在使用 `ssr:false` 进行预渲染时，将预渲染路径与以"SPA 模式"运行的其他路径组合使用的能力。

- If you specify `ssr:false` without a `prerender` config, this is considered "SPA Mode" and the generated `index.html` file will only render down to the root route and will be able to hydrate for any valid application path
- If you specify `ssr:false` with a `prerender` config but _do not_ include the `/` path (i.e., `prerender: ['/blog/post']`), then we still generate a "SPA Mode" `index.html` file that can hydrate for any path in the application
- If you specify `ssr:false` and include the `/` path in your `prerender` config, the generated `index.html` file will be specific to the root index route, so we will now also generate a separate "SPA Mode" file in `__spa-fallback.html` that you can serve/hydrate for non-prerendered paths

For more info, see the [Pre-rendering](https://reactrouter.com/dev/how-to/pre-rendering#pre-rendering-with-a-spa-fallback) docs for more info.

#### 在 SPA 模式中允许根 `loader`

SPA 模式以前禁止在所有路由中使用 loader，以便我们可以为应用中的任何路径进行 hydrate。但是，由于根路由总是在构建时渲染，我们可以为根路由解除此限制。

为了在预渲染期间使用构建时的 loader 数据，我们现在还将 `loaderData` 作为可选 prop 暴露给路由上的 `HydrateFallback` 组件：

- This will be defined so long as the `HydrateFallback` is rendering because _children_ routes are loading
- This will be `undefined` if the `HydrateFallback` is rendering because the route itself has it's own hydrating `clientLoader`
  - In SPA mode, this will allow you to render loader root data into the SPA Mode HTML file

### 次要变更

- `react-router` - 新的类型安全 `href` 工具函数，保证链接指向应用中的实际路径 ([#13012](https://github.com/remix-run/react-router/pull/13012))
- `@react-router/dev` - 使用 `ssr:false` 预渲染 `/` 路由时生成 "SPA 回退" HTML 文件 ([#12948](https://github.com/remix-run/react-router/pull/12948))
- `@react-router/dev` - 允许 SPA 模式中根路由使用 `loader`，因为它可以在构建时被调用/服务器渲染 ([#12948](https://github.com/remix-run/react-router/pull/12948))
  - `Route.HydrateFallbackProps` 现在也接收 `loaderData`

### 补丁变更

- `react-router` - 为所有 `ssr:false` 应用禁用懒加载路由发现，而不仅仅是"SPA 模式"，因为没有运行时服务器来提供搜索参数配置的 `__manifest` 请求 ([#12894](https://github.com/remix-run/react-router/pull/12894))
  - We previously only disabled this for "SPA Mode" but we realized it should apply to all `ssr:false` apps
  - In those `prerender` scenarios we would pre-render the `/__manifest` file but that makes some unnecessary assumptions about the static file server behaviors
- `react-router` - 在 SPA 模式中不要应用 Single Fetch 重新验证的去优化，因为没有服务器 HTTP 请求 ([#12948](https://github.com/remix-run/react-router/pull/12948))
- `react-router` - 正确处理跨预渲染/SPA 边界的重新验证 ([#13021](https://github.com/remix-run/react-router/pull/13021))
  - In "hybrid" applications where some routes are pre-rendered and some are served from a SPA fallback, we need to avoid making `.data` requests if the path wasn't pre-rendered because the request will 404
  - We don't know all the pre-rendered paths client-side, however:
    - All `loader` data in `ssr:false` mode is static because it's generated at build time
    - A route must use a `clientLoader` to do anything dynamic
    - Therefore, if a route only has a `loader` and not a `clientLoader`, we disable revalidation by default because there is no new data to retrieve
    - We short circuit and skip single fetch `.data` request logic if there are no server loaders with `shouldLoad=true` in our single fetch `dataStrategy`
    - This ensures that the route doesn't cause a `.data` request that would 404 after a submission
- `react-router` - Align dev server behavior with static file server behavior when `ssr:false` is set ([#12948](https://github.com/remix-run/react-router/pull/12948))
  - When no `prerender` config exists, only SSR down to the root `HydrateFallback` (SPA Mode)
  - When a `prerender` config exists but the current path is not pre-rendered, only SSR down to the root `HydrateFallback` (SPA Fallback)
  - Return a 404 on `.data` requests to non-pre-rendered paths
- `react-router` - 改进 framework 模式中 CSS 副作用的预获取性能 ([#12889](https://github.com/remix-run/react-router/pull/12889))
- `react-router` - 正确处理懒加载路由发现中被中断的 manifest 请求 ([#12915](https://github.com/remix-run/react-router/pull/12915))
- `@react-router/dev` - 处理 Vite 配置中自定义的 `envDir` ([#12969](https://github.com/remix-run/react-router/pull/12969))
- `@react-router/dev` - 修复 CLI 解析以允许无参数的 `npx react-router` 使用 ([#12925](https://github.com/remix-run/react-router/pull/12925))
- `@react-router/dev` - 使用 `prerender:true` 时跳过仅有 action 的资源路由 ([#13004](https://github.com/remix-run/react-router/pull/13004))
- `@react-router/dev` - 增强使用 `ssr:false` 时的无效导出检测 ([#12948](https://github.com/remix-run/react-router/pull/12948))
  - `headers`/`action` functions are prohibited in all routes with `ssr:false` because there will be no runtime server on which to run them
  - `loader` functions are more nuanced and depend on whether a given route is prerendered
    - When using `ssr:false` without a `prerender` config, only the `root` route can have a `loader`
    - When using `ssr:false` with a `prerender` config, only routes matched by a `prerender` path can have a `loader`
- `@react-router/dev` - Error at build time in `ssr:false` + `prerender` apps for the edge case scenario of: ([#13021](https://github.com/remix-run/react-router/pull/13021))
  - A parent route has only a `loader` (does not have a `clientLoader`)
  - The parent route is pre-rendered
  - The parent route has children routes which are not prerendered
  - This means that when the child paths are loaded via the SPA fallback, the parent won't have any `loaderData` because there is no server on which to run the `loader`
  - This can be resolved by either adding a parent `clientLoader` or pre-rendering the child paths
  - If you add a `clientLoader`, calling the `serverLoader()` on non-prerendered paths will throw a 404
- `@react-router/dev` - 将预渲染资源路由的 `.data` 文件限制为仅目标路由 ([#13004](https://github.com/remix-run/react-router/pull/13004))
- `@react-router/dev` - 修复二进制文件的预渲染 ([#13039](https://github.com/remix-run/react-router/pull/13039))
- `@react-router/dev` - 修复重复参数的类型生成 ([#13012](https://github.com/remix-run/react-router/pull/13012))
  - In React Router, path parameters are keyed by their name, so for a path pattern like `/a/:id/b/:id?/c/:id`, the last `:id` will set the value for `id` in `useParams` and the `params` prop
    - For example, `/a/1/b/2/c/3` will result in the value `{ id: 3 }` at runtime
  - Previously, generated types for params incorrectly modeled repeated params with an array
    - For example, `/a/1/b/2/c/3` generated a type like `{ id: [1,2,3] }`.
  - To be consistent with runtime behavior, the generated types now correctly model the "last one wins" semantics of path parameters.
    - For example, `/a/1/b/2/c/3` now generates a type like `{ id: 3 }`.
- `@react-router/dev` - 修复 `react-router --version` 加载 `package.json` 的路径 ([#13012](https://github.com/remix-run/react-router/pull/13012))

### 不稳定变更

⚠️ _[不稳定特性](https://reactrouter.com/community/api-development-strategy#unstable-flags)不建议在生产环境中使用_

- `react-router` - 为库作者新增 `unstable_SerializesTo` 品牌类型，用于注册可通过 React Router 流式格式（`turbo-stream`）序列化的类型 ([#12264](https://github.com/remix-run/react-router/pull/12264))
- `@react-router/dev` - 通过 `future.unstable_splitRouteModules` 在 framework 模式中新增拆分路由模块的不稳定支持 ([#11871](https://github.com/remix-run/react-router/pull/11871))
- `@react-router/dev` - 新增 `future.unstable_viteEnvironmentApi` 标志以启用实验性 Vite Environment API 支持 ([#12936](https://github.com/remix-run/react-router/pull/12936))

#### 拆分路由模块（不稳定）

> ⚠️ This feature is currently [unstable](https://reactrouter.com/community/api-development-strategy#unstable-flags), enabled by the `future.unstable_splitRouteModules` flag. We’d love any interested users to play with it locally and provide feedback, but we do not recommend using it in production yet.
>
> If you do choose to adopt this flag in production, please ensure you do sufficient testing against your production build to ensure that the optimization is working as expected.

One of the conveniences of the [Route Module API](https://reactrouter.com/start/framework/route-module) is that everything a route needs is in a single file. Unfortunately this comes with a performance cost in some cases when using the `clientLoader`, `clientAction`, and `HydrateFallback` APIs.

As a basic example, consider this route module:

```tsx filename=routes/example.tsx
import { MassiveComponent } from "~/components";

export async function clientLoader() {
  return await fetch("https://example.com/api").then((response) =>
    response.json(),
  );
}

export default function Component({ loaderData }) {
  return <MassiveComponent data={loaderData} />;
}
```

In this example we have a minimal `clientLoader` export that makes a basic fetch call, whereas the default component export is much larger. This is a problem for performance because it means that if we want to navigate to this route client-side, the entire route module must be downloaded before the client loader can start running.

To visualize this as a timeline:

<docs-info>In the following timeline diagrams, different characters are used within the Route Module bars to denote the different Route Module APIs being exported.</docs-info>

```
Get Route Module:  |--=======|
Run clientLoader:            |-----|
Render:                            |-|
```

Instead, we want to optimize this to the following:

```
Get clientLoader:  |--|
Get Component:     |=======|
Run clientLoader:     |-----|
Render:                     |-|
```

To achieve this optimization, React Router will split the route module into multiple smaller modules during the production build process. In this case, we'll end up with two separate [virtual modules](https://vite.dev/guide/api-plugin#virtual-modules-convention) — one for the client loader and one for the component and its dependencies.

```tsx filename=routes/example.tsx?route-chunk=clientLoader
export async function clientLoader() {
  return await fetch("https://example.com/api").then((response) =>
    response.json(),
  );
}
```

```tsx filename=routes/example.tsx?route-chunk=main
import { MassiveComponent } from "~/components";

export default function Component({ loaderData }) {
  return <MassiveComponent data={loaderData} />;
}
```

> 💡 This optimization is automatically applied in framework mode, but you can also implement it in library mode via `route.lazy` and authoring your route in multiple files as covered in our blog post on [lazy loading route modules.](https://remix.run/blog/lazy-loading-routes#advanced-usage-and-optimizations)

Now that these are available as separate modules, the client loader and the component can be downloaded in parallel. This means that the client loader can be executed as soon as it's ready without having to wait for the component.

This optimization is even more pronounced when more Route Module APIs are used. For example, when using `clientLoader`, `clientAction` and `HydrateFallback`, the timeline for a single route module during a client-side navigation might look like this:

```
Get Route Module:     |--~~++++=======|
Run clientLoader:                     |-----|
Render:                                     |-|
```

This would instead be optimized to the following:

```
Get clientLoader:     |--|
Get clientAction:     |~~|
Get HydrateFallback:  SKIPPED
Get Component:        |=======|
Run clientLoader:        |-----|
Render:                        |-|
```

Note that this optimization only works when the Route Module APIs being split don't share code within the same file. For example, the following route module can't be split:

```tsx filename=routes/example.tsx
import { MassiveComponent } from "~/components";

const shared = () => console.log("hello");

export async function clientLoader() {
  shared();
  return await fetch("https://example.com/api").then((response) =>
    response.json(),
  );
}

export default function Component({ loaderData }) {
  shared();
  return <MassiveComponent data={loaderData} />;
}
```

This route will still work, but since both the client loader and the component depend on the `shared` function defined within the same file, it will be de-optimized into a single route module.

To avoid this, you can extract any code shared between exports into a separate file. For example:

```tsx filename=routes/example/shared.tsx
export const shared = () => console.log("hello");
```

You can then import this shared code in your route module without triggering the de-optimization:

```tsx filename=routes/example/route.tsx
import { MassiveComponent } from "~/components";
import { shared } from "./shared";

export async function clientLoader() {
  shared();
  return await fetch("https://example.com/api").then((response) =>
    response.json(),
  );
}

export default function Component({ loaderData }) {
  shared();
  return <MassiveComponent data={loaderData} />;
}
```

Since the shared code is in its own module, React Router is now able to split this route module into two separate virtual modules:

```tsx filename=routes/example/route.tsx?route-chunk=clientLoader
import { shared } from "./shared";

export async function clientLoader() {
  shared();
  return await fetch("https://example.com/api").then((response) =>
    response.json(),
  );
}
```

```tsx filename=routes/example/route.tsx?route-chunk=main
import { MassiveComponent } from "~/components";
import { shared } from "./shared";

export default function Component({ loaderData }) {
  shared();
  return <MassiveComponent data={loaderData} />;
}
```

If your project is particularly performance sensitive, you can set the `unstable_splitRouteModules` future flag to `"enforce"`:

```tsx filename=react-router-config.ts
export default {
  future: {
    unstable_splitRouteModules: "enforce",
  },
};
```

This setting will raise an error if any route modules can't be split:

```
Error splitting route module: routes/example/route.tsx

- clientLoader

This export could not be split into its own chunk because it shares code with other exports. You should extract any shared code into its own module and then import it within the route module.
```

### 按包分类的变更

- [`create-react-router`](https://github.com/remix-run/react-router/blob/react-router%407.2.0/packages/create-react-router/CHANGELOG.md#720)
- [`react-router`](https://github.com/remix-run/react-router/blob/react-router%407.2.0/packages/react-router/CHANGELOG.md#720)
- [`@react-router/architect`](https://github.com/remix-run/react-router/blob/react-router%407.2.0/packages/react-router-architect/CHANGELOG.md#720)
- [`@react-router/cloudflare`](https://github.com/remix-run/react-router/blob/react-router%407.2.0/packages/react-router-cloudflare/CHANGELOG.md#720)
- [`@react-router/dev`](https://github.com/remix-run/react-router/blob/react-router%407.2.0/packages/react-router-dev/CHANGELOG.md#720)
- [`@react-router/express`](https://github.com/remix-run/react-router/blob/react-router%407.2.0/packages/react-router-express/CHANGELOG.md#720)
- [`@react-router/fs-routes`](https://github.com/remix-run/react-router/blob/react-router%407.2.0/packages/react-router-fs-routes/CHANGELOG.md#720)
- [`@react-router/node`](https://github.com/remix-run/react-router/blob/react-router%407.2.0/packages/react-router-node/CHANGELOG.md#720)
- [`@react-router/remix-config-routes-adapter`](https://github.com/remix-run/react-router/blob/react-router%407.2.0/packages/react-router-remix-config-routes-adapter/CHANGELOG.md#720)
- [`@react-router/serve`](https://github.com/remix-run/react-router/blob/react-router%407.2.0/packages/react-router-serve/CHANGELOG.md#720)

**完整更新日志**: [`v7.1.5...v7.2.0`](https://github.com/remix-run/react-router/compare/react-router@7.1.5...react-router@7.2.0)

## v7.1.5

日期：2025-01-31

### 补丁变更

- `react-router` - 修复通过 [#12800](https://github.com/remix-run/react-router/pull/12800) 在 `7.1.4` 中引入的回归问题，该问题导致使用懒加载路由发现（`patchRoutesOnNavigation`）的应用在 splat 路由内导航到 hash 路由时出现问题 ([#12927](https://github.com/remix-run/react-router/pull/12927))

**完整更新日志**: [`v7.1.4...v7.1.5`](https://github.com/remix-run/react-router/compare/react-router@7.1.4...react-router@7.1.5)

## v7.1.4

日期：2025-01-30

### 补丁变更

- `@react-router/dev` - 使用 `unstable_optimizeDeps` future 标志时正确解析 Windows 文件路径以扫描 Vite 的依赖优化 ([#12637](https://github.com/remix-run/react-router/pull/12637))
- `@react-router/dev` - 修复使用自定义服务器时的预渲染 - 之前我们最终尝试导入用户的自定义服务器，而实际上我们想导入虚拟服务器构建模块 ([#12759](https://github.com/remix-run/react-router/pull/12759))
- `react-router` - 正确处理 single fetch 响应中不能有 body 的状态码（204 等） ([#12760](https://github.com/remix-run/react-router/pull/12760))
- `react-router` - 抛出 `data()` 结果时正确地将 headers 冒泡为 `errorHeaders` ([#12846](https://github.com/remix-run/react-router/pull/12846))
  - 如果也从 `headers` 返回，避免 `Set-Cookie` header 的重复
- `react-router` - 停止对返回原始字符串/对象的资源路由报错，而是将它们序列化为 `text/plain` 或 `application/json` 响应 ([#12848](https://github.com/remix-run/react-router/pull/12848))
  - This only applies when accessed as a resource route without the `.data` extension
  - When accessed from a Single Fetch `.data` request, they will still be encoded via `turbo-stream`
- `react-router` - 优化懒加载路由发现的路径发现，优先在 `body` 层级进行单次 `querySelectorAll` 调用而非在子树层级进行多次调用 ([#12731](https://github.com/remix-run/react-router/pull/12731))
- `react-router` - 通过在可能时跳过冗余的 `matchRoutes` 调用来优化路由匹配 ([#12800](https://github.com/remix-run/react-router/pull/12800), [#12882](https://github.com/remix-run/react-router/pull/12882))
- `react-router` - 内部重组以清理一些重复的路由模块类型 ([#12799](https://github.com/remix-run/react-router/pull/12799))

**完整更新日志**: [`v7.1.3...v7.1.4`](https://github.com/remix-run/react-router/compare/react-router@7.1.3...react-router@7.1.4)

## v7.1.3

日期：2025-01-17

### 补丁变更

- `@react-router/dev` - 修复 `reveal` 和 `routes` CLI 命令 ([#12745](https://github.com/remix-run/react-router/pull/12745))

**完整更新日志**: [`v7.1.2...v7.1.3`](https://github.com/remix-run/react-router/compare/react-router@7.1.2...react-router@7.1.3)

## v7.1.2

日期：2025-01-16

### 补丁变更

- `react-router` - 修复issue with fetcher data cleanup in the data layer on fetcher unmount ([#12681](https://github.com/remix-run/react-router/pull/12681))
- `react-router` - 不rely on `symbol` for filtering out `redirect` responses from loader data ([#12694](https://github.com/remix-run/react-router/pull/12694))
  - Previously, some projects were getting type checking errors like:
    ```ts
    error TS4058: Return type of exported function has or is using name 'redirectSymbol' from external module "node_modules/..." but cannot be named.
    ```
  - Now that `symbol`s are not used for the `redirect` response type, these errors should no longer be present
- `@react-router/dev` - 修复default external conditions in Vite v6 ([#12644](https://github.com/remix-run/react-router/pull/12644))
  - This fixes resolution issues with certain npm packages
- `@react-router/dev` - 修复mismatch in prerendering html/data files when path is missing a leading slash ([#12684](https://github.com/remix-run/react-router/pull/12684))
- `@react-router/dev` - Use `module-sync` server condition when enabled in the runtime. This fixes React context mismatches (e.g. `useHref() may be used only in the context of a <Router> component.`) during development on Node 22.10.0+ when using libraries that have a peer dependency on React Router ([#12729](https://github.com/remix-run/react-router/pull/12729))
- `@react-router/dev` - 修复`react-refresh` source maps ([#12686](https://github.com/remix-run/react-router/pull/12686))

**完整更新日志**: [`v7.1.1...v7.1.2`](https://github.com/remix-run/react-router/compare/react-router@7.1.1...react-router@7.1.2)

## v7.1.1

日期：2024-12-23

### 补丁变更

- `@react-router/dev` - 修复for a crash when optional args are passed to the CLI ([#12609](https://github.com/remix-run/react-router/pull/12609))

**完整更新日志**: [`v7.1.0...v7.1.1`](https://github.com/remix-run/react-router/compare/react-router@7.1.0...react-router@7.1.1)

## v7.1.0

日期：2024-12-20

### 次要变更

- Add support for Vite v6 ([#12469](https://github.com/remix-run/react-router/pull/12469))

### 补丁变更

- `react-router` - Throw unwrapped Single Fetch `redirect` to align with pre-Single Fetch behavior ([#12506](https://github.com/remix-run/react-router/pull/12506))
- `react-router` - Ignore redirects when inferring loader data types ([#12527](https://github.com/remix-run/react-router/pull/12527))
- `react-router` - 移除`<Link prefetch>` warning which suffers from false positives in a lazy route discovery world ([#12485](https://github.com/remix-run/react-router/pull/12485))
- `create-react-router` - 修复missing `fs-extra` dependency ([#12556](https://github.com/remix-run/react-router/pull/12556))
- `@react-router/dev`/`@react-router/serve` - Properly initialize `NODE_ENV` if not already set for compatibility with React 19 ([#12578](https://github.com/remix-run/react-router/pull/12578))
- `@react-router/dev` - 移除the leftover/unused `abortDelay` prop from `ServerRouter` and update the default `entry.server.tsx` to use the new `streamTimeout` value for Single Fetch ([#12478](https://github.com/remix-run/react-router/pull/12478))
  - The `abortDelay` functionality was removed in v7 as it was coupled to the `defer` implementation from Remix v2, but this removal of this prop was missed
  - If you were still using this prop in your `entry.server` file, it's likely your app is not aborting streams as you would expect and you will need to adopt the new [`streamTimeout`](https://reactrouter.com/explanation/special-files#streamtimeout) value introduced with Single Fetch
- `@react-router/fs-routes` - Throw error in `flatRoutes` if routes directory is missing ([#12407](https://github.com/remix-run/react-router/pull/12407))

### 按包分类的变更

- [`create-react-router`](https://github.com/remix-run/react-router/blob/react-router%407.1.0/packages/create-react-router/CHANGELOG.md#710)
- [`react-router`](https://github.com/remix-run/react-router/blob/react-router%407.1.0/packages/react-router/CHANGELOG.md#710)
- [`@react-router/architect`](https://github.com/remix-run/react-router/blob/react-router%407.1.0/packages/react-router-architect/CHANGELOG.md#710)
- [`@react-router/cloudflare`](https://github.com/remix-run/react-router/blob/react-router%407.1.0/packages/react-router-cloudflare/CHANGELOG.md#710)
- [`@react-router/dev`](https://github.com/remix-run/react-router/blob/react-router%407.1.0/packages/react-router-dev/CHANGELOG.md#710)
- [`@react-router/express`](https://github.com/remix-run/react-router/blob/react-router%407.1.0/packages/react-router-express/CHANGELOG.md#710)
- [`@react-router/fs-routes`](https://github.com/remix-run/react-router/blob/react-router%407.1.0/packages/react-router-fs-routes/CHANGELOG.md#710)
- [`@react-router/node`](https://github.com/remix-run/react-router/blob/react-router%407.1.0/packages/react-router-node/CHANGELOG.md#710)
- [`@react-router/remix-config-routes-adapter`](https://github.com/remix-run/react-router/blob/react-router%407.1.0/packages/react-router-remix-config-routes-adapter/CHANGELOG.md#710)
- [`@react-router/serve`](https://github.com/remix-run/react-router/blob/react-router%407.1.0/packages/react-router-serve/CHANGELOG.md#710)

**完整更新日志**: [`v7.0.2...v7.1.0`](https://github.com/remix-run/react-router/compare/react-router@7.0.2...react-router@7.1.0)

## v7.0.2

日期：2024-12-02

### 补丁变更

- `react-router` - Temporarily only use one build in export map so packages can have a peer dependency on react router ([#12437](https://github.com/remix-run/react-router/pull/12437))
- `@react-router/dev` - 支持`moduleResolution` `Node16` and `NodeNext` ([#12440](https://github.com/remix-run/react-router/pull/12440))
- `@react-router/dev` - 生成wide `matches` and `params` types for child routes ([#12397](https://github.com/remix-run/react-router/pull/12397))
  - At runtime, `matches` includes child route matches and `params` include child route path parameters
  - But previously, we only generated types for parent routes and the current route in `matches` and `params`
  - To align our generated types more closely to the runtime behavior, we now generate more permissive, wider types when accessing child route information

**完整更新日志**: [`v7.0.1...v7.0.2`](https://github.com/remix-run/react-router/compare/react-router@7.0.1...react-router@7.0.2)

## v7.0.1

日期：2024-11-22

### 补丁变更

- `@react-router/dev` - 确保typegen file watcher is cleaned up when Vite dev server restarts ([#12331](https://github.com/remix-run/react-router/pull/12331))
- `@react-router/dev` - Pass route `error` to `ErrorBoundary` as a prop ([#12338](https://github.com/remix-run/react-router/pull/12338))

**完整更新日志**: [`v7.0.0...v7.0.1`](https://github.com/remix-run/react-router/compare/react-router@7.0.0...react-router@7.0.1)

## v7.0.0

日期：2024-11-21

### 破坏性变更

#### 包重构

- The `react-router-dom`, `@remix-run/react`, `@remix-run/server-runtime`, and `@remix-run/router` have been collapsed into the `react-router` package
  - To ease migration, `react-router-dom` is still published in v7 as a re-export of everything from `react-router`
- The `@remix-run/cloudflare-pages` and `@remix-run/cloudflare-workers` have been collapsed into `@react-router/cloudflare` package`
- The `react-router-dom-v5-compat` and `react-router-native` packages are removed starting with v7

#### 移除了适配器重导出

Remix v2 used to re-export all common `@remix-run/server-runtime` APIs through the various runtime packages (`node`, `cloudflare`, `deno`) so that you wouldn't need an additional `@remix-run/server-runtime` dependency in your `package.json`. With the collapsing of packages into `react-router`, these common APIs are now no longer re-exported through the runtime adapters. You should import all common APIs from `react-router`, and only import runtime-specific APIs from the runtime packages:

```jsx
// Runtime-specific APIs
import { createFileSessionStorage } from "@react-router/node";
// Runtime-agnostic APIs
import { redirect, useLoaderData } from "react-router";
```

#### 移除的 API

以下 API 已在 React Router v7 中移除：

- `json`
- `defer`
- `unstable_composeUploadHandlers`
- `unstable_createMemoryUploadHandler`
- `unstable_parseMultipartFormData`

#### 最低版本

React Router v7 要求以下最低版本：

- `node@20`
  - React Router no longer provides an `installGlobals` method to [polyfill](https://reactrouter.com/dev/guides/deploying/custom-node#polyfilling-fetch) the `fetch` API
- `react@18`, `react-dom@18`

#### 采用的 Future Flag 行为

Remix and React Router follow an [API Development Strategy](https://reactrouter.com/en/main/guides/api-development-strategy) leveraging "Future Flags" to avoid introducing a slew of breaking changes in a major release. Instead, breaking changes are introduced in minor releases behind a flag, allowing users to opt-in at their convenience. In the next major release, all future flag behaviors become the default behavior.

以下之前标记的行为现在是 React Router v7 中的默认值：

- [React Router v6 flags](https://reactrouter.com/en/v6/upgrading/future)
  - `future.v7_relativeSplatPath`
  - `future.v7_startTransition`
  - `future.v7_fetcherPersist`
  - `future.v7_normalizeFormMethod`
  - `future.v7_partialHydration`
  - `future.v7_skipActionStatusRevalidation`
- [Remix v2 flags](https://remix.run/docs/en/v2/start/future-flags)
  - `future.v3_fetcherPersist`
  - `future.v3_relativeSplatPath`
  - `future.v3_throwAbortReason`
  - `future.v3_singleFetch`
  - `future.v3_lazyRouteDiscovery`
  - `future.v3_optimizeDeps`

#### Vite 编译器

The [Remix Vite plugin](https://remix.run/docs/en/2.12.1/start/future-flags#vite-plugin) is the proper way to build full-stack SSR apps using React Router v7. The former `esbuild`-based compiler is no longer available.

**Renamed `vitePlugin` and `cloudflareDevProxyVitePlugin`**

For Remix consumers migrating to React Router, the `vitePlugin` and `cloudflareDevProxyVitePlugin` exports have been renamed and moved ([#11904](https://github.com/remix-run/react-router/pull/11904))

```diff
-import {
-  vitePlugin as remix,
-  cloudflareDevProxyVitePlugin,
-} from "@remix/dev";

+import { reactRouter } from "@react-router/dev/vite";
+import { cloudflareDevProxy } from "@react-router/dev/vite/cloudflare";
```

**Removed `manifest` option**

For Remix consumers migrating to React Router, the Vite plugin's `manifest` option has been removed. The `manifest` option been superseded by the more powerful `buildEnd` hook since it's passed the `buildManifest` argument. You can still write the build manifest to disk if needed, but you'll most likely find it more convenient to write any logic depending on the build manifest within the `buildEnd` hook itself. ([#11573](https://github.com/remix-run/react-router/pull/11573))

If you were using the `manifest` option, you can replace it with a `buildEnd` hook that writes the manifest to disk like this:

```js
// react-router.config.ts
import { type Config } from "@react-router/dev/config";
import { writeFile } from "node:fs/promises";

export default {
  async buildEnd({ buildManifest }) {
    await writeFile(
      "build/manifest.json",
      JSON.stringify(buildManifest, null, 2),
      "utf-8"
    );
  },
} satisfies Config;
```

#### 暴露的路由器 Promise

Because React 19 will have first-class support for handling promises in the render pass (via `React.use` and `useAction`), we are now comfortable exposing the promises for the APIs that previously returned `undefined`:

- `useNavigate()`
- `useSubmit()`
- `useFetcher().load`
- `useFetcher().submit`
- `useRevalidator().revalidate()`

### 其他重要变更

#### `routes.ts`

When using the React Router Vite plugin, routes are defined in `app/routes.ts`. Route config is exported via the `routes` export, conforming to the `RouteConfig` type. Route helper functions `route`, `index`, and `layout` are provided to make declarative type-safe route definitions easier.

```ts
// app/routes.ts
import {
  type RouteConfig,
  route,
  index,
  layout,
} from "@react-router/dev/routes";

export const routes: RouteConfig = [
  index("./home.tsx"),
  route("about", "./about.tsx"),

  layout("./auth/layout.tsx", [
    route("login", "./auth/login.tsx"),
    route("register", "./auth/register.tsx"),
  ]),

  route("concerts", [
    index("./concerts/home.tsx"),
    route(":city", "./concerts/city.tsx"),
    route("trending", "./concerts/trending.tsx"),
  ]),
];
```

For Remix consumers migrating to React Router, you can still configure file system routing within `routes.ts` using the `@react-router/fs-routes` package. A minimal route config that reproduces the default Remix setup looks like this:

```ts
// app/routes.ts
import { type RouteConfig } from "@react-router/dev/routes";
import { flatRoutes } from "@react-router/fs-routes";

export const routes: RouteConfig = flatRoutes();
```

If you want to migrate from file system routing to config-based routes, you can mix and match approaches by spreading the results of the async `flatRoutes` function into the array of config-based routes.

```ts
// app/routes.ts
import { type RouteConfig, route } from "@react-router/dev/routes";
import { flatRoutes } from "@react-router/fs-routes";

export const routes: RouteConfig = [
  // Example config-based route:
  route("/hello", "./routes/hello.tsx"),

  // File system routes scoped to a different directory:
  ...(await flatRoutes({
    rootDirectory: "fs-routes",
  })),
];
```

If you were using Remix's `routes` option to use alternative file system routing conventions, you can adapt these to the new `RouteConfig` format using `@react-router/remix-config-routes-adapter`.

For example, if you were using [Remix v1 route conventions](https://remix.run/docs/en/1.19.3/file-conventions/routes-files) in Remix v2, you can combine `@react-router/remix-config-routes-adapter` with `@remix-run/v1-route-convention` to adapt this to React Router:

```ts
// app/routes.ts
import { type RouteConfig } from "@react-router/dev/routes";
import { remixConfigRoutes } from "@react-router/remix-config-routes-adapter";
import { createRoutesFromFolders } from "@remix-run/v1-route-convention";

export const routes: RouteConfig = remixConfigRoutes(async (defineRoutes) => {
  return createRoutesFromFolders(defineRoutes, {
    ignoredFilePatterns: ["**/.*", "**/*.css"],
  });
});
```

Also note that, if you were using Remix's `routes` option to define config-based routes, you can also adapt these to the new `RouteConfig` format using `@react-router/remix-config-routes-adapter` with minimal code changes. While this makes for a fast migration path, we recommend migrating any config-based routes from Remix to the new `RouteConfig` format since it's a fairly straightforward migration.

```diff
// app/routes.ts
-import { type RouteConfig } from "@react-router/dev/routes";
+import { type RouteConfig, route } from "@react-router/dev/routes";
-import { remixConfigRoutes } from "@react-router/remix-config-routes-adapter";

-export const routes: RouteConfig = remixConfigRoutes(async (defineRoutes) => {
-  defineRoutes((route) => {
-    route("/parent", "./routes/parent.tsx", () => [
-      route("/child", "./routes/child.tsx"),
-    ]);
-  });
-});
+export const routes: RouteConfig = [
+  route("/parent", "./routes/parent.tsx", [
+    route("/child", "./routes/child.tsx"),
+  ]),
+];
```

#### 类型安全改进

React Router now generates types for each of your route modules and passes typed props to route module component exports ([#11961](https://github.com/remix-run/react-router/pull/11961), [#12019](https://github.com/remix-run/react-router/pull/12019)). You can access those types by importing them from `./+types/<route filename without extension>`.

See [_How To > Route Module Type Safety_](https://reactrouter.com/dev/how-to/route-module-type-safety) and [_Explanations > Type Safety_](https://reactrouter.com/dev/explanation/type-safety) for more details.

#### 预渲染

React Router v7 includes a new `prerender` config in the vite plugin to support SSG use-cases. This will pre-render your `.html` and `.data` files at build time and so you can serve them statically at runtime from a running server or a CDN ([#11539](https://github.com/remix-run/react-router/pull/11539))

```ts
export default defineConfig({
  plugins: [
    reactRouter({
      async prerender({ getStaticPaths }) {
        let slugs = await fakeGetSlugsFromCms();
        return [
          ...getStaticPaths(),
          ...slugs.map((slug) => `/product/${slug}`),
        ];
      },
    }),
    tsconfigPaths(),
  ],
});

async function fakeGetSlugsFromCms() {
  await new Promise((r) => setTimeout(r, 1000));
  return ["shirt", "hat"];
}
```

### 主要变更 (`react-router`)

- Remove the original `defer` implementation in favor of using raw promises via single fetch and `turbo-stream` ([#11744](https://github.com/remix-run/react-router/pull/11744))
  - This removes these exports from React Router:
    - `defer`
    - `AbortedDeferredError`
    - `type TypedDeferredData`
    - `UNSAFE_DeferredData`
    - `UNSAFE_DEFERRED_SYMBOL`
- Collapse packages into `react-router`([#11505](https://github.com/remix-run/react-router/pull/11505))
  - `@remix-run/router`
  - `react-router-dom`
  - `@remix-run/server-runtime`
  - `@remix-run/testing`
  - As a note, the `react-router-dom` package is maintained to ease adoption but it simply re-exports all APIs from `react-router`
- Drop support for Node 16, React Router SSR now requires Node 18 or higher ([#11391](https://github.com/remix-run/react-router/pull/11391), [#11690](https://github.com/remix-run/react-router/pull/11690))
- Remove `future.v7_startTransition` flag ([#11696](https://github.com/remix-run/react-router/pull/11696))
- Expose the underlying router promises from the following APIs for composition in React 19 APIs: ([#11521](https://github.com/remix-run/react-router/pull/11521))
- Remove `future.v7_normalizeFormMethod` future flag ([#11697](https://github.com/remix-run/react-router/pull/11697))
- Imports/Exports cleanup ([#11840](https://github.com/remix-run/react-router/pull/11840))
  - Removed the following exports that were previously public API from `@remix-run/router`
    - types
      - `AgnosticDataIndexRouteObject`
      - `AgnosticDataNonIndexRouteObject`
      - `AgnosticDataRouteMatch`
      - `AgnosticDataRouteObject`
      - `AgnosticIndexRouteObject`
      - `AgnosticNonIndexRouteObject`
      - `AgnosticRouteMatch`
      - `AgnosticRouteObject`
      - `TrackedPromise`
      - `unstable_AgnosticPatchRoutesOnMissFunction`
      - `Action` -> exported as `NavigationType` via `react-router`
      - `Router` exported as `RemixRouter` to differentiate from RR's `<Router>`
    - API
      - `getToPathname` (`@private`)
      - `joinPaths` (`@private`)
      - `normalizePathname` (`@private`)
      - `resolveTo` (`@private`)
      - `stripBasename` (`@private`)
      - `createBrowserHistory` -> in favor of `createBrowserRouter`
      - `createHashHistory` -> in favor of `createHashRouter`
      - `createMemoryHistory` -> in favor of `createMemoryRouter`
      - `createRouter`
      - `createStaticHandler` -> in favor of wrapper `createStaticHandler` in RR Dom
      - `getStaticContextFromError`
  - Removed the following exports that were previously public API from `react-router`
    - `Hash`
    - `Pathname`
    - `Search`
- Remove `future.v7_prependBasename` from the internalized `@remix-run/router` package ([#11726](https://github.com/remix-run/react-router/pull/11726))
- Remove `future.v7_throwAbortReason` from internalized `@remix-run/router` package ([#11728](https://github.com/remix-run/react-router/pull/11728))
- Add `exports` field to all packages ([#11675](https://github.com/remix-run/react-router/pull/11675))
- Renamed `RemixContext` to `FrameworkContext` ([#11705](https://github.com/remix-run/react-router/pull/11705))
- Update the minimum React version to 18 ([#11689](https://github.com/remix-run/react-router/pull/11689))
- `PrefetchPageDescriptor` replaced by `PageLinkDescriptor` ([#11960](https://github.com/remix-run/react-router/pull/11960))
- Remove the `future.v7_partialHydration` flag ([#11725](https://github.com/remix-run/react-router/pull/11725))
  - This also removes the `<RouterProvider fallbackElement>` prop
    - To migrate, move the `fallbackElement` to a `hydrateFallbackElement`/`HydrateFallback` on your root route
  - Also worth nothing there is a related breaking changer with this future flag:
    - Without `future.v7_partialHydration` (when using `fallbackElement`), `state.navigation` was populated during the initial load
    - With `future.v7_partialHydration`, `state.navigation` remains in an `"idle"` state during the initial load
- Remove `future.v7_relativeSplatPath` future flag ([#11695](https://github.com/remix-run/react-router/pull/11695))
- Remove remaining future flags ([#11820](https://github.com/remix-run/react-router/pull/11820))
  - React Router `v7_skipActionErrorRevalidation`
  - Remix `v3_fetcherPersist`, `v3_relativeSplatPath`, `v3_throwAbortReason`
- Rename `createRemixStub` to `createRoutesStub` ([#11692](https://github.com/remix-run/react-router/pull/11692))
- Remove `@remix-run/router` deprecated `detectErrorBoundary` option in favor of `mapRouteProperties` ([#11751](https://github.com/remix-run/react-router/pull/11751))
- Add `react-router/dom` subpath export to properly enable `react-dom` as an optional `peerDependency` ([#11851](https://github.com/remix-run/react-router/pull/11851))
  - This ensures that we don't blindly `import ReactDOM from "react-dom"` in `<RouterProvider>` in order to access `ReactDOM.flushSync()`, since that would break `createMemoryRouter` use cases in non-DOM environments
  - DOM environments should import from `react-router/dom` to get the proper component that makes `ReactDOM.flushSync()` available:
    - If you are using the Vite plugin, use this in your `entry.client.tsx`:
      - `import { HydratedRouter } from 'react-router/dom'`
    - If you are not using the Vite plugin and are manually calling `createBrowserRouter`/`createHashRouter`:
      - `import { RouterProvider } from "react-router/dom"`
- Remove `future.v7_fetcherPersist` flag ([#11731](https://github.com/remix-run/react-router/pull/11731))
- Allow returning `undefined` from loaders and actions ([#11680](https://github.com/remix-run/react-router/pull/11680), [#12057]([https://github.com/remix-run/react-router/pull/1205))
- Use `createRemixRouter`/`RouterProvider` in `entry.client` instead of `RemixBrowser` ([#11469](https://github.com/remix-run/react-router/pull/11469))
- Remove the deprecated `json` utility ([#12146](https://github.com/remix-run/react-router/pull/12146))
  - You can use [`Response.json`](https://developer.mozilla.org/en-US/docs/Web/API/Response/json_static) if you still need to construct JSON responses in your app

### 主要变更 (`@react-router/*`)

- Remove `future.v3_singleFetch` flag ([#11522](https://github.com/remix-run/react-router/pull/11522))
- Drop support for Node 16 and 18, update minimum Node version to 20 ([#11690](https://github.com/remix-run/react-router/pull/11690), [#12171](https://github.com/remix-run/react-router/pull/12171))
  - Remove `installGlobals()` as this should no longer be necessary
- Add `exports` field to all packages ([#11675](https://github.com/remix-run/react-router/pull/11675))
- No longer re-export APIs from `react-router` through different runtime/adapter packages ([#11702](https://github.com/remix-run/react-router/pull/11702))
- For Remix consumers migrating to React Router, the `crypto` global from the [Web Crypto API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API) is now required when using cookie and session APIs
  - This means that the following APIs are provided from `react-router` rather than platform-specific packages: ([#11837](https://github.com/remix-run/react-router/pull/11837))
    - `createCookie`
    - `createCookieSessionStorage`
    - `createMemorySessionStorage`
    - `createSessionStorage`
  - For consumers running older versions of Node, the `installGlobals` function from `@remix-run/node` has been updated to define `globalThis.crypto`, using [Node's `require('node:crypto').webcrypto` implementation](https://nodejs.org/api/webcrypto.html)
  - Since platform-specific packages no longer need to implement this API, the following low-level APIs have been removed:
    - `createCookieFactory`
    - `createSessionStorageFactory`
    - `createCookieSessionStorageFactory`
    - `createMemorySessionStorageFactory`
- Consolidate types previously duplicated across `@remix-run/router`, `@remix-run/server-runtime`, and `@remix-run/react` now that they all live in `react-router` ([#12177](https://github.com/remix-run/react-router/pull/12177))
  - Examples: `LoaderFunction`, `LoaderFunctionArgs`, `ActionFunction`, `ActionFunctionArgs`, `DataFunctionArgs`, `RouteManifest`, `LinksFunction`, `Route`, `EntryRoute`
  - The `RouteManifest` type used by the "remix" code is now slightly stricter because it is using the former `@remix-run/router` `RouteManifest`
    - `Record<string, Route> -> Record<string, Route | undefined>`
  - Removed `AppData` type in favor of inlining `unknown` in the few locations it was used
  - Removed `ServerRuntimeMeta*` types in favor of the `Meta*` types they were duplicated from
- Migrate Remix v2 type generics to React Router ([#12180](https://github.com/remix-run/react-router/pull/12180))
  - These generics are provided for Remix v2 migration purposes
  - These generics and the APIs they exist on should be considered informally deprecated in favor of the new `Route.*` types
  - Anyone migrating from React Router v6 should probably not leverage these new generics and should migrate straight to the `Route.*` types
  - For React Router v6 users, these generics are new and should not impact your app, with one exception
    - `useFetcher` previously had an optional generic (used primarily by Remix v2) that expected the data type
    - This has been updated in v7 to expect the type of the function that generates the data (i.e., `typeof loader`/`typeof action`)
    - Therefore, you should update your usages:
      - ❌ `useFetcher<LoaderData>()`
      - ✅ `useFetcher<typeof loader>()`
- Update `cookie` dependency to `^1.0.1` - please see the [release notes](https://github.com/jshttp/cookie/releases) for any breaking changes ([#12172](https://github.com/remix-run/react-router/pull/12172))
- `@react-router/cloudflare` - For Remix consumers migrating to React Router, all exports from `@remix-run/cloudflare-pages` are now provided for React Router consumers in the `@react-router/cloudflare` package. There is no longer a separate package for Cloudflare Pages. ([#11801](https://github.com/remix-run/react-router/pull/11801))
- `@react-router/cloudflare` - The `@remix-run/cloudflare-workers` package has been deprecated. Remix consumers migrating to React Router should use the `@react-router/cloudflare` package directly. For guidance on how to use `@react-router/cloudflare` within a Cloudflare Workers context, refer to the Cloudflare Workers template. ([#11801](https://github.com/remix-run/react-router/pull/11801))
- `@react-router/dev` - For Remix consumers migrating to React Router, the `vitePlugin` and `cloudflareDevProxyVitePlugin` exports have been renamed and moved. ([#11904](https://github.com/remix-run/react-router/pull/11904))
- `@react-router/dev` - For Remix consumers migrating to React Router who used the Vite plugin's `buildEnd` hook, the resolved `reactRouterConfig` object no longer contains a `publicPath` property since this belongs to Vite, not React Router ([#11575](https://github.com/remix-run/react-router/pull/11575))
- `@react-router/dev` - For Remix consumers migrating to React Router, the Vite plugin's `manifest` option has been removed ([#11573](https://github.com/remix-run/react-router/pull/11573))
- `@react-router/dev` - 更新默认 `isbot` 版本至 v5 并放弃 `isbot@3` 支持 ([#11770](https://github.com/remix-run/react-router/pull/11770))
  - If you have `isbot@4` or `isbot@5` in your `package.json`:
    - You do not need to make any changes
  - If you have `isbot@3` in your `package.json` and you have your own `entry.server.tsx` file in your repo
    - You do not need to make any changes
    - You can upgrade to `isbot@5` independent of the React Router v7 upgrade
  - If you have `isbot@3` in your `package.json` and you do not have your own `entry.server.tsx` file in your repo
    - You are using the internal default entry provided by React Router v7 and you will need to upgrade to `isbot@5` in your `package.json`
- `@react-router/dev` - For Remix consumers migrating to React Router, Vite manifests (i.e. `.vite/manifest.json`) are now written within each build subdirectory, e.g. `build/client/.vite/manifest.json` and `build/server/.vite/manifest.json` instead of `build/.vite/client-manifest.json` and `build/.vite/server-manifest.json`. This means that the build output is now much closer to what you'd expect from a typical Vite project. ([#11573](https://github.com/remix-run/react-router/pull/11573))
  - Originally the Remix Vite plugin moved all Vite manifests to a root-level `build/.vite` directory to avoid accidentally serving them in production, particularly from the client build. This was later improved with additional logic that deleted these Vite manifest files at the end of the build process unless Vite's `build.manifest` had been enabled within the app's Vite config. This greatly reduced the risk of accidentally serving the Vite manifests in production since they're only present when explicitly asked for. As a result, we can now assume that consumers will know that they need to manage these additional files themselves, and React Router can safely generate a more standard Vite build output.

### 次要变更

- `react-router` - 将参数、loader 数据和 action 数据作为路由组件导出的 props ([#11961](https://github.com/remix-run/react-router/pull/11961))
- `react-router` - 新增路由模块类型生成 ([#12019](https://github.com/remix-run/react-router/pull/12019))
- `react-router` - 移除重复的 `RouterProvider` 实现 ([#11679](https://github.com/remix-run/react-router/pull/11679))
- `react-router` - 稳定化 `unstable_dataStrategy` ([#11969](https://github.com/remix-run/react-router/pull/11969))
- `react-router` - 稳定化 `unstable_patchRoutesOnNavigation` ([#11970](https://github.com/remix-run/react-router/pull/11970))
- `react-router` - 使用 Remix SSR 时为 `Link`/`NavLink` 新增预获取支持 ([#11402](https://github.com/remix-run/react-router/pull/11402))
- `react-router` - 增强 `ScrollRestoration`，使其在 SSR 的文档加载时可以正确恢复 ([#11401](https://github.com/remix-run/react-router/pull/11401))
- `@react-router/dev` - 在 React Router vite 插件中新增 `prerender` 配置支持，以支持现有的 SSG 用例 ([#11539](https://github.com/remix-run/react-router/pull/11539))
- `@react-router/dev` - 移除与 Single Fetch 异步 hydration 方法不兼容的内部 `entry.server.spa.tsx` 实现 ([#11681](https://github.com/remix-run/react-router/pull/11681))
- `@react-router/serve`: Update `express.static` configurations to support new `prerender` API ([#11547](https://github.com/remix-run/react-router/pull/11547))
  - Assets in the `build/client/assets` folder are served as before, with a 1-year immutable `Cache-Control` header
  - Static files outside of assets, such as pre-rendered `.html` and `.data` files are not served with a specific `Cache-Control` header
  - `.data` files are served with `Content-Type: text/x-turbo`
    - For some reason, when adding this via `express.static`, it seems to also add a `Cache-Control: public, max-age=0` to `.data` files

### 补丁变更

- 将 `substr` 替换为 `substring` ([#12080](https://github.com/remix-run/react-router/pull/12080))
- `react-router` - 修复使用 `data()` 从 loader/action 返回的重定向 ([#12021](https://github.com/remix-run/react-router/pull/12021))
- `@react-router/dev` - 为资源路由启用预渲染 ([#12200](https://github.com/remix-run/react-router/pull/12200))
- `@react-router/dev` - 相对于扁平输出文件结构解析配置目录 ([#12187](https://github.com/remix-run/react-router/pull/12187))

### 按包分类的变更

- [`react-router`](https://github.com/remix-run/react-router/blob/react-router%407.0.0/packages/react-router/CHANGELOG.md#700)
- [`@react-router/architect`](https://github.com/remix-run/react-router/blob/react-router%407.0.0/packages/react-router-architect/CHANGELOG.md#700)
- [`@react-router/cloudflare`](https://github.com/remix-run/react-router/blob/react-router%407.0.0/packages/react-router-cloudflare/CHANGELOG.md#700)
- [`@react-router/dev`](https://github.com/remix-run/react-router/blob/react-router%407.0.0/packages/react-router-dev/CHANGELOG.md#700)
- [`@react-router/express`](https://github.com/remix-run/react-router/blob/react-router%407.0.0/packages/react-router-express/CHANGELOG.md#700)
- [`@react-router/fs-routes`](https://github.com/remix-run/react-router/blob/react-router%407.0.0/packages/react-router-fs-routes/CHANGELOG.md#700)
- [`@react-router/node`](https://github.com/remix-run/react-router/blob/react-router%407.0.0/packages/react-router-node/CHANGELOG.md#700)
- [`@react-router/remix-config-routes-adapter`](https://github.com/remix-run/react-router/blob/react-router%407.0.0/packages/react-router-remix-config-routes-adapter/CHANGELOG.md#700)
- [`@react-router/serve`](https://github.com/remix-run/react-router/blob/react-router%407.0.0/packages/react-router-serve/CHANGELOG.md#700)

**完整更新日志**: [`v6.28.0...v7.0.0`](https://github.com/remix-run/react-router/compare/react-router@6.28.0...react-router@7.0.0)

# React Router v6 Releases

## v6.30.3

日期：2026-01-07

### 安全通知

本次发布修复了 1 个安全漏洞：

- [通过开放重定向的 XSS](https://github.com/remix-run/react-router/security/advisories/GHSA-2w69-qvjg-hvjx)

### 补丁变更

- 验证重定向位置 ([#14707](https://github.com/remix-run/react-router/pull/14707))

**完整更新日志**: [`v6.30.2...v6.30.3`](https://github.com/remix-run/react-router/compare/react-router@6.30.2...react-router@6.30.3)

## v6.30.2

日期：2025-11-13

### 安全通知

本次发布修复了 1 个安全漏洞：

- [通过不可信路径的意外外部重定向](https://github.com/remix-run/react-router/security/advisories/GHSA-9jcx-v3wj-wh4m)

### 补丁变更

- 规范化 `resolvePath` 中的双斜杠 ([#14537](https://github.com/remix-run/react-router/pull/14537))

**完整更新日志**: [`v6.30.1...v6.30.2`](https://github.com/remix-run/react-router/compare/react-router@6.30.1...react-router@6.30.2)

## v6.30.1

日期：2025-05-20

### 补丁变更

- 部分恢复 `6.29.0` 中添加的减少 `matchRoutes` 调用的优化，因为它暴露了其他问题 ([#13623](https://github.com/remix-run/react-router/pull/13623))
- 当 `v7_relativeSplatPath` 设为 `false` 时停止记录无效警告 ([#13502](https://github.com/remix-run/react-router/pull/13502))

**完整更新日志**: [`v6.30.0...v6.30.1`](https://github.com/remix-run/react-router/compare/react-router@6.30.0...react-router@6.30.1)

## v6.30.0

日期：2025-02-27

### 次要变更

- 为 `patchRoutesOnNavigation` 新增 `fetcherKey` 参数 ([#13109](https://github.com/remix-run/react-router/pull/13109))

### 补丁变更

- 修复通过 [#12169](https://github.com/remix-run/react-router/pull/12169) 在 `6.29.0` 中引入的回归问题，该问题导致使用懒加载路由发现（`patchRoutesOnNavigation`）的应用在 splat 路由内导航到 hash 路由时出现问题 ([#13108](https://github.com/remix-run/react-router/pull/13108))

**完整更新日志**: [`v6.29.0...v6.30.0`](https://github.com/remix-run/react-router/compare/react-router@6.29.0...react-router@6.30.0)

## v6.29.0

日期：2025-01-30

### 次要变更

- 将请求 `signal` 作为参数提供给 `patchRoutesOnNavigation` ([#12900](https://github.com/remix-run/react-router/pull/12900))
  - 如果正在进行的导航/fetcher 被中止，可以使用此参数中止任何 manifest 获取

### 补丁变更

- 不要在生产构建中记录 v7 弃用警告 ([#12794](https://github.com/remix-run/react-router/pull/12794))
- 抛出 `data()` 结果时正确冒泡 headers ([#12845](https://github.com/remix-run/react-router/pull/12845))
- 通过在可能时跳过冗余的 `matchRoutes` 调用来优化路由匹配 ([#12169](https://github.com/remix-run/react-router/pull/12169))
- 为 fetcher 调用从 `patchRoutesOnNavigation` 的 `path` 参数中剥离搜索参数 ([#12899](https://github.com/remix-run/react-router/pull/12899))

**完整更新日志**: [`v6.28.2...v6.29.0`](https://github.com/remix-run/react-router/compare/react-router@6.28.2...react-router@6.29.0)

## v6.28.2

日期：2025-01-16

### 补丁变更

- 修复未选择 `future.v7_fetcherPersist` 时手动 fetcher `key` 的使用 ([#12674](https://github.com/remix-run/react-router/pull/12674))
- 修复 fetcher 卸载时数据层中 fetcher 数据清理的问题 ([#12674](https://github.com/remix-run/react-router/pull/12674))

**完整更新日志**: [`v6.28.1...v6.28.2`](https://github.com/remix-run/react-router/compare/react-router@6.28.1...react-router@6.28.2)

## v6.28.1

日期：2024-12-20

### 补丁变更

- 允许用户通过将标志设为 `false` 来选择退出 v7 弃用警告 ([#12441](https://github.com/remix-run/react-router/pull/12441))

**完整更新日志**: [`v6.28.0...v6.28.1`](https://github.com/remix-run/react-router/compare/react-router@6.28.0...react-router@6.28.1)

## v6.28.0

日期：2024-11-06

### 重要变更

- 为准备 v7，我们为你尚未选择的所有 future 标志添加了弃用警告。请使用这些标志以更好地为最终升级到 v7 做准备。

### 次要变更

- 为 v7 标志记录弃用警告 ([#11750](https://github.com/remix-run/react-router/pull/11750))
  - 为 `json`/`defer` 添加弃用警告，推荐返回原始对象
    - 这些方法将在 React Router v7 中移除

### 补丁变更

- 为新的网站结构更新 JSDoc URL（添加 /v6/ 段） ([#12141](https://github.com/remix-run/react-router/pull/12141))

**完整更新日志**: [`v6.27.0...v6.28.0`](https://github.com/remix-run/react-router/compare/react-router@6.27.0...react-router@6.28.0)

## v6.27.0

日期：2024-10-11

### 重要变更

#### 稳定化的 API

本次发布稳定化了一些"不稳定"的 API，为[即将发布](https://x.com/remix_run/status/1841926034868077009)的 React Router v7 做准备（更多信息请参阅[这些](https://remix.run/blog/merging-remix-and-react-router)[文章](https://remix.run/blog/incremental-path-to-react-19)）：

- `unstable_dataStrategy` → `dataStrategy` (`createBrowserRouter` and friends) ([Docs](https://reactrouter.com/v6/routers/create-browser-router#optsdatastrategy))
- `unstable_patchRoutesOnNavigation` → `patchRoutesOnNavigation` (`createBrowserRouter` and friends) ([Docs](https://reactrouter.com/v6/routers/create-browser-router#optspatchroutesonnavigation))
- `unstable_flushSync` → `flushSync` (`useSubmit`, `fetcher.load`, `fetcher.submit`) ([Docs](https://reactrouter.com/v6/hooks/use-submit#optionsflushsync))
- `unstable_viewTransition` → `viewTransition` (`<Link>`, `<Form>`, `useNavigate`, `useSubmit`) ([Docs](https://reactrouter.com/v6/components/link#viewtransition))

### 次要变更

- 稳定化导航和 fetcher 的 `unstable_flushSync` 选项 ([#11989](https://github.com/remix-run/react-router/pull/11989))
- 稳定化导航的 `unstable_viewTransition` 选项及对应的 `unstable_useViewTransitionState` hook ([#11989](https://github.com/remix-run/react-router/pull/11989))
- 稳定化 `unstable_dataStrategy` ([#11974](https://github.com/remix-run/react-router/pull/11974))
- 稳定化 `unstable_patchRoutesOnNavigation` ([#11973](https://github.com/remix-run/react-router/pull/11973))
  - 新增 `PatchRoutesOnNavigationFunctionArgs` 类型以便使用 ([#11967](https://github.com/remix-run/react-router/pull/11967))

### 补丁变更

- 修复当 `?index` 参数已从先前提交存在时向当前上下文路由（具有索引子路由的父路由）提交的 bug ([#12003](https://github.com/remix-run/react-router/pull/12003))
- 修复 `useFormAction` bug - 移除 `?index` 参数时不会保留其他非 Remix 的 `index` 参数 ([#12003](https://github.com/remix-run/react-router/pull/12003))
- 修复并发 fetch 期间 fetcher 不通过重定向持久化 `preventScrollReset` 的 bug ([#11999](https://github.com/remix-run/react-router/pull/11999))
- 避免由于连续重新验证调用导致 fetcher 中止时的不必要 `console.error` ([#12050](https://github.com/remix-run/react-router/pull/12050))
- 修复带错误 hydrate 时 `partialHydration` 的 bug ([#12070](https://github.com/remix-run/react-router/pull/12070))
- 移除内部缓存以修复被中断的 `patchRoutesOnNavigation` 调用的问题 ([#12055](https://github.com/remix-run/react-router/pull/12055))
  - ⚠️ This may be a breaking change if you were relying on this behavior in the `unstable_` API
  - We used to cache in-progress calls to `patchRoutesOnNavigation` internally so that multiple navigations with the same start/end would only execute the function once and use the same promise
  - However, this approach was at odds with `patch` short circuiting if a navigation was interrupted (and the `request.signal` aborted) since the first invocation's `patch` would no-op
  - This cache also made some assumptions as to what a valid cache key might be - and is oblivious to any other application-state changes that may have occurred
  - So, the cache has been removed because in _most_ cases, repeated calls to something like `import()` for async routes will already be cached automatically - and if not it's easy enough for users to implement this cache in userland
- 从 `unstable_patchRoutesOnNavigation` 中移除内部 `discoveredRoutes` FIFO 队列 ([#11977](https://github.com/remix-run/react-router/pull/11977))
  - ⚠️ This may be a breaking change if you were relying on this behavior in the `unstable_` API
  - This was originally implemented as an optimization but it proved to be a bit too limiting
  - If you need this optimization you can implement your own cache inside `patchRoutesOnNavigation`
- 修复 `PatchRoutesOnNavigationFunction` 的 `patch` 方法中 `RouteObject` 的类型，使其不再期望传递给 `patch` 的无关路由对象 ([#11967](https://github.com/remix-run/react-router/pull/11967))
- 将从 `patchRoutesOnNavigation` 抛出的错误直接暴露给 `useRouteError`，而不是将其包装在 400 `ErrorResponse` 实例中 ([#12111](https://github.com/remix-run/react-router/pull/12111))

**完整更新日志**: [`v6.26.2...v6.27.0`](https://github.com/remix-run/react-router/compare/react-router@6.26.2...react-router@6.27.0)

## v6.26.2

日期：2024-09-09

### 补丁变更

- 更新 `unstable_dataStrategy` API 以允许更高级的实现 ([#11943](https://github.com/remix-run/react-router/pull/11943))
  - ⚠️ If you have already adopted `unstable_dataStrategy`, please review carefully as this includes breaking changes to this API
  - Rename `unstable_HandlerResult` to `unstable_DataStrategyResult`
  - Change the return signature of `unstable_dataStrategy` from a parallel array of `unstable_DataStrategyResult[]` (parallel to `matches`) to a key/value object of `routeId => unstable_DataStrategyResult`
    - This allows more advanced control over revalidation behavior because you can opt-into or out-of revalidating data that may not have been revalidated by default (via `match.shouldLoad`)
  - You should now return/throw a result from your `handlerOverride` instead of returning a `DataStrategyResult`
    - The return value (or thrown error) from your `handlerOverride` will be wrapped up into a `DataStrategyResult` and returned fromm `match.resolve`
    - Therefore, if you are aggregating the results of `match.resolve()` into a final results object you should not need to think about the `DataStrategyResult` type
    - If you are manually filling your results object from within your `handlerOverride`, then you will need to assign a `DataStrategyResult` as the value so React Router knows if it's a successful execution or an error (see examples in the documentation for details)
  - Added a new `fetcherKey` parameter to `unstable_dataStrategy` to allow differentiation from navigational and fetcher calls
- 通过重定向保留已选择的视图过渡 ([#11925](https://github.com/remix-run/react-router/pull/11925))
- 通过路由器重新验证调用保留挂起的视图过渡 ([#11917](https://github.com/remix-run/react-router/pull/11917))
- 修复快速/同步调用 `blocker.proceed` 时的 blocker 使用 ([#11930](https://github.com/remix-run/react-router/pull/11930))

**完整更新日志**: [`v6.26.1...v6.26.2`](https://github.com/remix-run/react-router/compare/react-router@6.26.1...react-router@6.26.2)

## v6.26.1

日期：2024-08-15

### 补丁变更

- 将 `unstable_patchRoutesOnMiss` 重命名为 `unstable_patchRoutesOnNavigation` 以匹配新行为 ([#11888](https://github.com/remix-run/react-router/pull/11888))
- 更新 `unstable_patchRoutesOnNavigation` 逻辑，当匹配到具有动态参数或 splat 段的路由时调用该方法，以防存在尚未发现的更高评分的静态路由 ([#11883](https://github.com/remix-run/react-router/pull/11883))
  - 我们现在还利用内部 FIFO 队列记录之前已调用 `unstable_patchRoutesOnNavigation` 的路径，以避免在后续导航到相同路径时重复调用

**完整更新日志**: [`v6.26.0...v6.26.1`](https://github.com/remix-run/react-router/compare/react-router@6.26.0...react-router@6.26.1)

## v6.26.0

日期：2024-08-01

### 次要变更

- Add a new `replace(url, init?)` alternative to `redirect(url, init?)` that performs a `history.replaceState` instead of a `history.pushState` on client-side navigation redirects ([#11811](https://github.com/remix-run/react-router/pull/11811))
- Add a new `unstable_data()` API for usage with Remix Single Fetch ([#11836](https://github.com/remix-run/react-router/pull/11836))
  - This API is not intended for direct usage in React Router SPA applications
  - It is primarily intended for usage with `createStaticHandler.query()` to allow loaders/actions to return arbitrary data along with custom `status`/`headers` without forcing the serialization of data into a `Response` instance
  - This allows for more advanced serialization tactics via `unstable_dataStrategy` such as serializing via `turbo-stream` in Remix Single Fetch
  - ⚠️ This removes the `status` field from `HandlerResult`
    - If you need to return a specific `status` from `unstable_dataStrategy` you should instead do so via `unstable_data()`

### 补丁变更

- Fix internal cleanup of interrupted fetchers to avoid invalid revalidations on navigations ([#11839](https://github.com/remix-run/react-router/pull/11839))
- Fix initial hydration behavior when using `future.v7_partialHydration` along with `unstable_patchRoutesOnMiss` ([#11838](https://github.com/remix-run/react-router/pull/11838))
  - During initial hydration, `router.state.matches` will now include any partial matches so that we can render ancestor `HydrateFallback` components

**完整更新日志**: [`v6.25.1...v6.26.0`](https://github.com/remix-run/react-router/compare/react-router@6.25.1...react-router@6.26.0)

## v6.25.1

日期：2024-07-17

### 补丁变更

- Memoize some `RouterProvider` internals to reduce unnecessary re-renders ([#11803](https://github.com/remix-run/react-router/pull/11803))

**完整更新日志**: [`v6.25.0...v6.25.1`](https://github.com/remix-run/react-router/compare/react-router@6.25.0...react-router@6.25.1)

## v6.25.0

日期：2024-07-16

### 重要变更

#### Stabilized `v7_skipActionErrorRevalidation`

This release stabilizes the `future.unstable_skipActionErrorRevalidation` flag into [`future.v7_skipActionErrorRevalidation`](https://reactrouter.com/v6/upgrading/future#v7_skipactionstatusrevalidation) in preparation for the upcoming React Router v7 release.

- When this flag is enabled, actions that return/throw a `4xx/5xx` `Response` will not trigger a revalidation by default
- This also stabilizes `shouldRevalidate`'s `unstable_actionStatus` parameter to `actionStatus`

### 次要变更

- Stabilize `future.unstable_skipActionErrorRevalidation` as `future.v7_skipActionErrorRevalidation` ([#11769](https://github.com/remix-run/react-router/pull/11769))

### 补丁变更

- Fix regression and properly decode paths inside `useMatch` so matches/params reflect decoded params ([#11789](https://github.com/remix-run/react-router/pull/11789))
- Fix bubbling of errors thrown from `unstable_patchRoutesOnMiss` ([#11786](https://github.com/remix-run/react-router/pull/11786))
- Fix hydration in SSR apps using `unstable_patchRoutesOnMiss` that matched a splat route on the server ([#11790](https://github.com/remix-run/react-router/pull/11790))

**完整更新日志**: [`v6.24.1...v6.25.0`](https://github.com/remix-run/react-router/compare/react-router@6.24.1...react-router@6.25.0)

## v6.24.1

日期：2024-07-03

### 补丁变更

- Remove `polyfill.io` reference from warning message because the domain was sold and has since been determined to serve malware ([#11741](https://github.com/remix-run/react-router/pull/11741))
  - See https://sansec.io/research/polyfill-supply-chain-attack
- Export `NavLinkRenderProps` type for easier typing of custom `NavLink` callback ([#11553](https://github.com/remix-run/react-router/pull/11553))
- When using `future.v7_relativeSplatPath`, properly resolve relative paths in splat routes that are children of pathless routes ([#11633](https://github.com/remix-run/react-router/pull/11633))
- Fog of War (unstable): Trigger a new `router.routes` identity/reflow during route patching ([#11740](https://github.com/remix-run/react-router/pull/11740))
- Fog of War (unstable): Fix initial matching when a splat route matches ([#11759](https://github.com/remix-run/react-router/pull/11759))

**完整更新日志**: [`v6.24.0...v6.24.1`](https://github.com/remix-run/react-router/compare/react-router@6.24.0...react-router@6.24.1)

## v6.24.0

日期：2024-06-24

### 重要变更

#### Lazy Route Discovery (a.k.a. "Fog of War")

We're really excited to release our new API for "Lazy Route Discovery" in `v6.24.0`! For some background information, please check out the original [RFC](https://github.com/remix-run/react-router/discussions/11113). The **tl;dr;** is that ever since we introduced the Data APIs in v6.4 via `<RouterProvider>`, we've been a little bummed that one of the tradeoffs was the lack of a compelling code-splitting story mirroring what we had in the `<BrowserRouter>`/`<Routes>` apps. We took a baby-step towards improving that story with `route.lazy` in `v6.9.0`, but with `v6.24.0` we've gone the rest of the way.

With "Fog of War", you can now load portions of the route tree lazily via the new `unstable_patchRoutesOnMiss` option passed to `createBrowserRouter` (and it's memory/hash counterparts). This gives you a way to hook into spots where React Router is unable to match a given path and patch new routes into the route tree during the navigation (or fetcher call).

Here's a very small example, but please refer to the [documentation](https://reactrouter.com/v6/routers/create-browser-router#optsunstable_patchroutesonmiss) for more information and use cases:

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

- 新增懒加载路由发现（又名 "迷雾"）支持 ([#11626](https://github.com/remix-run/react-router/pull/11626))

### 补丁变更

- 修复 `fetcher.submit` 类型 - 移除不正确的 `navigate`/`fetcherKey`/`unstable_viewTransition` 选项，因为它们仅与 `useSubmit` 相关 ([#11631](https://github.com/remix-run/react-router/pull/11631))
- 允许传递给 `<StaticRouter>` 的假值 `location.state` ([#11495](https://github.com/remix-run/react-router/pull/11495))

**完整更新日志**: [`v6.23.1...v6.24.0`](https://github.com/remix-run/react-router/compare/react-router@6.23.1...react-router@6.24.0)

## v6.23.1

日期：2024-05-10

### 补丁变更

- 允许 `<Await>` 解析 `undefined` ([#11513](https://github.com/remix-run/react-router/pull/11513))
- 检查 `document.startViewTransition` 可用性时新增防御性 `document` 检查 ([#11544](https://github.com/remix-run/react-router/pull/11544))
- 将 `react-router-dom/server` 导入改回 `react-router-dom` 而非 `index.ts` ([#11514](https://github.com/remix-run/react-router/pull/11514))
- `@remix-run/router` - 在 `staticHandler.queryRoute` 上支持 `unstable_dataStrategy` ([#11515](https://github.com/remix-run/react-router/pull/11515))

**完整更新日志**: [`v6.23.0...v6.23.1`](https://github.com/remix-run/react-router/compare/react-router@6.23.0...react-router@6.23.1)

## v6.23.0

日期：2024-04-23

### 重要变更

#### 数据策略（不稳定）

新的 `unstable_dataStrategy` API 是一个低级 API，专为需要控制 `loader`/`action` 函数数据策略的高级用例设计。默认实现是当前的行为，即并行获取所有 loader，但此选项允许用户实现更高级的数据流，包括 Remix ["Single Fetch"](https://remix.run/docs/guides/single-fetch)、用户空间中间件/context API、自动 loader 缓存等。更多信息请参阅[文档](https://reactrouter.com/v6/routers/create-browser-router#unstable_datastrategy)。

**注意：** 这是一个面向高级用例的低级 API。它覆盖了 React Router 对 `loader`/`action` 执行的内部处理，如果使用不当会破坏你的应用代码。请谨慎使用并进行适当的测试。

#### 跳过 Action 错误重新验证（不稳定）

目前，所有活跃的 `loader` 在任何 `action` 提交后都会重新验证，无论 `action` 的结果如何。然而，在大多数情况下，来自 `action` 的 `4xx`/`5xx` 响应意味着没有实际数据被更改，重新验证是不必要的。我们引入了一个新的 `future.unstable_skipActionErrorRevalidation` 标志来改变此行为，并计划在 React Router 的未来版本中将其设为默认值。

启用此标志后，返回/抛出 `4xx`/`5xx` 响应状态的 `action` 将不再自动重新验证。如果你需要在启用此标志的情况下在 `4xx`/`5xx` 结果后重新验证，仍然可以通过从 `shouldRevalidate` 返回 `true` 来实现 - 它现在还接收一个新的 `unstable_actionStatus` 参数以及 `actionResult`，以便你可以根据 `action` 响应的状态做出决定，而无需将其编码到 action 数据中。

### 次要变更

- 新增 `unstable_dataStrategy` 配置选项 ([#11098](https://github.com/remix-run/react-router/pull/11098), [#11377](https://github.com/remix-run/react-router/pull/11377))
- `@remix-run/router` - 新增 `future.unstable_skipActionRevalidation` future 标志 ([#11098](https://github.com/remix-run/react-router/pull/11098))
- `@remix-run/router` - SSR：为 `staticHandler.query` 方法新增 `skipLoaderErrorBubbling` 选项，用于在 Remix 的 Single Fetch 实现中禁用静态处理器的错误冒泡 ([#11098](https://github.com/remix-run/react-router/pull/11098), ([#11377](https://github.com/remix-run/react-router/pull/11377)))

**完整更新日志**: [`v6.22.3...v6.23.0`](https://github.com/remix-run/react-router/compare/react-router@6.22.3...react-router@6.23.0)

## v6.22.3

日期：2024-03-07

### 补丁变更

- 修复 `future.v7_partialHydration` 的 bug，当 SSR loader 错误冒泡到父边界时会在 hydration 时重新运行边界以下的 loader ([#11324](https://github.com/remix-run/react-router/pull/11324))
- 修复 `future.v7_partialHydration` 的 bug，如果路由没有 loader 则会认为路由器未初始化 ([#11325](https://github.com/remix-run/react-router/pull/11325))

**完整更新日志**: [`v6.22.2...v6.22.3`](https://github.com/remix-run/react-router/compare/react-router@6.22.2...react-router@6.22.3)

## v6.22.2

日期：2024-02-28

### 补丁变更

- 在部分 hydration 运行期间保留已 hydrate 的错误 ([#11305](https://github.com/remix-run/react-router/pull/11305))

**完整更新日志**: [`v6.22.1...v6.22.2`](https://github.com/remix-run/react-router/compare/react-router@6.22.1...react-router@6.22.2)

## v6.22.1

日期：2024-02-16

### 补丁变更

- 修复预编码动态参数值的编码/解码问题 ([#11199](https://github.com/remix-run/react-router/pull/11199))

**完整更新日志**: [`v6.22.0...v6.22.1`](https://github.com/remix-run/react-router/compare/react-router@6.22.0...react-router@6.22.1)

## v6.22.0

日期：2024-02-01

### 重要变更

#### Core Web Vitals 技术报告标志

In 2021, the HTTP Archive launched the [Core Web Vitals Technology Report dashboard](https://discuss.httparchive.org/t/new-dashboard-the-core-web-vitals-technology-report/2178):

> By combining the powers of real-user experiences in the Chrome UX Report 26 (CrUX) dataset with web technology detections in HTTP Archive 30, we can get a glimpse into how architectural decisions like choices of CMS platform or JavaScript framework play a role in sites’ CWV performance.

They use a tool called [`wappalyzer`](https://github.com/HTTPArchive/wappalyzer) to identify what technologies a given website is using by looking for certain scripts, global JS variables, or other identifying characteristics. For example, for Remix applications, they [look for the global `__remixContext`](https://github.com/HTTPArchive/wappalyzer/blob/c2a24ee7c2d07bf9c521f02584ae2dcf603ac0b7/src/technologies/r.json#L1328) variable to identify that a website is using Remix.

It was brought to our attention that React Router was unable to be reliably identified because there are no identifying global aspects. They are currently [looking for external scripts with `react-router`](https://github.com/HTTPArchive/wappalyzer/blob/c2a24ee7c2d07bf9c521f02584ae2dcf603ac0b7/src/technologies/r.json#L637) in the name. This will identify sites using React Router from a CDN such as `unpkg` - but it will miss the **vast** majority of sites that are installing React Router from the npm registry and bundling it into their JS files. This results in [drastically under-reporting](https://lookerstudio.google.com/s/pixHkNmGbN4) the usage of React Router on the web.

Starting with version `6.22.0`, sites using `react-router-dom` will begin adding a `window.__reactRouterVersion` variable that will be set to a string value of the SemVer major version number (i.e., `window.__reactRouterVersion = "6";`) so that they can be properly identified.

### 次要变更

- 包含 `window.__reactRouterVersion` 用于 CWV 报告检测 ([#11222](https://github.com/remix-run/react-router/pull/11222))
- 新增 `createStaticHandler` 的 `future.v7_throwAbortReason` 标志，在请求被中止时抛出 `request.signal.reason`（默认为 `DOMException`）而非 `Error` 如 `new Error("query() call aborted: GET /path")` ([#11104](https://github.com/remix-run/react-router/pull/11104))
  - 请注意 `DOMException` 是在 Node v17 中添加的，因此在 Node 16 及以下版本中不会得到 `DOMException`。

### 补丁变更

- 如果传递给 `getStaticContextFormError` 则尊重 `ErrorResponse` 状态码 ([#11213](https://github.com/remix-run/react-router/pull/11213))

**完整更新日志**: [`v6.21.3...v6.22.0`](https://github.com/remix-run/react-router/compare/react-router@6.21.3...react-router@6.22.0)

## v6.21.3

日期：2024-01-18

### 补丁变更

- 修复使用 `basename` 时 `NavLink` 的 `isPending` ([#11195](https://github.com/remix-run/react-router/pull/11195))
- 从 `Blocker`/`BlockerFunction` 类型中移除遗留的 `unstable_` 前缀 ([#11187](https://github.com/remix-run/react-router/pull/11187))

**完整更新日志**: [`v6.21.2...v6.21.3`](https://github.com/remix-run/react-router/compare/react-router@6.21.2...react-router@6.21.3)

## v6.21.2

日期：2024-01-11

### 补丁变更

- 在可用时利用 `useId` 作为内部 fetcher 键 ([#11166](https://github.com/remix-run/react-router/pull/11166))
- 修复动态参数名中连字符未被识别的 bug ([#11160](https://github.com/remix-run/react-router/pull/11160))
- 不要尝试反序列化空 JSON 响应 ([#11164](https://github.com/remix-run/react-router/pull/11164))

**完整更新日志**: [`v6.21.1...v6.21.2`](https://github.com/remix-run/react-router/compare/react-router@6.21.1...react-router@6.21.2)

## v6.21.1

日期：2023-12-21

### 补丁变更

- 修复指定 `v7_partialHydration` 时 `route.lazy` 在初始 SPA 加载时不能正确工作的 bug ([#11121](https://github.com/remix-run/react-router/pull/11121))
- 修复在 `submitting` 阶段卸载的持久化 fetcher 无法进行重新验证的 bug ([#11102](https://github.com/remix-run/react-router/pull/11102))
- 在 `resolveTo` 中去重相对路径逻辑 ([#11097](https://github.com/remix-run/react-router/pull/11097))

**完整更新日志**: [`v6.21.0...v6.21.1`](https://github.com/remix-run/react-router/compare/react-router@6.21.0...react-router@6.21.1)

## v6.21.0

日期：2023-12-13

### 重要变更

#### `future.v7_relativeSplatPath`

We fixed a splat route path-resolution bug in `6.19.0`, but later determined a large number of applications were relying on the buggy behavior, so we reverted the fix in `6.20.1` (see [#10983](https://github.com/remix-run/react-router/issues/10983), [#11052](https://github.com/remix-run/react-router/issues/11052), [#11078](https://github.com/remix-run/react-router/issues/11078)).

The buggy behavior is that the default behavior when resolving relative paths inside a splat route would _ignore_ any splat (`*`) portion of the current route path. When the future flag is enabled, splat portions are included in relative path logic within splat routes.

For more information, please refer to the [`useResolvedPath` docs](https://reactrouter.com/v6/hooks/use-resolved-path#splat-paths) and/or the [detailed changelog entry](https://github.com/remix-run/react-router/blob/main/packages/react-router-dom/CHANGELOG.md#6210).

#### Partial Hydration

We added a new `future.v7_partialHydration` future flag for the `@remix-run/router` that enables partial hydration of a data router when Server-Side Rendering. This allows you to provide `hydrationData.loaderData` that has values for _some_ initially matched route loaders, but not all. When this flag is enabled, the router will call `loader` functions for routes that do not have hydration loader data during `router.initialize()`, and it will render down to the deepest provided `HydrateFallback` (up to the first route without hydration data) while it executes the unhydrated routes. ([#11033](https://github.com/remix-run/react-router/pull/11033))

### 次要变更

- 新增 `future.v7_relativeSplatPath` 标志以实现 splat 路由内相对路由的破坏性 bug 修复。 ([#11087](https://github.com/remix-run/react-router/pull/11087))
- 新增 `future.v7_partialHydration` future 标志，在服务器端渲染时启用数据路由器的部分 hydration ([#11033](https://github.com/remix-run/react-router/pull/11033))

### 补丁变更

- 在 `ErrorBoundary` 中正确处理假值错误 ([#11071](https://github.com/remix-run/react-router/pull/11071))
- 捕获并冒泡尝试解包 `loader`/`action` 函数响应时抛出的错误 ([#11061](https://github.com/remix-run/react-router/pull/11061))
- 修复在匹配路由外渲染 `Link`/`NavLink` 时 `relative="path"` 的问题 ([#11062](https://github.com/remix-run/react-router/pull/11062))

**完整更新日志**: [`v6.20.1...v6.21.0`](https://github.com/remix-run/react-router/compare/react-router@6.20.1...react-router@6.21.0)

## v6.20.1

日期：2023-12-01

### 补丁变更

- 由于大量应用依赖于有 bug 的行为，恢复了 splat 路由的 `useResolvedPath` 修复（参见 [#11052](https://github.com/remix-run/react-router/issues/11052#issuecomment-1836589329)） ([#11078](https://github.com/remix-run/react-router/pull/11078))
  - 我们计划在下一个次要版本中通过 future 标志重新引入此修复（参见[此评论](https://github.com/remix-run/react-router/issues/11052#issuecomment-1836589329)）
  - 此修复包含在 `6.19.0` 和 `6.20.0` 版本中。如果你是从 `6.18.0` 或更早版本升级，你不会受到此修复的影响。

**完整更新日志**: [`v6.20.0...v6.20.1`](https://github.com/remix-run/react-router/compare/react-router@6.20.0...react-router@6.20.1)

## v6.20.0

日期：2023-11-22

> [!WARNING]
> Please use version `6.20.1` or later instead of `6.20.0`. We discovered that a large number of apps were relying on buggy behavior that was fixed in this release ([#11045](https://github.com/remix-run/react-router/pull/11045)). We reverted the fix in `6.20.1` and will be re-introducing it behind a future flag in a subsequent release. See [#11052](https://github.com/remix-run/react-router/issues/11052#issuecomment-1836589329) for more details.

### 次要变更

- 从公共 API 导出 `PathParam` 类型 ([#10719](https://github.com/remix-run/react-router/pull/10719))

### 补丁变更

- 启用 `v7_fetcherPersist` 时不要重新验证已卸载的 fetcher ([#11044](https://github.com/remix-run/react-router/pull/11044))
- 修复 splat 路由中 `resolveTo` 路径解析的 bug ([#11045](https://github.com/remix-run/react-router/pull/11045))
  - 这是 [#10983](https://github.com/remix-run/react-router/pull/10983) 的后续处理，用于处理使用 `getPathContributingMatches` 的其他几个代码路径
  - 这从 `@remix-run/router` 中移除了 `UNSAFE_getPathContributingMatches` 导出，因为我们不再在 `react-router`/`react-router-dom` 层中需要它

**完整更新日志**: [`v6.19.0...v6.20.0`](https://github.com/remix-run/react-router/compare/react-router@6.19.0...react-router@6.20.0)

## v6.19.0

日期：2023-11-16

> [!WARNING]
> Please use version `6.20.1` or later instead of `6.19.0`. We discovered that a large number of apps were relying on buggy behavior that was fixed in this release ([#10983](https://github.com/remix-run/react-router/pull/10983)). We reverted the fix in `6.20.1` and will be re-introducing it behind a future flag in a subsequent release. See [#11052](https://github.com/remix-run/react-router/issues/11052#issuecomment-1836589329) for more details.

### 重要变更

#### `unstable_flushSync` API

本次发布为命令式 API（`useSubmit`、`useNavigate`、`fetcher.submit`、`fetcher.load`）带来了新的 `unstable_flushSync` 选项，让用户可以为挂起/乐观 UI 选择同步 DOM 更新。

```js
function handleClick() {
  submit(data, { flushSync: true });
  // 所有内容都已刷新到 DOM，你可以聚焦/滚动到你的挂起/乐观 UI
  setFocusAndOrScrollToNewlyAddedThing();
}
```

### 次要变更

- 为 `useNavigate`/`useSubmit`/`fetcher.load`/`fetcher.submit` 新增 `unstable_flushSync` 选项以退出 `React.startTransition` 并使用 `ReactDOM.flushSync` 进行状态更新 ([#11005](https://github.com/remix-run/react-router/pull/11005))
- 从 [`useBlocker`](https://reactrouter.com/v6/hooks/use-blocker) hook 中移除 `unstable_` 前缀，因为它已使用了足够长的时间，我们对该 API 有信心 ([#10991](https://github.com/remix-run/react-router/pull/10991))
  - 由于浏览器处理 `window.confirm` 的方式不同，React Router 无法保证一致/正确的行为，我们不计划从 `unstable_usePrompt` 中移除前缀

### 补丁变更

- 修复 `useActionData` 使其返回正确的上下文 action 数据，而非树中的_任何_ action 数据 ([#11023](https://github.com/remix-run/react-router/pull/11023))
- 修复 `useResolvedPath` 中的 bug，该 bug 导致 splat 路由中的 `useResolvedPath(".")` 丢失 URL 路径的 splat 部分。 ([#10983](https://github.com/remix-run/react-router/pull/10983))
  - ⚠️ This fixes a quite long-standing bug specifically for `"."` paths inside a splat route which incorrectly dropped the splat portion of the URL. If you are relative routing via `"."` inside a splat route in your application you should double check that your logic is not relying on this buggy behavior and update accordingly.
- 修复保持挂载的 `useFetcher` 中更改 fetcher `key` 不被捕获的问题 ([#11009](https://github.com/remix-run/react-router/pull/11009))
- 修复 `useFormAction` 错误地从子路由 `action` 提交继承 `?index` 查询参数的问题 ([#11025](https://github.com/remix-run/react-router/pull/11025))
- 修复 `to` 位置有尾斜杠时 `NavLink` 的 `active` 逻辑 ([#10734](https://github.com/remix-run/react-router/pull/10734))
- 修复类型使 `unstable_usePrompt` 可以接受 `BlockerFunction` 和 `boolean` ([#10991](https://github.com/remix-run/react-router/pull/10991))
- 修复 `relative="path"` bug，相对路径计算从完整的 location 路径名开始，而不是从当前上下文路由路径名开始。 ([#11006](https://github.com/remix-run/react-router/pull/11006))

  ```jsx
  <Route path="/a">
    <Route path="/b" element={<Component />}>
      <Route path="/c" />
    </Route>
  </Route>;

  function Component() {
    return (
      <>
        {/* 现在正确地相对于 /a/b，而非 /a/b/c */}
        <Link to=".." relative="path" />
        <Outlet />
      </>
    );
  }
  ```

**完整更新日志**: [`6.18.0...6.19.0`](https://github.com/remix-run/react-router/compare/react-router@6.18.0...react-router@6.19.0)

## v6.18.0

日期：2023-10-31

### 重要变更

#### 新的 Fetcher API

Per this [RFC](https://github.com/remix-run/remix/discussions/7698), we've introduced some new APIs that give you more granular control over your fetcher behaviors.

- You may now specify your own fetcher identifier via `useFetcher({ key: string })`, which allows you to access the same fetcher instance from different components in your application without prop-drilling
- Fetcher keys are now exposed on the fetchers returned from `useFetchers` so that they can be looked up by `key`
- `Form` and `useSubmit` now support optional `navigate`/`fetcherKey` props/params to allow kicking off a fetcher submission under the hood with an optionally user-specified `key`
  - `<Form method="post" navigate={false} fetcherKey="my-key">`
  - `submit(data, { method: "post", navigate: false, fetcherKey: "my-key" })`
  - Invoking a fetcher in this way is ephemeral and stateless
  - If you need to access the state of one of these fetchers, you will need to leverage `useFetchers()` or `useFetcher({ key })` to look it up elsewhere

#### 持久化 Future 标志（`future.v7_fetcherPersist`）

Per the same [RFC](https://github.com/remix-run/remix/discussions/7698) as above, we've introduced a new `future.v7_fetcherPersist` flag that allows you to opt-into the new fetcher persistence/cleanup behavior. Instead of being immediately cleaned up on unmount, fetchers will persist until they return to an `idle` state. This makes pending/optimistic UI _much_ easier in scenarios where the originating fetcher needs to unmount.

- This is sort of a long-standing bug fix as the `useFetchers()` API was always supposed to only reflect **in-flight** fetcher information for pending/optimistic UI -- it was not intended to reflect fetcher data or hang onto fetchers after they returned to an `idle` state
- Keep an eye out for the following specific behavioral changes when opting into this flag and check your app for compatibility:
  - Fetchers that complete _while still mounted_ will no longer appear in `useFetchers()` after completion - they served no purpose in there since you can access the data via `useFetcher().data`
  - Fetchers that previously unmounted _while in-flight_ will not be immediately aborted and will instead be cleaned up once they return to an `idle` state
    - They will remain exposed via `useFetchers` while in-flight so you can still access pending/optimistic data after unmount
    - If a fetcher is no longer mounted when it completes, then it's result will not be post processed - e.g., redirects will not be followed and errors will not bubble up in the UI
    - However, if a fetcher was re-mounted elsewhere in the tree using the same `key`, then it's result will be processed, even if the originating fetcher was unmounted

### 次要变更

- Add fetcher `key` APIs and `navigate=false` options ([#10960](https://github.com/remix-run/react-router/pull/10960))
- Add `future.v7_fetcherPersist` flag ([#10962](https://github.com/remix-run/react-router/pull/10962))
- Add support for optional path segments in `matchPath` ([#10768](https://github.com/remix-run/react-router/pull/10768))

### 补丁变更

- Fix the `future` prop on `BrowserRouter`, `HashRouter` and `MemoryRouter` so that it accepts a `Partial<FutureConfig>` instead of requiring all flags to be included ([#10962](https://github.com/remix-run/react-router/pull/10962))
- Fix `router.getFetcher`/`router.deleteFetcher` type definitions which incorrectly specified `key` as an optional parameter ([#10960](https://github.com/remix-run/react-router/pull/10960))

**完整更新日志**: [`6.17.0...6.18.0`](https://github.com/remix-run/react-router/compare/react-router@6.17.0...react-router@6.18.0)

## v6.17.0

日期：2023-10-16

### 重要变更

#### View Transitions 🚀

We're excited to release experimental support for the [View Transitions API](https://developer.mozilla.org/en-US/docs/Web/API/ViewTransition) in React Router! You can now trigger navigational DOM updates to be wrapped in `document.startViewTransition` to enable CSS animated transitions on SPA navigations in your application.

The simplest approach to enabling a View Transition in your React Router app is via the new [`<Link unstable_viewTransition>`](https://reactrouter.com/v6/components/link#unstable_viewtransition) prop. This will cause the navigation DOM update to be wrapped in `document.startViewTransition` which will enable transitions for the DOM update. Without any additional CSS styles, you'll get a basic cross-fade animation for your page.

If you need to apply more fine-grained styles for your animations, you can leverage the [`unstable_useViewTransitionState`](https://reactrouter.com/v6/hooks/use-view-transition-state) hook which will tell you when a transition is in progress and you can use that to apply classes or styles:

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

You can also use the [`<NavLink unstable_viewTransition>`](https://reactrouter.com/v6/components/nav-link#unstable_viewtransition) shorthand which will manage the hook usage for you and automatically add a `transitioning` class to the `<a>` during the transition:

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

For an example usage of View Transitions, check out [our fork](https://github.com/brophdawg11/react-router-records) of the awesome [Astro Records](https://github.com/Charca/astro-records) demo.

For more information on using the View Transitions API, please refer to the [Smooth and simple transitions with the View Transitions API](https://developer.chrome.com/docs/web-platform/view-transitions/) guide from the Google Chrome team.

### 次要变更

- Add support for view transitions ([#10916](https://github.com/remix-run/react-router/pull/10916))

### 补丁变更

- Log a warning and fail gracefully in `ScrollRestoration` when `sessionStorage` is unavailable ([#10848](https://github.com/remix-run/react-router/pull/10848))
- Fix `RouterProvider` `future` prop type to be a `Partial<FutureConfig>` so that not all flags must be specified ([#10900](https://github.com/remix-run/react-router/pull/10900))
- Allow 404 detection to leverage root route error boundary if path contains a URL segment ([#10852](https://github.com/remix-run/react-router/pull/10852))
- Fix `ErrorResponse` type to avoid leaking internal field ([#10876](https://github.com/remix-run/react-router/pull/10876))

**完整更新日志**: [`6.16.0...6.17.0`](https://github.com/remix-run/react-router/compare/react-router@6.16.0...react-router@6.17.0)

## v6.16.0

日期：2023-09-13

### 次要变更

- In order to move towards stricter TypeScript support in the future, we're aiming to replace current usages of `any` with `unknown` on exposed typings for user-provided data. To do this in Remix v2 without introducing breaking changes in React Router v6, we have added generics to a number of shared types. These continue to default to `any` in React Router and are overridden with `unknown` in Remix. In React Router v7 we plan to move these to `unknown` as a breaking change. ([#10843](https://github.com/remix-run/react-router/pull/10843))
  - `Location` now accepts a generic for the `location.state` value
  - `ActionFunctionArgs`/`ActionFunction`/`LoaderFunctionArgs`/`LoaderFunction` now accept a generic for the `context` parameter (only used in SSR usages via `createStaticHandler`)
  - The return type of `useMatches` (now exported as `UIMatch`) accepts generics for `match.data` and `match.handle` - both of which were already set to `unknown`
- Move the `@private` class export `ErrorResponse` to an `UNSAFE_ErrorResponseImpl` export since it is an implementation detail and there should be no construction of `ErrorResponse` instances in userland. This frees us up to export a `type ErrorResponse` which correlates to an instance of the class via `InstanceType`. Userland code should only ever be using `ErrorResponse` as a type and should be type-narrowing via `isRouteErrorResponse`. ([#10811](https://github.com/remix-run/react-router/pull/10811))
- Export `ShouldRevalidateFunctionArgs` interface ([#10797](https://github.com/remix-run/react-router/pull/10797))
- Removed private/internal APIs only required for the Remix v1 backwards compatibility layer and no longer needed in Remix v2 (`_isFetchActionRedirect`, `_hasFetcherDoneAnything`) ([#10715](https://github.com/remix-run/react-router/pull/10715))

### 补丁变更

- Properly encode rendered URIs in server rendering to avoid hydration errors ([#10769](https://github.com/remix-run/react-router/pull/10769))
- Add method/url to error message on aborted `query`/`queryRoute` calls ([#10793](https://github.com/remix-run/react-router/pull/10793))
- Fix a race-condition with loader/action-thrown errors on `route.lazy` routes ([#10778](https://github.com/remix-run/react-router/pull/10778))
- Fix type for `actionResult` on the arguments object passed to `shouldRevalidate` ([#10779](https://github.com/remix-run/react-router/pull/10779))

**完整更新日志**: [`v6.15.0...v6.16.0`](https://github.com/remix-run/react-router/compare/react-router@6.15.0...react-router@6.16.0)

## v6.15.0

日期：2023-08-10

### 次要变更

- Add's a new `redirectDocument()` function which allows users to specify that a redirect from a `loader`/`action` should trigger a document reload (via `window.location`) instead of attempting to navigate to the redirected location via React Router ([#10705](https://github.com/remix-run/react-router/pull/10705))

### 补丁变更

- Ensure `useRevalidator` is referentially stable across re-renders if revalidations are not actively occurring ([#10707](https://github.com/remix-run/react-router/pull/10707))
- Ensure hash history always includes a leading slash on hash pathnames ([#10753](https://github.com/remix-run/react-router/pull/10753))
- Fixes an edge-case affecting web extensions in Firefox that use `URLSearchParams` and the `useSearchParams` hook ([#10620](https://github.com/remix-run/react-router/pull/10620))
- Reorder effects in `unstable_usePrompt` to avoid throwing an exception if the prompt is unblocked and a navigation is performed synchronously ([#10687](https://github.com/remix-run/react-router/pull/10687), [#10718](https://github.com/remix-run/react-router/pull/10718))
- SSR: Do not include hash in `useFormAction()` for unspecified actions since it cannot be determined on the server and causes hydration issues ([#10758](https://github.com/remix-run/react-router/pull/10758))
- SSR: Fix an issue in `queryRoute` that was not always identifying thrown `Response` instances ([#10717](https://github.com/remix-run/react-router/pull/10717))
- `react-router-native`: Update `@ungap/url-search-params` dependency from `^0.1.4` to `^0.2.2` ([#10590](https://github.com/remix-run/react-router/pull/10590))

**完整更新日志**: [`v6.14.2...v6.15.0`](https://github.com/remix-run/react-router/compare/react-router@6.14.2...react-router@6.15.0)

## v6.14.2

日期：2023-07-17

### 补丁变更

- Add missing `<Form state>` prop to populate `history.state` on submission navigations ([#10630](https://github.com/remix-run/react-router/pull/10630))
- Trigger an error if a `defer` promise resolves/rejects with `undefined` in order to match the behavior of loaders and actions which must return a value or `null` ([#10690](https://github.com/remix-run/react-router/pull/10690))
- Properly handle fetcher redirects interrupted by normal navigations ([#10674](https://github.com/remix-run/react-router/pull/10674))
- Initial-load fetchers should not automatically revalidate on GET navigations ([#10688](https://github.com/remix-run/react-router/pull/10688))
- Properly decode element id when emulating hash scrolling via `<ScrollRestoration>` ([#10682](https://github.com/remix-run/react-router/pull/10682))
- Typescript: Enhance the return type of `Route.lazy` to prohibit returning an empty object ([#10634](https://github.com/remix-run/react-router/pull/10634))
- SSR: Support proper hydration of `Error` subclasses such as `ReferenceError`/`TypeError` ([#10633](https://github.com/remix-run/react-router/pull/10633))

**完整更新日志**: [`v6.14.1...v6.14.2`](https://github.com/remix-run/react-router/compare/react-router@6.14.1...react-router@6.14.2)

## v6.14.1

日期：2023-06-30

### 补丁变更

- Fix loop in `unstable_useBlocker` when used with an unstable blocker function ([#10652](https://github.com/remix-run/react-router/pull/10652))
- Fix issues with reused blockers on subsequent navigations ([#10656](https://github.com/remix-run/react-router/pull/10656))
- Updated dependencies:
  - `@remix-run/router@1.7.1`

**完整更新日志**: [`v6.14.0...v6.14.1`](https://github.com/remix-run/react-router/compare/react-router@6.14.0...react-router@6.14.1)

## v6.14.0

日期：2023-06-23

### 重要变更

#### JSON/Text Submissions

`6.14.0` adds support for JSON and Text submissions via `useSubmit`/`fetcher.submit` since it's not always convenient to have to serialize into `FormData` if you're working in a client-side SPA. To opt-into these encodings you just need to specify the proper `formEncType`:

**Opt-into `application/json` encoding:**

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

**Opt-into `text/plain` encoding:**

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

**⚠️ Default Behavior Will Change in v7**

Please note that to avoid a breaking change, the default behavior will still encode a simple key/value JSON object into a `FormData` instance:

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

This behavior will likely change in v7 so it's best to make any JSON object submissions explicit with `formEncType: "application/x-www-form-urlencoded"` or `formEncType: "application/json"` to ease your eventual v7 migration path.

### 次要变更

- Add support for `application/json` and `text/plain` encodings for `useSubmit`/`fetcher.submit`. To reflect these additional types, `useNavigation`/`useFetcher` now also contain `navigation.json`/`navigation.text` and `fetcher.json`/`fetcher.text` which include the json/text submission if applicable. ([#10413](https://github.com/remix-run/react-router/pull/10413))

### 补丁变更

- When submitting a form from a `submitter` element, prefer the built-in `new FormData(form, submitter)` instead of the previous manual approach in modern browsers (those that support the new `submitter` parameter) ([#9865](https://github.com/remix-run/react-router/pull/9865))
  - For browsers that don't support it, we continue to just append the submit button's entry to the end, and we also add rudimentary support for `type="image"` buttons
  - If developers want full spec-compliant support for legacy browsers, they can use the `formdata-submitter-polyfill`
- Call `window.history.pushState/replaceState` _before_ updating React Router state (instead of after) so that `window.location` matches `useLocation` during synchronous React 17 rendering ([#10448](https://github.com/remix-run/react-router/pull/10448))
  - ⚠️ Note: generally apps should not be relying on `window.location` and should always reference `useLocation` when possible, as `window.location` will not be in sync 100% of the time (due to `popstate` events, concurrent mode, etc.)
- Avoid calling `shouldRevalidate` for fetchers that have not yet completed a data load ([#10623](https://github.com/remix-run/react-router/pull/10623))
- Strip `basename` from the `location` provided to `<ScrollRestoration getKey>` to match the `useLocation` behavior ([#10550](https://github.com/remix-run/react-router/pull/10550))
- Strip `basename` from locations provided to `unstable_useBlocker` functions to match the `useLocation` behavior ([#10573](https://github.com/remix-run/react-router/pull/10573))
- Fix `unstable_useBlocker` key issues in `StrictMode` ([#10573](https://github.com/remix-run/react-router/pull/10573))
- Fix `generatePath` when passed a numeric `0` value parameter ([#10612](https://github.com/remix-run/react-router/pull/10612))
- Fix `tsc --skipLibCheck:false` issues on React 17 ([#10622](https://github.com/remix-run/react-router/pull/10622))
- Upgrade `typescript` to 5.1 ([#10581](https://github.com/remix-run/react-router/pull/10581))

**完整更新日志**: [`v6.13.0...v6.14.0`](https://github.com/remix-run/react-router/compare/react-router@6.13.0...react-router@6.14.0)

## v6.13.0

日期：2023-06-14

### 重要变更

`6.13.0` is really a patch release in spirit but comes with a SemVer minor bump since we added a new future flag.

#### `future.v7_startTransition`

The **tl;dr;** is that `6.13.0` is the same as [`6.12.0`](https://github.com/remix-run/react-router/releases/tag/react-router%406.12.0) bue we've moved the usage of `React.startTransition` behind an opt-in `future.v7_startTransition` [future flag](https://reactrouter.com/v6/guides/api-development-strategy) because we found that there are applications in the wild that are currently using `Suspense` in ways that are incompatible with `React.startTransition`.

Therefore, in `6.13.0` the default behavior will no longer leverage `React.startTransition`:

```jsx
<BrowserRouter>
  <Routes>{/*...*/}</Routes>
</BrowserRouter>

<RouterProvider router={router} />
```

If you wish to enable `React.startTransition`, pass the future flag to your router component:

```jsx
<BrowserRouter future={{ v7_startTransition: true }}>
  <Routes>{/*...*/}</Routes>
</BrowserRouter>

<RouterProvider router={router} future={{ v7_startTransition: true }}/>
```

We recommend folks adopt this flag sooner rather than later to be better compatible with React concurrent mode, but if you run into issues you can continue without the use of `React.startTransition` until v7. Issues usually boil down to creating net-new promises during the render cycle, so if you run into issues when opting into `React.startTransition`, you should either lift your promise creation out of the render cycle or put it behind a `useMemo`.

### 次要变更

- Move `React.startTransition` usage behinds a future flag ([#10596](https://github.com/remix-run/react-router/pull/10596))

### 补丁变更

- Work around webpack/terser `React.startTransition` minification bug in production mode ([#10588](https://github.com/remix-run/react-router/pull/10588))

**完整更新日志**: [`v6.12.1...v6.13.0`](https://github.com/remix-run/react-router/compare/react-router@6.12.1...react-router@6.13.0)

## v6.12.1

日期：2023-06-08

> [!WARNING]
> Please use version `6.13.0` or later instead of `6.12.0`/`6.12.1`. These versions suffered from some Webpack build/minification issues resulting failed builds or invalid minified code in your production bundles. See [#10569](https://github.com/remix-run/react-router/pull/10569) and [#10579](https://github.com/remix-run/react-router/issues/10579) for more details.

### 补丁变更

- Adjust feature detection of `React.startTransition` to fix webpack + react 17 compilation error ([#10569](https://github.com/remix-run/react-router/pull/10569))

**完整更新日志**: [`v6.12.0...v6.12.1`](https://github.com/remix-run/react-router/compare/react-router@6.12.0...react-router@6.12.1)

## v6.12.0

日期：2023-06-06

> [!WARNING]
> Please use version `6.13.0` or later instead of `6.12.0`/`6.12.1`. These versions suffered from some Webpack build/minification issues resulting failed builds or invalid minified code in your production bundles. See [#10569](https://github.com/remix-run/react-router/pull/10569) and [#10579](https://github.com/remix-run/react-router/issues/10579) for more details.

### 重要变更

#### `React.startTransition` support

With `6.12.0` we've added better support for suspending components by wrapping the internal router state updates in [`React.startTransition`](https://react.dev/reference/react/startTransition). This means that, for example, if one of your components in a destination route suspends and you have not provided a [`Suspense`](https://react.dev/reference/react/Suspense) boundary to show a fallback, React will delay the rendering of the new UI and show the old UI until that asynchronous operation resolves. This could be useful for waiting for things such as waiting for images or CSS files to load (and technically, yes, you could use it for data loading but we'd still recommend using loaders for that 😀). For a quick overview of this usage, check out [Ryan's demo on Twitter](https://twitter.com/remix_run/status/1658976420767604736).

### 次要变更

- Wrap internal router state updates with `React.startTransition` ([#10438](https://github.com/remix-run/react-router/pull/10438))

### 补丁变更

- Allow fetcher revalidations to complete if submitting fetcher is deleted ([#10535](https://github.com/remix-run/react-router/pull/10535))
- Re-throw `DOMException` (`DataCloneError`) when attempting to perform a `PUSH` navigation with non-serializable state. ([#10427](https://github.com/remix-run/react-router/pull/10427))
- Ensure revalidations happen when hash is present ([#10516](https://github.com/remix-run/react-router/pull/10516))
- Upgrade `jest` and `jsdom` ([#10453](https://github.com/remix-run/react-router/pull/10453))
- Updated dependencies:
  - `@remix-run/router@1.6.3` ([Changelog](https://github.com/remix-run/react-router/blob/main/packages/router/CHANGELOG.md#163))

**完整更新日志**: [`v6.11.2...v6.12.0`](https://github.com/remix-run/react-router/compare/react-router@6.11.2...react-router@6.12.0)

## v6.11.2

日期：2023-05-17

### 补丁变更

- Fix `basename` duplication in descendant `<Routes>` inside a `<RouterProvider>` ([#10492](https://github.com/remix-run/react-router/pull/10492))
- Fix bug where initial data load would not kick off when hash is present ([#10493](https://github.com/remix-run/react-router/pull/10493))
- Export `SetURLSearchParams` type ([#10444](https://github.com/remix-run/react-router/pull/10444))
- Fix Remix HMR-driven error boundaries by properly reconstructing new routes and `manifest` in `_internalSetRoutes` ([#10437](https://github.com/remix-run/react-router/pull/10437))

**完整更新日志**: [`v6.11.1...v6.11.2`](https://github.com/remix-run/react-router/compare/react-router@6.11.1...react-router@6.11.2)

## v6.11.1

日期：2023-05-03

### 补丁变更

- Fix usage of `Component` API within descendant `<Routes>` ([#10434](https://github.com/remix-run/react-router/pull/10434))
- Fix bug when calling `useNavigate` from `<Routes>` inside a `<RouterProvider>` ([#10432](https://github.com/remix-run/react-router/pull/10432))
- Fix usage of `<Navigate>` in strict mode when using a data router ([#10435](https://github.com/remix-run/react-router/pull/10435))
- Fix `basename` handling when navigating without a path ([#10433](https://github.com/remix-run/react-router/pull/10433))
- "Same hash" navigations no longer re-run loaders to match browser behavior (i.e. `/path#hash -> /path#hash`) ([#10408](https://github.com/remix-run/react-router/pull/10408))

**完整更新日志**: [`v6.11.0...v6.11.1`](https://github.com/remix-run/react-router/compare/react-router@6.11.0...react-router@6.11.1)

## v6.11.0

日期：2023-04-28

### 次要变更

- Enable `basename` support in `useFetcher` ([#10336](https://github.com/remix-run/react-router/pull/10336))
  - If you were previously working around this issue by manually prepending the `basename` then you will need to remove the manually prepended `basename` from your `fetcher` calls (`fetcher.load('/basename/route') -> fetcher.load('/route')`)
- Updated dependencies:
  - `@remix-run/router@1.6.0` ([Changelog](https://github.com/remix-run/react-router/blob/main/packages/router/CHANGELOG.md#160))

### 补丁变更

- When using a `RouterProvider`, `useNavigate`/`useSubmit`/`fetcher.submit` are now stable across location changes, since we can handle relative routing via the `@remix-run/router` instance and get rid of our dependence on `useLocation()` ([#10336](https://github.com/remix-run/react-router/pull/10336))
  - When using `BrowserRouter`, these hooks remain unstable across location changes because they still rely on `useLocation()`
- Fetchers should no longer revalidate on search params changes or routing to the same URL, and will only revalidate on `action` submissions or `router.revalidate` calls ([#10344](https://github.com/remix-run/react-router/pull/10344))
- Fix inadvertent re-renders when using `Component` instead of `element` on a route definition ([#10287](https://github.com/remix-run/react-router/pull/10287))
- Fail gracefully on `<Link to="//">` and other invalid URL values ([#10367](https://github.com/remix-run/react-router/pull/10367))
- Switched from `useSyncExternalStore` to `useState` for internal `@remix-run/router` router state syncing in `<RouterProvider>`. We found some [subtle bugs](https://codesandbox.io/s/use-sync-external-store-loop-9g7b81) where router state updates got propagated _before_ other normal `useState` updates, which could lead to foot guns in `useEffect` calls. ([#10377](https://github.com/remix-run/react-router/pull/10377), [#10409](https://github.com/remix-run/react-router/pull/10409))
- Log loader/action errors caught by the default error boundary to the console in dev for easier stack trace evaluation ([#10286](https://github.com/remix-run/react-router/pull/10286))
- Fix bug preventing rendering of descendant `<Routes>` when `RouterProvider` errors existed ([#10374](https://github.com/remix-run/react-router/pull/10374))
- Fix detection of `useNavigate` in the render cycle by setting the `activeRef` in a layout effect, allowing the `navigate` function to be passed to child components and called in a `useEffect` there ([#10394](https://github.com/remix-run/react-router/pull/10394))
- Allow `useRevalidator()` to resolve a loader-driven error boundary scenario ([#10369](https://github.com/remix-run/react-router/pull/10369))
- Enhance `LoaderFunction`/`ActionFunction` return type to prevent `undefined` from being a valid return value ([#10267](https://github.com/remix-run/react-router/pull/10267))
- Ensure proper 404 error on `fetcher.load` call to a route without a `loader` ([#10345](https://github.com/remix-run/react-router/pull/10345))
- Decouple `AbortController` usage between revalidating fetchers and the thing that triggered them such that the unmount/deletion of a revalidating fetcher doesn't impact the ongoing triggering navigation/revalidation ([#10271](https://github.com/remix-run/react-router/pull/10271))

**完整更新日志**: [`v6.10.0...v6.11.0`](https://github.com/remix-run/react-router/compare/react-router@6.10.0...react-router@6.11.0)

## v6.10.0

日期：2023-03-29

### 重要变更

We recently published a post over on the Remix Blog titled ["Future Proofing Your Remix App"](https://remix.run/blog/future-flags) that goes through our strategy to ensure smooth upgrades for your Remix and React Router apps going forward. React Router `6.10.0` adds support for these flags (for data routers) which you can specify when you create your router:

```js
const router = createBrowserRouter(routes, {
  future: {
    // specify future flags here
  },
});
```

You can also check out the docs [here](https://reactrouter.com/en/dev/guides/api-development-strategy) and [here](https://reactrouter.com/en/dev/routers/create-browser-router#future).

### 次要变更

#### `future.v7_normalizeFormMethod`

The first future flag being introduced is `future.v7_normalizeFormMethod` which will normalize the exposed `useNavigation()/useFetcher()` `formMethod` fields as uppercase HTTP methods to align with the `fetch()` (and some Remix) behavior. ([#10207](https://github.com/remix-run/react-router/pull/10207))

- When `future.v7_normalizeFormMethod` is unspecified or set to `false` (default v6 behavior),
  - `useNavigation().formMethod` is lowercase
  - `useFetcher().formMethod` is lowercase
- When `future.v7_normalizeFormMethod === true`:
  - `useNavigation().formMethod` is UPPERCASE
  - `useFetcher().formMethod` is UPPERCASE

### 补丁变更

- Fix `createStaticHandler` to also check for `ErrorBoundary` on routes in addition to `errorElement` ([#10190](https://github.com/remix-run/react-router/pull/10190))
- Fix route ID generation when using Fragments in `createRoutesFromElements` ([#10193](https://github.com/remix-run/react-router/pull/10193))
- Provide fetcher submission to `shouldRevalidate` if the fetcher action redirects ([#10208](https://github.com/remix-run/react-router/pull/10208))
- Properly handle `lazy()` errors during router initialization ([#10201](https://github.com/remix-run/react-router/pull/10201))
- Remove `instanceof` check for `DeferredData` to be resilient to ESM/CJS boundaries in SSR bundling scenarios ([#10247](https://github.com/remix-run/react-router/pull/10247))
- Update to latest `@remix-run/web-fetch@4.3.3` ([#10216](https://github.com/remix-run/react-router/pull/10216))

**完整更新日志**: [`v6.9.0...v6.10.0`](https://github.com/remix-run/react-router/compare/react-router@6.9.0...react-router@6.10.0)

## v6.9.0

日期：2023-03-10

### 重要变更

#### `Component`/`ErrorBoundary` route properties

React Router now supports an alternative way to define your route `element` and `errorElement` fields as React Components instead of React Elements. You can instead pass a React Component to the new `Component` and `ErrorBoundary` fields if you choose. There is no functional difference between the two, so use whichever approach you prefer 😀. You shouldn't be defining both, but if you do `Component`/`ErrorBoundary` will "win"

**Example JSON Syntax**

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

**Example JSX Syntax**

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

#### Introducing Lazy Route Modules

In order to keep your application bundles small and support code-splitting of your routes, we've introduced a new `lazy()` route property. This is an async function that resolves the non-route-matching portions of your route definition (`loader`, `action`, `element`/`Component`, `errorElement`/`ErrorBoundary`, `shouldRevalidate`, `handle`).

Lazy routes are resolved on initial load and during the `loading` or `submitting` phase of a navigation or fetcher call. You cannot lazily define route-matching properties (`path`, `index`, `children`) since we only execute your lazy route functions after we've matched known routes.

Your `lazy` functions will typically return the result of a dynamic import.

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

Then in your lazy route modules, export the properties you want defined for the route:

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

An example of this in action can be found in the [`examples/lazy-loading-router-provider`](https://github.com/remix-run/react-router/tree/main/examples/lazy-loading-router-provider) directory of the repository. For more info, check out the [`lazy` docs](https://reactrouter.com/v6/route/lazy).

🙌 Huge thanks to @rossipedia for the [Initial Proposal](https://github.com/remix-run/react-router/discussions/9826) and [POC Implementation](https://github.com/remix-run/react-router/pull/9830).

### 次要变更

- Add support for `route.Component`/`route.ErrorBoundary` properties ([#10045](https://github.com/remix-run/react-router/pull/10045))
- Add support for `route.lazy` ([#10045](https://github.com/remix-run/react-router/pull/10045))

### 补丁变更

- Improve memoization for context providers to avoid unnecessary re-renders ([#9983](https://github.com/remix-run/react-router/pull/9983))
- Fix `generatePath` incorrectly applying parameters in some cases ([#10078](https://github.com/remix-run/react-router/pull/10078))
- `[react-router-dom-v5-compat]` Add missed data router API re-exports ([#10171](https://github.com/remix-run/react-router/pull/10171))

**完整更新日志**: [`v6.8.2...v6.9.0`](https://github.com/remix-run/react-router/compare/react-router@6.8.2...react-router@6.9.0)

## v6.8.2

日期：2023-02-27

### 补丁变更

- Treat same-origin absolute URLs in `<Link to>` as external if they are outside of the router `basename` ([#10135](https://github.com/remix-run/react-router/pull/10135))
- Correctly perform a hard redirect for same-origin absolute URLs outside of the router `basename` ([#10076](https://github.com/remix-run/react-router/pull/10076))
- Fix SSR of absolute `<Link to>` urls ([#10112](https://github.com/remix-run/react-router/pull/10112))
- Properly escape HTML characters in `StaticRouterProvider` serialized hydration data ([#10068](https://github.com/remix-run/react-router/pull/10068))
- Fix `useBlocker` to return `IDLE_BLOCKER` during SSR ([#10046](https://github.com/remix-run/react-router/pull/10046))
- Ensure status code and headers are maintained for `defer` loader responses in `createStaticHandler`'s `query()` method ([#10077](https://github.com/remix-run/react-router/pull/10077))
- Change `invariant` to an `UNSAFE_invariant` export since it's only intended for internal use ([#10066](https://github.com/remix-run/react-router/pull/10066))

**完整更新日志**: [`v6.8.1...v6.8.2`](https://github.com/remix-run/react-router/compare/react-router@6.8.1...react-router@6.8.2)

## v6.8.1

日期：2023-02-06

### 补丁变更

- Remove inaccurate console warning for POP navigations and update active blocker logic ([#10030](https://github.com/remix-run/react-router/pull/10030))
- Only check for differing origin on absolute URL redirects ([#10033](https://github.com/remix-run/react-router/pull/10033))
- Improved absolute url detection in `Link` component (now also supports `mailto:` urls) ([#9994](https://github.com/remix-run/react-router/pull/9994))
- Fix partial object (search or hash only) pathnames losing current path value ([#10029](https://github.com/remix-run/react-router/pull/10029))

**完整更新日志**: [`v6.8.0...v6.8.1`](https://github.com/remix-run/react-router/compare/react-router@6.8.0...react-router@6.8.1)

## v6.8.0

日期：2023-01-26

### 次要变更

Support absolute URLs in `<Link to>`. If the URL is for the current origin, it will still do a client-side navigation. If the URL is for a different origin then it will do a fresh document request for the new origin. ([#9900](https://github.com/remix-run/react-router/pull/9900))

```tsx
<Link to="https://neworigin.com/some/path">    {/* Document request */}
<Link to="//neworigin.com/some/path">          {/* Document request */}
<Link to="https://www.currentorigin.com/path"> {/* Client-side navigation */}
```

### 补丁变更

- Fixes 2 separate issues for revalidating fetcher `shouldRevalidate` calls ([#9948](https://github.com/remix-run/react-router/pull/9948))
  - The `shouldRevalidate` function was only being called for _explicit_ revalidation scenarios (after a mutation, manual `useRevalidator` call, or an `X-Remix-Revalidate` header used for cookie setting in Remix). It was not properly being called on _implicit_ revalidation scenarios that also apply to navigation `loader` revalidation, such as a change in search params or clicking a link for the page we're already on. It's now correctly called in those additional scenarios.
  - The parameters being passed were incorrect and inconsistent with one another since the `current*`/`next*` parameters reflected the static `fetcher.load` URL (and thus were identical). Instead, they should have reflected the navigation that triggered the revalidation (as the `form*` parameters did). These parameters now correctly reflect the triggering navigation.
- Fix bug with search params removal via `useSearchParams` ([#9969](https://github.com/remix-run/react-router/pull/9969))
- Respect `preventScrollReset` on `<fetcher.Form>` ([#9963](https://github.com/remix-run/react-router/pull/9963))
- Fix navigation for hash routers on manual URL changes ([#9980](https://github.com/remix-run/react-router/pull/9980))
- Use `pagehide` instead of `beforeunload` for `<ScrollRestoration>`. This has better cross-browser support, specifically on Mobile Safari. ([#9945](https://github.com/remix-run/react-router/pull/9945))
- Do not short circuit on hash change only mutation submissions ([#9944](https://github.com/remix-run/react-router/pull/9944))
- Remove `instanceof` check from `isRouteErrorResponse` to avoid bundling issues on the server ([#9930](https://github.com/remix-run/react-router/pull/9930))
- Detect when a `defer` call only contains critical data and remove the `AbortController` ([#9965](https://github.com/remix-run/react-router/pull/9965))
- Send the name as the value when url-encoding `File` `FormData` entries ([#9867](https://github.com/remix-run/react-router/pull/9867))
- `react-router-dom-v5-compat` - 修复SSR `useLayoutEffect` `console.error` when using `CompatRouter` ([#9820](https://github.com/remix-run/react-router/pull/9820))

**完整更新日志**: [`v6.7.0...v6.8.0`](https://github.com/remix-run/react-router/compare/react-router@6.7.0...react-router@6.8.0)

## v6.7.0

日期：2023-01-18

### 次要变更

- Add `unstable_useBlocker`/`unstable_usePrompt` hooks for blocking navigations within the app's location origin ([#9709](https://github.com/remix-run/react-router/pull/9709), [#9932](https://github.com/remix-run/react-router/pull/9932))
- Add `preventScrollReset` prop to `<Form>` ([#9886](https://github.com/remix-run/react-router/pull/9886))

### 补丁变更

- Added pass-through event listener options argument to `useBeforeUnload` ([#9709](https://github.com/remix-run/react-router/pull/9709))
- Fix `generatePath` when optional params are present ([#9764](https://github.com/remix-run/react-router/pull/9764))
- Update `<Await>` to accept `ReactNode` as children function return result ([#9896](https://github.com/remix-run/react-router/pull/9896))
- Improved absolute redirect url detection in actions/loaders ([#9829](https://github.com/remix-run/react-router/pull/9829))
- Fix URL creation with memory histories ([#9814](https://github.com/remix-run/react-router/pull/9814))
- Fix scroll reset if a submission redirects ([#9886](https://github.com/remix-run/react-router/pull/9886))
- Fix 404 bug with same-origin absolute redirects ([#9913](https://github.com/remix-run/react-router/pull/9913))
- Streamline `jsdom` bug workaround in tests ([#9824](https://github.com/remix-run/react-router/pull/9824))

**完整更新日志**: [`v6.6.2...v6.7.0`](https://github.com/remix-run/react-router/compare/react-router@6.6.2...react-router@6.7.0)

## v6.6.2

日期：2023-01-09

### 补丁变更

- Ensure `useId` consistency during SSR ([#9805](https://github.com/remix-run/react-router/pull/9805))

**完整更新日志**: [`v6.6.1...v6.6.2`](https://github.com/remix-run/react-router/compare/react-router@6.6.1...react-router@6.6.2)

## v6.6.1

日期：2022-12-23

### 补丁变更

- Include submission info in `shouldRevalidate` on action redirects ([#9777](https://github.com/remix-run/react-router/pull/9777), [#9782](https://github.com/remix-run/react-router/pull/9782))
- Reset `actionData` on action redirect to current location ([#9772](https://github.com/remix-run/react-router/pull/9772))

**完整更新日志**: [`v6.6.0...v6.6.1`](https://github.com/remix-run/react-router/compare/react-router@6.6.0...react-router@6.6.1)

## v6.6.0

日期：2022-12-21

### 重要变更

This minor release is primarily to stabilize our SSR APIs for Data Routers now that we've wired up the new `RouterProvider` in Remix as part of the [React Router-ing Remix](https://remix.run/blog/react-routering-remix) work.

### 次要变更

- Remove `unstable_` prefix from `createStaticHandler`/`createStaticRouter`/`StaticRouterProvider` ([#9738](https://github.com/remix-run/react-router/pull/9738))
- Add `useBeforeUnload()` hook ([#9664](https://github.com/remix-run/react-router/pull/9664))

### 补丁变更

- Support uppercase `<Form method>` and `useSubmit` method values ([#9664](https://github.com/remix-run/react-router/pull/9664))
- Fix `<button formmethod>` form submission overriddes ([#9664](https://github.com/remix-run/react-router/pull/9664))
- Fix explicit `replace` on submissions and `PUSH` on submission to new paths ([#9734](https://github.com/remix-run/react-router/pull/9734))
- Prevent `useLoaderData` usage in `errorElement` ([#9735](https://github.com/remix-run/react-router/pull/9735))
- Proper hydration of `Error` objects from `StaticRouterProvider` ([#9664](https://github.com/remix-run/react-router/pull/9664))
- Skip initial scroll restoration for SSR apps with `hydrationData` ([#9664](https://github.com/remix-run/react-router/pull/9664))
- Fix a few bugs where loader/action data wasn't properly cleared on errors ([#9735](https://github.com/remix-run/react-router/pull/9735))

**完整更新日志**: [`v6.5.0...v6.6.0`](https://github.com/remix-run/react-router/compare/react-router@6.5.0...react-router@6.6.0)

## v6.5.0

日期：2022-12-16

### 重要变更

This release introduces support for [Optional Route Segments](https://github.com/remix-run/react-router/issues/9546). Now, adding a `?` to the end of any path segment will make that entire segment optional. This works for both static segments and dynamic parameters.

**Optional Params Examples**

- `<Route path=":lang?/about>` will match:
  - `/:lang/about`
  - `/about`
- `<Route path="/multistep/:widget1?/widget2?/widget3?">` will match:
  - `/multistep`
  - `/multistep/:widget1`
  - `/multistep/:widget1/:widget2`
  - `/multistep/:widget1/:widget2/:widget3`

**Optional Static Segment Example**

- `<Route path="/home?">` will match:
  - `/`
  - `/home`
- `<Route path="/fr?/about">` will match:
  - `/about`
  - `/fr/about`

### 次要变更

- Allows optional routes and optional static segments ([#9650](https://github.com/remix-run/react-router/pull/9650))

### 补丁变更

- Stop incorrectly matching on partial named parameters, i.e. `<Route path="prefix-:param">`, to align with how splat parameters work. If you were previously relying on this behavior then it's recommended to extract the static portion of the path at the `useParams` call site: ([#9506](https://github.com/remix-run/react-router/pull/9506))

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

- Persist `headers` on `loader` `request`'s after SSR document `action` request ([#9721](https://github.com/remix-run/react-router/pull/9721))
- Fix requests sent to revalidating loaders so they reflect a GET request ([#9660](https://github.com/remix-run/react-router/pull/9660))
- Fix issue with deeply nested optional segments ([#9727](https://github.com/remix-run/react-router/pull/9727))
- GET forms now expose a submission on the loading navigation ([#9695](https://github.com/remix-run/react-router/pull/9695))
- Fix error boundary tracking for multiple errors bubbling to the same boundary ([#9702](https://github.com/remix-run/react-router/pull/9702))

**完整更新日志**: [`v6.4.5...v6.5.0`](https://github.com/remix-run/react-router/compare/react-router@6.4.5...react-router@6.5.0)

## v6.4.5

日期：2022-12-07

### 补丁变更

- Fix requests sent to revalidating loaders so they reflect a `GET` request ([#9680](https://github.com/remix-run/react-router/pull/9680))
- Remove `instanceof Response` checks in favor of `isResponse` ([#9690](https://github.com/remix-run/react-router/pull/9690))
- Fix `URL` creation in Cloudflare Pages or other non-browser-environments ([#9682](https://github.com/remix-run/react-router/pull/9682), [#9689](https://github.com/remix-run/react-router/pull/9689))
- Add `requestContext` support to static handler `query`/`queryRoute` ([#9696](https://github.com/remix-run/react-router/pull/9696))
  - Note that the unstable API of `queryRoute(path, routeId)` has been changed to `queryRoute(path, { routeId, requestContext })`

**完整更新日志**: [`v6.4.4...v6.4.5`](https://github.com/remix-run/react-router/compare/react-router@6.4.4...react-router@6.4.5)

## v6.4.4

日期：2022-11-30

### 补丁变更

- Throw an error if an `action`/`loader` function returns `undefined` as revalidations need to know whether the loader has previously been executed. `undefined` also causes issues during SSR stringification for hydration. You should always ensure your `loader`/`action` returns a value, and you may return `null` if you don't wish to return anything. ([#9511](https://github.com/remix-run/react-router/pull/9511))
- Properly handle redirects to external domains ([#9590](https://github.com/remix-run/react-router/pull/9590), [#9654](https://github.com/remix-run/react-router/pull/9654))
- Preserve the HTTP method on 307/308 redirects ([#9597](https://github.com/remix-run/react-router/pull/9597))
- Support `basename` in static data routers ([#9591](https://github.com/remix-run/react-router/pull/9591))
- Enhanced `ErrorResponse` bodies to contain more descriptive text in internal 403/404/405 scenarios
- Fix issues with encoded characters in `NavLink` and descendant `<Routes>` ([#9589](https://github.com/remix-run/react-router/pull/9589), [#9647](https://github.com/remix-run/react-router/pull/9647))
- Properly serialize/deserialize `ErrorResponse` instances when using built-in hydration ([#9593](https://github.com/remix-run/react-router/pull/9593))
- Support `basename` in static data routers ([#9591](https://github.com/remix-run/react-router/pull/9591))
- Updated dependencies:
  - `@remix-run/router@1.0.4`
  - `react-router@6.4.4`

**完整更新日志**: [`v6.4.3...v6.4.4`](https://github.com/remix-run/react-router/compare/react-router-dom@6.4.3...react-router-dom@6.4.4)

## v6.4.3

日期：2022-11-01

### 补丁变更

- Generate correct `<a href>` values when using `createHashRouter` ([#9409](https://github.com/remix-run/react-router/pull/9409))
- Better handle encoding/matching with special characters in URLs and route paths ([#9477](https://github.com/remix-run/react-router/pull/9477), [#9496](https://github.com/remix-run/react-router/pull/9496))
- Generate correct `formAction` pathnames when an `index` route also has a `path` ([#9486](https://github.com/remix-run/react-router/pull/9486))
- Respect `relative=path` prop on `NavLink` ([#9453](https://github.com/remix-run/react-router/pull/9453))
- Fix `NavLink` behavior for root urls ([#9497](https://github.com/remix-run/react-router/pull/9497))
- `useRoutes` should be able to return `null` when passing `locationArg` ([#9485](https://github.com/remix-run/react-router/pull/9485))
- Fix `initialEntries` type in `createMemoryRouter` ([#9498](https://github.com/remix-run/react-router/pull/9498))
- Support `basename` and relative routing in `loader`/`action` redirects ([#9447](https://github.com/remix-run/react-router/pull/9447))
- Ignore pathless layout routes when looking for proper submission `action` function ([#9455](https://github.com/remix-run/react-router/pull/9455))
- Add UMD build for `@remix-run/router` ([#9446](https://github.com/remix-run/react-router/pull/9446))
- Fix `createURL` in local file execution in Firefox ([#9464](https://github.com/remix-run/react-router/pull/9464))

**完整更新日志**: [`v6.4.2...v6.4.3`](https://github.com/remix-run/react-router/compare/react-router@6.4.2...react-router@6.4.3)

## v6.4.2

日期：2022-10-06

### 补丁变更

- Respect `basename` in `useFormAction` ([#9352](https://github.com/remix-run/react-router/pull/9352))
- Fix `IndexRouteObject` and `NonIndexRouteObject` types to make `hasErrorElement` optional ([#9394](https://github.com/remix-run/react-router/pull/9394))
- Enhance console error messages for invalid usage of data router hooks ([#9311](https://github.com/remix-run/react-router/pull/9311))
- If an index route has children, it will result in a runtime error. We have strengthened our `RouteObject`/`RouteProps` types to surface the error in TypeScript. ([#9366](https://github.com/remix-run/react-router/pull/9366))

**完整更新日志**: [`v6.4.1...v6.4.2`](https://github.com/remix-run/react-router/compare/react-router@6.4.1...react-router@6.4.2)

## v6.4.1

日期：2022-09-22

### 补丁变更

- Preserve state from `initialEntries` ([#9288](https://github.com/remix-run/react-router/pull/9288))
- Preserve `?index` for fetcher get submissions to index routes ([#9312](https://github.com/remix-run/react-router/pull/9312))

**完整更新日志**: [`v6.4.0...v6.4.1`](https://github.com/remix-run/react-router/compare/react-router@6.4.0...react-router@6.4.1)

## v6.4.0

日期：2022-09-13

### 重要变更

#### Remix Data APIs

Whoa this is a big one! `6.4.0` brings all the data loading and mutation APIs over from Remix. Here's a quick high level overview, but it's recommended you go check out the [docs](https://reactrouter.com/), especially the [feature overview](https://reactrouter.com/en/6.4.0/start/overview) and the [tutorial](https://reactrouter.com/en/6.4.0/start/tutorial).

**New `react-router` APIs**

- Create your router with `createMemoryRouter`
- Render your router with `<RouterProvider>`
- Load data with a Route `loader` and mutate with a Route `action`
- Handle errors with Route `errorElement`
- Defer non-critical data with `defer` and `Await`

**New `react-router-dom` APIs**

- Create your router with `createBrowserRouter`/`createHashRouter`
- Submit data with the new `<Form>` component
- Perform in-page data loads and mutations with `useFetcher()`
- Defer non-critical data with `defer` and `Await`
- Manage scroll position with `<ScrollRestoration>`
- Perform path-relative navigations with `<Link relative="path">` ([#9160](https://github.com/remix-run/react-router/pull/9160))

### 补丁变更

- Path resolution is now trailing slash agnostic ([#8861](https://github.com/remix-run/react-router/pull/8861))
- `useLocation` returns the scoped location inside a `<Routes location>` component ([#9094](https://github.com/remix-run/react-router/pull/9094))
- Respect the `<Link replace>` prop if it is defined ([#8779](https://github.com/remix-run/react-router/pull/8779))

**完整更新日志**: [`v6.3.0...v6.4.0`](https://github.com/remix-run/react-router/compare/v6.3.0...react-router%406.4.0)

## v6.3.0

日期：2022-03-31

### 次要变更

- Added the v5 to v6 backwards compatibility package 💜 ([#8752](https://github.com/remix-run/react-router/pull/8752)). The official guide can be found [in this discussion](https://github.com/remix-run/react-router/discussions/8753)

**完整更新日志**: [`v6.2.2...v6.3.0`](https://github.com/remix-run/react-router/compare/v6.2.2...v6.3.0)

## v6.2.2

日期：2022-02-28

### 补丁变更

- Fixed nested splat routes that begin with special URL-safe characters ([#8563](https://github.com/remix-run/react-router/pull/8563))
- Fixed a bug where index routes were missing route context in some cases ([#8497](https://github.com/remix-run/react-router/pull/8497))

**完整更新日志**: [`v6.2.1...v6.2.2`](https://github.com/remix-run/react-router/compare/v6.2.1...v6.2.2)

## v6.2.1

日期：2021-12-17

### 补丁变更

- This release updates the internal `history` dependency to `5.2.0`.

**完整更新日志**: [`v6.2.0...v6.2.1`](https://github.com/remix-run/react-router/compare/v6.2.0...v6.2.1)

## v6.2.0

日期：2021-12-17

### 次要变更

- We now use statically analyzable CJS exports. This enables named imports in Node ESM scripts ([See the commit](https://github.com/remix-run/react-router/commit/29c7fc8b5f853b0b06ecd0f5682a9bbe6eca0715)).

### 补丁变更

- Fixed the `RouteProps` `element` type, which should be a `ReactNode` ([#8473](https://github.com/remix-run/react-router/pull/8473))
- Fixed a bug with `useOutlet` for top-level routes ([#8483](https://github.com/remix-run/react-router/pull/8483))

**完整更新日志**: [`v6.1.1...v6.2.0`](https://github.com/remix-run/react-router/compare/v6.1.1...v6.2.0)

## v6.1.1

日期：2021-12-11

### 补丁变更

- In v6.1.0 we inadvertently shipped a new, undocumented API that will likely introduce bugs ([#7586](https://github.com/remix-run/react-router/pull/7586)). We have flagged `HistoryRouter` as `unstable_HistoryRouter`, as this API will likely need to change before a new major release.

**完整更新日志**: [`v6.1.0...v6.1.1`](https://github.com/remix-run/react-router/compare/v6.1.0...v6.1.1)

## v6.1.0

日期：2021-12-10

### 次要变更

- `<Outlet>` can now receive a `context` prop. This value is passed to child routes and is accessible via the new `useOutletContext` hook. See [the API docs](https://reactrouter.com/docs/en/v6/api#useoutletcontext) for details. ([#8461](https://github.com/remix-run/react-router/pull/8461))
- `<NavLink>` can now receive a child function for access to its props. ([#8164](https://github.com/remix-run/react-router/pull/8164))
- Improved TypeScript signature for `useMatch` and `matchPath`. For example, when you call `useMatch("foo/:bar/:baz")`, the path is parsed and the return type will be `PathMatch<"bar" | "baz">`. ([#8030](https://github.com/remix-run/react-router/pull/8030))

### 补丁变更

- Fixed a bug that broke support for base64 encoded IDs on nested routes ([#8291](https://github.com/remix-run/react-router/pull/8291))
- A few error message improvements ([#8202](https://github.com/remix-run/react-router/pull/8202))

**完整更新日志**: [`v6.0.2...v6.1.0`](https://github.com/remix-run/react-router/compare/v6.0.2...v6.1.0)

## v6.0.2

日期：2021-11-09

### 补丁变更

- Added the `reloadDocument` prop to `<Link>`. This allows `<Link>` to function like a normal anchor tag by reloading the document after navigation while maintaining the relative `to` resolution ([#8283](https://github.com/remix-run/react-router/pull/8283))

**完整更新日志**: [`v6.0.1...v6.0.2`](https://github.com/remix-run/react-router/compare/v6.0.1...v6.0.2)

## v6.0.1

日期：2021-11-05

### 补丁变更

- Add a default `<StaticRouter location>` value ([#8243](https://github.com/remix-run/react-router/pull/8243))
- Add invariant for using `<Route>` inside `<Routes>` to help people make the change ([#8238](https://github.com/remix-run/react-router/pull/8238))

**完整更新日志**: [`v6.0.0...v6.0.1`](https://github.com/remix-run/react-router/compare/v6.0.0...v6.0.1)

## v6.0.0

日期：2021-11-03

React Router v6 is here!

Please go read [our blog post for more information on all the great stuff in v6](https://remix.run/blog/react-router-v6) including [notes about how to upgrade from React Router v5](https://remix.run/blog/react-router-v6#upgrading-to-react-router-v6) and Reach Router.
