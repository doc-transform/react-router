---
title: 错误上报
---

# 错误上报

[MODES: framework,data]

<br/>
<br/>

React Router 会捕获路由模块中的错误并将其发送到[错误边界](./error-boundary)，以防止错误发生时出现空白页面。然而，`ErrorBoundary` 不足以用于记录和上报错误。

## 服务端错误

[modes: framework]

要在服务端访问这些被捕获的错误，请使用服务端入口模块的 `handleError` 导出。

### 1. 显示服务端入口

如果你在 app 目录中看不到 [`entry.server.tsx`][entryserver]，说明你使用的是默认入口。使用以下 CLI 命令来显示它：

```shellscript nonumber
react-router reveal entry.server
```

### 2. 导出错误处理器

每当 React Router 在服务端捕获到应用中的错误时，都会调用此函数。

```tsx filename=entry.server.tsx
import { type HandleErrorFunction } from "react-router";

export const handleError: HandleErrorFunction = (
  error,
  { request },
) => {
  // React Router 可能会中止一些被打断的请求，不要记录那些
  if (!request.signal.aborted) {
    myReportError(error);

    // 确保仍然在控制台记录错误，以便你能看到
    console.error(error);
  }
};
```

另请参阅：

- [`handleError`][handleError]

## 客户端错误

要在客户端访问这些被捕获的错误，请使用 [`HydratedRouter`][hydratedrouter] 或 [`RouterProvider`][routerprovider] 组件的 `onError` prop。

### 框架模式

[modes: framework]

#### 1. 显示客户端入口

如果你在 app 目录中看不到 [`entry.client.tsx`][entryclient]，说明你使用的是默认入口。使用以下 CLI 命令来显示它：

```shellscript nonumber
react-router reveal entry.client
```

#### 2. 添加错误处理器

每当 React Router 在客户端捕获到应用中的错误时，都会调用此函数。

```tsx filename=entry.client.tsx
import { type ClientOnErrorFunction } from "react-router";

const onError: ClientOnErrorFunction = (
  error,
  { location, params, unstable_pattern, errorInfo },
) => {
  myReportError(error, location, errorInfo);

  // 确保仍然在控制台记录错误，以便你能看到
  console.error(error, errorInfo);
};

startTransition(() => {
  hydrateRoot(
    document,
    <StrictMode>
      <HydratedRouter onError={onError} />
    </StrictMode>,
  );
});
```

另请参阅：

- [`<HydratedRouter onError>`][hydratedrouter-onerror]

### 数据模式

[modes: data]

每当 React Router 在客户端捕获到应用中的错误时，都会调用此函数。

```tsx
import { type ClientOnErrorFunction } from "react-router";

const onError: ClientOnErrorFunction = (
  error,
  { location, params, unstable_pattern, errorInfo },
) => {
  myReportError(error, location, errorInfo);

  // 确保仍然在控制台记录错误，以便你能看到
  console.error(error, errorInfo);
};

function App() {
  return <RouterProvider onError={onError} />;
}
```

另请参阅：

- [`<RouterProvider onError>`][routerprovider-onerror]

[entryserver]: ../api/framework-conventions/entry.server.tsx
[handleError]: ../api/framework-conventions/entry.server.tsx#handleerror
[entryclient]: ../api/framework-conventions/entry.client.tsx
[hydratedrouter]: ../api/framework-routers/HydratedRouter
[routerprovider]: ../api/data-routers/RouterProvider
[hydratedrouter-onerror]: ../api/framework-routers/HydratedRouter#onError
[routerprovider-onerror]: ../api/data-routers/RouterProvider#onError
