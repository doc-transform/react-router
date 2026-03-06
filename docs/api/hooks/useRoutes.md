---
title: useRoutes
---

# useRoutes

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useRoutes.html)

[`<Routes>`](../components/Routes) 的 Hook 版本，使用对象而非组件。这些对象具有与组件 props 相同的属性。`useRoutes` 的返回值是一个可用于渲染路由树的有效 React 元素，如果没有匹配则返回 `null`。

```tsx
import { useRoutes } from "react-router";

function App() {
  let element = useRoutes([
    {
      path: "/",
      element: <Dashboard />,
      children: [
        {
          path: "messages",
          element: <DashboardMessages />,
        },
        { path: "tasks", element: <DashboardTasks /> },
      ],
    },
    { path: "team", element: <AboutPage /> },
  ]);

  return element;
}
```

## 函数签名

```tsx
function useRoutes(
  routes: RouteObject[],
  locationArg?: Partial<Location> | string,
): React.ReactElement | null;
```

## 参数

### routes

定义路由层级的 [`RouteObject`](https://api.reactrouter.com/v7/types/react-router.RouteObject.html) 数组。

### locationArg

可选的 [`Location`](https://api.reactrouter.com/v7/interfaces/react-router.Location.html) 对象或路径名字符串，用于替代当前 [`Location`](https://api.reactrouter.com/v7/interfaces/react-router.Location.html)。

## 返回值

用于渲染匹配路由的 React 元素，如果没有匹配的路由则返回 `null`。
