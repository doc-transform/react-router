---
title: HistoryRouter
unstable: true
---

# unstable_HistoryRouter

[MODES: declarative]

<br />
<br />

<docs-warning>此 API 是实验性的，可能在次要/补丁版本中发生破坏性变更。请谨慎使用，并**密切**关注发布说明中的相关变更。</docs-warning>

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.unstable_HistoryRouter.html)

接受预实例化的 `history` 对象的声明式 [`<Router>`](../declarative-routers/Router)。需要注意，强烈不建议使用自己的 `history` 对象，除非你使用与 React Router 内部相同版本的 `history` 库，否则可能会在你的包中包含两个版本的 `history` 库。

## 函数签名

```tsx
function HistoryRouter({
  basename,
  children,
  history,
  unstable_useTransitions,
}: HistoryRouterProps);
```

## Props

### basename

应用的基础路径。

### children

描述路由配置的 `<Route>` 组件。

### history

供路由器使用的 `History` 实现。

### unstable_useTransitions

控制路由器状态更新是否在内部包裹在 [`React.startTransition`](https://react.dev/reference/react/startTransition) 中。

- 当设置为 `undefined` 时，所有路由器状态更新都会被包裹在 `React.startTransition` 中。
- 当设置为 `true` 时，[`Link`](../components/Link) 和 [`Form`](../components/Form) 导航会被包裹在 `React.startTransition` 中，所有路由器状态更新都会被包裹在 `React.startTransition` 中。
- 当设置为 `false` 时，路由器不会在任何导航或状态变化上使用 `React.startTransition`。

更多信息请参阅[文档](https://reactrouter.com/explanation/react-transitions)。
