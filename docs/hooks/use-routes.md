---
title: useRoutes
---

# `useRoutes`

<details>
  <summary>类型声明</summary>

```tsx
declare function useRoutes(
  routes: RouteObject[],
  location?: Partial<Location> | string;
): React.ReactElement | null;
```

</details>

`useRoutes` hook 是 [`<Routes>`][routes] 的函数式等价物，但它使用 JavaScript 对象而不是 `<Route>` 元素来定义路由。这些对象具有与普通 [`<Route>` 元素][route]相同的属性，但不需要 JSX。

`useRoutes` 的返回值要么是可用于渲染路由树的有效 React 元素，要么在没有匹配时为 `null`。

```tsx
import * as React from "react";
import { useRoutes } from "react-router-dom";

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

[routes]: ../components/routes
[route]: ../components/route
