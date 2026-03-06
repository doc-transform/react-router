---
title: Route
---

# Route

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.Route.html)

配置当 URL 模式匹配当前位置时要渲染的元素。必须在 [`Routes`](../components/Routes) 元素内渲染。注意：这些路由不参与数据加载、action、代码拆分或任何其他路由模块功能。

```tsx
// 通常在声明式路由中使用
function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route index element={<StepOne />} />
        <Route path="step-2" element={<StepTwo />} />
        <Route path="step-3" element={<StepThree />} />
      </Routes>
    </BrowserRouter>
  );
}

// 也可以与数据路由一起使用（如果你偏好 JSX 写法）
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

## 函数签名

```tsx
function Route(
  props: RouteProps,
): React.ReactElement | null;
```

## Props

### action

路由 action。参见 [`action`](../../start/data/route-object#action)。

### caseSensitive

路径是否区分大小写。默认为 `false`。

### Component

此路由匹配时要渲染的 React 组件。与 `element` 互斥。

### children

子 Route 组件。

### element

此路由匹配时要渲染的 React 元素。与 `Component` 互斥。

### ErrorBoundary

当此路由发生错误时要渲染的 React 组件。与 `errorElement` 互斥。

### errorElement

当此路由发生错误时要渲染的 React 元素。与 `ErrorBoundary` 互斥。

### handle

路由 handle。

### HydrateFallback

路由加载数据时要渲染的 React 组件。与 `hydrateFallbackElement` 互斥。

### hydrateFallbackElement

路由加载数据时要渲染的 React 元素。与 `HydrateFallback` 互斥。

### id

此路由的唯一标识符（用于 [`DataRouter`](https://api.reactrouter.com/v7/interfaces/react-router.DataRouter.html)）。

### index

是否为索引路由。

### lazy

返回一个解析为路由对象的 Promise 的函数。用于路由的代码拆分。参见 [`lazy`](../../start/data/route-object#lazy)。

### loader

路由 loader。参见 [`loader`](../../start/data/route-object#loader)。

### path

要匹配的路径模式。如果未指定或为空，则成为布局路由。

### shouldRevalidate

路由的 shouldRevalidate 函数。参见 [`shouldRevalidate`](../../start/data/route-object#shouldRevalidate)。
