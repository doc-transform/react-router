---
title: 注水
hidden: true
---

关于 `HydrateFallback` 的行为有一些值得注意的细微差别：

- 它只与初始文档请求和注水相关，在后续的客户端导航中不会被渲染
- 只有在给定路由上同时设置了 [`clientLoader.hydrate=true`][hydrate-true] 时才相关
- 如果你有 `clientLoader` 但没有服务端 `loader`，它也是相关的，因为这意味着 `clientLoader.hydrate=true`，因为否则 `useLoaderData` 没有任何 loader 数据可以返回
  - 即使在这种情况下你没有指定 `HydrateFallback`，React Router 也不会渲染你的路由组件，而是会冒泡到任何祖先的 `HydrateFallback` 组件
  - 这是为了确保 `useLoaderData` 保持"正常路径"
  - 没有服务端 `loader` 时，`useLoaderData` 将在任何渲染的路由组件中返回 `undefined`
- 你不能在 `HydrateFallback` 中渲染 `<Outlet/>`，因为如果子路由的祖先 loader 数据尚不可用（即它们在注水时运行 `clientLoader` 函数的用例，如 `useRouteLoaderData()` 或 `useMatches()`），子路由无法保证正常工作
