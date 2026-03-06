---
title: HydratedRouter
---

# HydratedRouter

[MODES: framework]

## 概述

框架模式路由器组件，用于从 [`ServerRouter`](../framework-routers/ServerRouter) 注水路由器。参见 [`entry.client.tsx`](../framework-conventions/entry.client.tsx)。

## 函数签名

```tsx
function HydratedRouter(props: HydratedRouterProps);
```

## Props

### getContext

上下文工厂函数，传递给 [`createBrowserRouter`](../data-routers/createBrowserRouter)。此函数会在每次导航/fetch 时调用以创建新的 `context` 实例，并提供给 [`clientAction`](../../start/framework/route-module#clientAction)/[`clientLoader`](../../start/framework/route-module#clientLoader) 函数。

### onError

错误处理函数，会在应用中遇到的任何中间件、loader、action 或渲染错误时调用。这对于记录日志或上报错误很有用，因为它不受重新渲染的影响，每个错误只运行一次。

`errorInfo` 参数来自 [`componentDidCatch`](https://react.dev/reference/react/Component#componentdidcatch)，仅在渲染错误时存在。

```tsx
<HydratedRouter onError=(error, info) => {
  let { location, params, unstable_pattern, errorInfo } = info;
  console.error(error, location, errorInfo);
  reportToErrorService(error, location, errorInfo);
}} />
```
