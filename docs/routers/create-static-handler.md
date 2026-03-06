---
title: createStaticHandler
new: true
---

# `createStaticHandler`

`createStaticHandler` 用于在服务器上（即 [Node][node] 或其他 JavaScript 运行时）在通过 `<StaticRouterProvider>` 进行服务端渲染应用之前执行数据获取和提交。更完整的概述请参阅[服务端渲染][ssr]指南。

```jsx lines=[2,21-23]
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
declare function createStaticHandler(
  routes: AgnosticRouteObject[],
  opts?: CreateStaticHandlerOptions,
): StaticHandler;

interface CreateStaticHandlerOptions {
  basename?: string;
  future?: Partial<StaticHandlerFutureConfig>;
  mapRouteProperties?: MapRoutePropertiesFunction;
}

interface StaticHandlerFutureConfig {
  v7_relativeSplatPath: boolean;
  v7_throwAbortReason: boolean;
}

interface MapRoutePropertiesFunction {
  (route: AgnosticRouteObject): {
    hasErrorBoundary: boolean;
  } & Record<string, any>;
}

interface StaticHandler {
  dataRoutes: AgnosticDataRouteObject[];
  query(
    request: Request,
    opts?: {
      requestContext?: unknown;
    },
  ): Promise<StaticHandlerContext | Response>;
  queryRoute(
    request: Request,
    opts?: {
      routeId?: string;
      requestContext?: unknown;
    },
  ): Promise<any>;
}
```

## `routes`/`basename`

这些与你传递给 [`createBrowserRouter`][createbrowserrouter] 的 `routes`/`basename` 相同。

## `handler.query(request, opts)`

`handler.query()` 方法接收一个 Fetch 请求，执行路由匹配，并根据请求执行所有相关的路由 action/loader 方法。返回的 `context` 值包含渲染该请求的 HTML 文档所需的所有信息（路由级别的 `actionData`、`loaderData`、`errors` 等）。如果任何匹配的路由返回或抛出重定向响应，则 `query()` 将以 Fetch `Response` 的形式返回该重定向。

如果请求被中止，`query` 将抛出一个错误，如 `Error("query() call aborted: GET /path")`。如果你想抛出原生的 `AbortSignal.reason`（默认是 `DOMException`），你可以启用 `future.v7_throwAbortReason` future flag。`DOMException` 在 Node 17 中添加，因此你必须使用 Node 17 或更高版本才能正常工作。

### `opts.requestContext`

如果你需要从服务器向 Remix 的 action/loader 传递信息，可以通过 `opts.requestContext` 来实现，它会在 action/loader 的 context 参数中可用。

```ts
const routes = [{
  path: '/',
  loader({ request, context }) {
    // Access `context.dataFormExpressMiddleware` here
  },
}];

export async function render(req: express.Request) {
  let { query, dataRoutes } = createStaticHandler(routes);
  let remixRequest = createFetchRequest(request);
  let staticHandlerContext = await query(remixRequest, {
    // Pass data from the express layer to the remix layer here
    requestContext: {
      dataFromExpressMiddleware: req.something
    }
 });
 ...
}
```

## `handler.queryRoute(request, opts)`

`handler.queryRoute` 是一个更有针对性的版本，它查询单个路由并根据请求运行其 loader 或 action。默认情况下，它会根据请求 URL 匹配目标路由。返回值是 loader 或 action 返回的值，通常是一个 `Response` 对象。

如果请求被中止，`query` 将抛出一个错误，如 `Error("queryRoute() call aborted: GET /path")`。如果你想抛出原生的 `AbortSignal.reason`（默认是 `DOMException`），你可以启用 `future.v7_throwAbortReason` future flag。`DOMException` 在 Node 17 中添加，因此你必须使用 Node 17 或更高版本才能正常工作。

### `opts.routeId`

如果你需要调用一个与 URL 不完全对应的特定路由 action/loader（例如，一个父路由 loader），你可以指定 `routeId`：

```js
staticHandler.queryRoute(new Request("/parent/child"), {
  routeId: "parent",
});
```

### `opts.requestContext`

如果你需要从服务器向 Remix 的 action/loader 传递信息，可以通过 `opts.requestContext` 来实现，它会在 action/loader 的 context 参数中可用。参见上面 `query()` 部分的示例。

**另请参阅：**

- [`createStaticRouter`][createstaticrouter]
- [`<StaticRouterProvider>`][staticrouterprovider]

[node]: https://nodejs.org/
[ssr]: ../guides/ssr
[createbrowserrouter]: ./create-browser-router
[createstaticrouter]: ../routers/create-static-router
[staticrouterprovider]: ../routers/static-router-provider
