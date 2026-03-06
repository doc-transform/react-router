---
title: HashRouter
---

# HashRouter

[MODES: declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.HashRouter.html)

声明式 [`<Router>`](../declarative-routers/Router)，将位置存储在 URL 的 [`hash`](https://developer.mozilla.org/en-US/docs/Web/API/URL/hash) 部分，因此不会发送到服务器。

## 函数签名

```tsx
function HashRouter({
  basename,
  children,
  unstable_useTransitions,
  window,
}: HashRouterProps);
```

## Props

### basename

应用的基础路径。

### children

描述路由配置的 `<Route>` 组件。

### unstable_useTransitions

控制路由器状态更新是否在内部包裹在 [`React.startTransition`](https://react.dev/reference/react/startTransition) 中。

- 当值为 `undefined` 时，所有路由器状态更新都包裹在 `React.startTransition` 中
- 当设为 `true` 时，[`Link`](../components/Link) 和 [`Form`](../components/Form) 导航会包裹在 `React.startTransition` 中
- 当设为 `false` 时，路由器不会使用 `React.startTransition`

更多信息请参阅[文档](https://reactrouter.com/explanation/react-transitions)。

### window

[`Window`](https://developer.mozilla.org/en-US/docs/Web/API/Window) 对象覆盖。默认为全局 `window` 实例。
