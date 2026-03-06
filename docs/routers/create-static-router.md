---
title: createStaticRouter
new: true
---

# `createStaticRouter`

`createStaticRouter` 用于当你想利用[数据路由器][picking-a-router]在服务器上（即 [Node][node] 或其他 JavaScript 运行时）进行渲染时使用。更完整的概述请参阅[服务端渲染][ssr]指南。

```jsx lines=[3,31]
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
declare function createStaticRouter(
  routes: RouteObject[],
  context: StaticHandlerContext,
  opts: {
    future?: {
      v7_partialHydration?: boolean;
    };
  },
): Router;
```

## `opts.future`

一组可选的 [Future Flags][api-development-strategy]，用于为此 Static Router 启用。我们建议尽早启用新发布的 future flag，以便将来更顺利地迁移到 v7。

```js
const router = createBrowserRouter(routes, {
  future: {
    // Opt-into partial hydration
    v7_partialHydration: true,
  },
});
```

当前可用的 future flag：

| Flag                                      | 描述                         |
| ----------------------------------------- | ---------------------------- |
| [`v7_partialHydration`][partialhydration] | 支持服务端渲染应用的部分注水 |

**另请参阅：**

- [`createStaticHandler`][createstatichandler]
- [`<StaticRouterProvider>`][staticrouterprovider]

[picking-a-router]: ./picking-a-router
[node]: https://nodejs.org/
[ssr]: ../guides/ssr
[createstatichandler]: ../routers/create-static-handler
[staticrouterprovider]: ../routers/static-router-provider
[partialhydration]: ../routers/create-browser-router#partial-hydration-data
[api-development-strategy]: ../guides/api-development-strategy
