---
title: useRouteError
new: true
---

# `useRouteError`

在 [`errorElement`][errorelement] 内部，此 hook 返回在 action、loader 或渲染过程中抛出的任何内容。注意，抛出的响应有特殊处理，更多信息请参阅 [`isRouteErrorResponse`][isrouteerrorresponse]。

<docs-warning>此功能仅在使用数据路由器时有效，参见[选择路由器][pickingarouter]</docs-warning>

```jsx
function ErrorBoundary() {
  const error = useRouteError();
  console.error(error);
  return <div>{error.message}</div>;
}

<Route
  errorElement={<ErrorBoundary />}
  loader={() => {
    // unexpected errors in loaders/actions
    something.that.breaks();
  }}
  action={() => {
    // stuff you throw on purpose in loaders/actions
    throw new Response("Bad Request", { status: 400 });
  }}
  element={
    // and errors thrown while rendering
    <div>{breaks.while.rendering}</div>
  }
/>;
```

[errorelement]: ../route/error-element
[isrouteerrorresponse]: ../utils/is-route-error-response
[pickingarouter]: ../routers/picking-a-router
