---
title: isRouteErrorResponse
---

# isRouteErrorResponse

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.isRouteErrorResponse.html)

检查给定的错误是否是从 [`action`](../../start/framework/route-module#action) 或 [`loader`](../../start/framework/route-module#loader) 函数中抛出的 4xx/5xx [`Response`](https://developer.mozilla.org/en-US/docs/Web/API/Response) 生成的 [`ErrorResponse`](https://api.reactrouter.com/v7/types/react-router.ErrorResponse.html)。

```tsx
import { isRouteErrorResponse } from "react-router";

export function ErrorBoundary({
  error,
}: Route.ErrorBoundaryProps) {
  if (isRouteErrorResponse(error)) {
    return (
      <>
        <p>
          Error: `${error.status}: ${error.statusText}`
        </p>
        <p>{error.data}</p>
      </>
    );
  }

  return (
    <p>
      Error:{" "}
      {error instanceof Error
        ? error.message
        : "Unknown Error"}
    </p>
  );
}
```

## 函数签名

```tsx
function isRouteErrorResponse(
  error: any,
): error is ErrorResponse;
```

## 参数

### error

要检查的错误。

## 返回值

如果错误是 [`ErrorResponse`](https://api.reactrouter.com/v7/types/react-router.ErrorResponse.html) 则为 `true`，否则为 `false`。
