---
title: isRouteErrorResponse
new: true
---

# `isRouteErrorResponse`

如果[路由错误][routeerror]是一个*路由错误响应*，则返回 `true`。

```jsx
import { isRouteErrorResponse } from "react-router-dom";

function ErrorBoundary() {
  const error = useRouteError();
  if (isRouteErrorResponse(error)) {
    return (
      <div>
        <h1>Oops!</h1>
        <h2>{error.status}</h2>
        <p>{error.statusText}</p>
        {error.data?.message && <p>{error.data.message}</p>}
      </div>
    );
  } else {
    return <div>Oops</div>;
  }
}
```

当从 action 或 loader 中抛出一个 response 时，它会被解包为 `ErrorResponse`，这样你的组件就不必处理解包的复杂性（否则需要 React state 和 effects 来处理 `res.json()` 返回的 promise）

```jsx
import { json } from "react-router-dom";

<Route
  errorElement={<ErrorBoundary />}
  action={() => {
    throw json(
      { message: "email is required" },
      { status: 400 },
    );
  }}
/>;

function ErrorBoundary() {
  const error = useRouteError();
  if (isRouteErrorResponse(error)) {
    error.status; // 400
    error.data; // { "message: "email is required" }
  }
}
```

<docs-info>如果用户访问了与应用中任何路由都不匹配的路径，React Router 框架本身会抛出一个 404 响应。</docs-info>

[routeerror]: ../hooks/use-route-error
