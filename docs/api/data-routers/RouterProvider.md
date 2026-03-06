---
title: RouterProvider
---

# RouterProvider

[MODES: data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.RouterProvider.html)

渲染给定 [`DataRouter`](https://api.reactrouter.com/v7/interfaces/react-router.DataRouter.html) 的 UI。此组件通常位于应用元素树的顶层。

```tsx
import { createBrowserRouter } from "react-router";
import { RouterProvider } from "react-router/dom";
import { createRoot } from "react-dom/client";

const router = createBrowserRouter(routes);
createRoot(document.getElementById("root")).render(
  <RouterProvider router={router} />,
);
```

<docs-info>请注意此组件同时从 `react-router` 和 `react-router/dom` 导出，唯一的区别是后者自动接入了 `react-dom` 的 [`flushSync`](https://react.dev/reference/react-dom/flushSync) 实现。除非你在非 DOM 环境中运行，否则几乎总是应该使用 `react-router/dom` 的版本。</docs-info>

## 函数签名

```tsx
function RouterProvider({
  router,
  flushSync: reactDomFlushSyncImpl,
  onError,
  unstable_useTransitions,
}: RouterProviderProps): React.ReactElement;
```

## Props

### flushSync

[`ReactDOM.flushSync`](https://react.dev/reference/react-dom/flushSync) 的实现，用于刷新更新。

通常你不需要关心这个：

- 从 `react-router/dom` 导出的 `RouterProvider` 会在内部为你处理
- 如果你在非 DOM 环境中渲染，可以从 `react-router` 导入 `RouterProvider` 并忽略此 prop

### onError

错误处理函数，会在应用中遇到的任何中间件、loader、action 或渲染错误时调用。这对于记录日志或上报错误很有用，因为它不受重新渲染的影响，每个错误只运行一次，比在 `ErrorBoundary` 中处理更可靠。

`errorInfo` 参数来自 [`componentDidCatch`](https://react.dev/reference/react/Component#componentdidcatch)，仅在渲染错误时存在。

```tsx
<RouterProvider onError=(error, info) => {
  let { location, params, unstable_pattern, errorInfo } = info;
  console.error(error, location, errorInfo);
  reportToErrorService(error, location, errorInfo);
}} />
```

### router

用于导航和数据获取的 [`DataRouter`](https://api.reactrouter.com/v7/interfaces/react-router.DataRouter.html) 实例。

### unstable_useTransitions

控制路由器状态更新是否在内部包裹在 [`React.startTransition`](https://react.dev/reference/react/startTransition) 中。

- 当值为 `undefined` 时，所有状态更新都包裹在 `React.startTransition` 中
  - 如果你自己将导航/fetcher 包裹在 `startTransition` 中，这可能导致异常行为。
- 当设为 `true` 时，[`Link`](../components/Link) 和 [`Form`](../components/Form) 导航会包裹在 `React.startTransition` 中，路由器状态变化也会包裹在 `React.startTransition` 中并通过 [`useOptimistic`](https://react.dev/reference/react/useOptimistic) 将导航中的路由器状态变化展示给 UI。
- 当设为 `false` 时，路由器不会在任何导航或状态变化中使用 `React.startTransition` 或 `React.useOptimistic`。

更多信息请参阅[文档](https://reactrouter.com/explanation/react-transitions)。
