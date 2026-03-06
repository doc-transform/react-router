---
title: useRouteError
---

# useRouteError

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useRouteError.html)

访问在 [`action`](../../start/framework/route-module#action)、[`loader`](../../start/framework/route-module#loader) 或组件渲染期间抛出的错误，用于路由模块的 [`ErrorBoundary`](../../start/framework/route-module#errorboundary) 中。

```tsx
export function ErrorBoundary() {
  const error = useRouteError();
  return <div>{error.message}</div>;
}
```

## 函数签名

```tsx
function useRouteError(): unknown;
```

## 返回值

在路由[加载](../../start/framework/route-module#loader)、[`action`](../../start/framework/route-module#action) 执行或渲染期间抛出的错误。
