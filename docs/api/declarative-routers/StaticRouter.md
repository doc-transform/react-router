---
title: StaticRouter
---

# StaticRouter

[MODES: declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.StaticRouter.html)

不会导航到其他 [`Location`](https://api.reactrouter.com/v7/interfaces/react-router.Location.html) 的 [`<Router>`](../declarative-routers/Router)。适用于没有有状态 UI 的服务端。

## 函数签名

```tsx
function StaticRouter({
  basename,
  children,
  location: locationProp = "/",
}: StaticRouterProps);
```

## Props

### basename

静态路由器的基础 URL（默认：`/`）。

### children

在静态路由器内部渲染的子元素。

### location

静态路由器要渲染的 [`Location`](https://api.reactrouter.com/v7/interfaces/react-router.Location.html)（默认：`/`）。
