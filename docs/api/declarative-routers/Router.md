---
title: Router
---

# Router

[MODES: declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.Router.html)

为应用的其余部分提供 location 上下文。

注意：你通常不会直接渲染 `<Router>`。而是渲染一个更适合你环境的路由器，如 Web 浏览器中的 [`BrowserRouter`](../declarative-routers/BrowserRouter) 或服务端渲染的 [`ServerRouter`](../framework-routers/ServerRouter)。

## 函数签名

```tsx
function Router({
  basename: basenameProp = "/",
  children = null,
  location: locationProp,
  navigationType = NavigationType.Pop,
  navigator,
  static: staticProp = false,
  unstable_useTransitions,
}: RouterProps): React.ReactElement | null;
```

## Props

### basename

应用的基础路径。此值会被添加到所有 location 前面。

### children

描述路由树的嵌套 [`Route`](../components/Route) 元素。

### location

要匹配的 location。默认为当前 location。可以是字符串或 [`Location`](https://api.reactrouter.com/v7/interfaces/react-router.Location.html) 对象。

### navigationType

触发此 `location` 变化的导航类型。默认为 `NavigationType.Pop`。

### navigator

用于导航的导航器。通常是 history 对象或实现 [`Navigator`](https://api.reactrouter.com/v7/interfaces/react-router.Navigator.html) 接口的自定义导航器。

### static

此路由器是否是静态的（用于 SSR）。如果为 `true`，路由器不会对 location 变化作出响应。

### unstable_useTransitions

控制路由器状态更新是否在内部包裹在 [`React.startTransition`](https://react.dev/reference/react/startTransition) 中。

更多信息请参阅[文档](https://reactrouter.com/explanation/react-transitions)。
