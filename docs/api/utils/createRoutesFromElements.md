---
title: createRoutesFromElements
---

# createRoutesFromElements

[MODES: data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.createRoutesFromElements.html)

从 JSX 元素而非对象数组创建路由对象。

```tsx
const routes = createRoutesFromElements(
  <>
    <Route index loader={step1Loader} Component={StepOne} />
    <Route
      path="step-2"
      loader={step2Loader}
      Component={StepTwo}
    />
    <Route
      path="step-3"
      loader={step3Loader}
      Component={StepThree}
    />
  </>,
);

const router = createBrowserRouter(routes);

function App() {
  return <RouterProvider router={router} />;
}
```

## 参数

### children

要转换为路由配置的 React 子元素

### parentPath

父路由的路径，用于生成唯一 ID。这用于内部递归，不打算由应用开发者使用。

## 返回值

可与 [`DataRouter`](https://api.reactrouter.com/v7/interfaces/react-router.DataRouter.html) 一起使用的 [`RouteObject`](https://api.reactrouter.com/v7/types/react-router.RouteObject.html) 数组
