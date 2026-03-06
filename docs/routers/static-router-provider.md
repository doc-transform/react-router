---
title: StaticRouterProvider
new: true
---

# `<StaticRouterProvider>`

`<StaticRouterProvider>` 接受来自 [`createStaticRouter()`][createstaticrouter] 的 `router` 和来自 [`createStaticHandler()`][createstatichandler] 的 `context`，在服务器上（即 [Node][node] 或其他 JavaScript 运行时）渲染你的应用。更完整的概述请参阅[服务端渲染][ssr]指南。

```jsx lines=[4,34-37]
import {
  createStaticHandler,
  createStaticRouter,
  StaticRouterProvider,
} from "react-router-dom/server";
import Root, {
  loader as rootLoader,
  ErrorBoundary as RootBoundary,
} from "./root";

const routes = [
  {
    path: "/",
    loader: rootLoader,
    Component: Root,
    ErrorBoundary: RootBoundary,
  },
];

export async function renderHtml(req) {
  let { query, dataRoutes } = createStaticHandler(routes);
  let fetchRequest = createFetchRequest(req);
  let context = await query(fetchRequest);

  // If we got a redirect response, short circuit and let our Express server
  // handle that directly
  if (context instanceof Response) {
    throw context;
  }

  let router = createStaticRouter(dataRoutes, context);
  return ReactDOMServer.renderToString(
    <React.StrictMode>
      <StaticRouterProvider
        router={router}
        context={context}
      />
    </React.StrictMode>,
  );
}
```

## 类型声明

```ts
declare function StaticRouterProvider(props: {
  context: StaticHandlerContext;
  router: Router;
  hydrate?: boolean;
  nonce?: string;
}: JSX.Element;
```

## `context`

这是从 `createStaticHandler().query()` 调用返回的 `context`，包含该请求所获取的所有数据。

## `router`

这是通过 `createStaticRouter` 创建的路由器。

## `hydrate`

默认情况下，`<StaticRouterProvider>` 会将所需的注水数据以 `<script>` 标签的形式序列化到 `window.__staticRouterHydrationData` 上，`createBrowserRouter()` 将读取并自动注水这些数据。

如果你希望手动进行更高级的注水，可以传递 `hydrate={false}` 来禁用此自动注水。在客户端，你需要将自己的 `hydrationData` 传递给 `createBrowserRouter`。

## `nonce`

在使用自动注水时，你可以提供一个 `nonce` 值，该值将被渲染到 `<script>` 标签上，并与你的[内容安全策略][nonce]一起使用。

**另请参阅：**

- [`createStaticHandler`][createstatichandler]
- [`createStaticRouter`][createstaticrouter]
- [`createBrowserRouter`][createbrowserrouter]

[node]: https://nodejs.org/
[ssr]: ../guides/ssr
[createstaticrouter]: ./create-static-router
[createstatichandler]: ./create-static-handler
[createbrowserrouter]: ./create-browser-router
[nonce]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/script-src#unsafe_inline_script
