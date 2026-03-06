---
title: createRoutesFromElements
---

# `createRoutesFromElements`

`createRoutesFromElements` 是一个辅助函数，用于从 `<Route>` 元素创建路由对象。如果你更喜欢用 JSX 而不是对象来创建路由，它会很有用。

```jsx
import {
  createBrowserRouter,
  createRoutesFromElements,
  RouterProvider,
  Route,
} from "react-router-dom";

// You can do this:
const router = createBrowserRouter(
  createRoutesFromElements(
    <Route path="/" element={<Root />}>
      <Route path="dashboard" element={<Dashboard />} />
      <Route path="about" element={<About />} />
    </Route>,
  ),
);

// Instead of this:
const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    children: [
      {
        path: "dashboard",
        element: <Dashboard />,
      },
      {
        path: "about",
        element: <About />,
      },
    ],
  },
]);
```

它也在 [`<Routes>`][routes] 内部使用，用于从其 [`<Route>`][route] 子元素生成路由对象。

## 类型声明

```tsx
declare function createRoutesFromElements(
  children: React.ReactNode,
): RouteObject[];

interface RouteObject {
  caseSensitive?: boolean;
  children?: RouteObject[];
  element?: React.ReactNode;
  index?: boolean;
  path?: string;
}
```

[routes]: ../components/routes
[route]: ../components/route
