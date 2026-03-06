---
title: createStaticRouter
---

# createStaticRouter

[MODES: data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.createStaticRouter.html)

创建用于服务端渲染的静态 [`DataRouter`](https://api.reactrouter.com/v7/interfaces/react-router.DataRouter.html)。

```tsx
export async function handleRequest(request: Request) {
  let { query, dataRoutes } = createStaticHandler(routes);
  let context = await query(request);

  if (context instanceof Response) {
    return context;
  }

  let router = createStaticRouter(dataRoutes, context);
  return new Response(
    ReactDOMServer.renderToString(<StaticRouterProvider ... />),
    { headers: { "Content-Type": "text/html" } }
  );
}
```

## 函数签名

```tsx
function createStaticRouter(
  routes: RouteObject[],
  context: StaticHandlerContext,
  opts: {
    future?: Partial<FutureConfig>;
  } = ,
): DataRouter {}
```

## 参数

### routes

要创建静态 [`DataRouter`](https://api.reactrouter.com/v7/interfaces/react-router.DataRouter.html) 的路由对象。

### context

[`StaticHandler`](https://api.reactrouter.com/v7/interfaces/react-router.StaticHandler.html) 的 `query` 返回的 [`StaticHandlerContext`](https://api.reactrouter.com/v7/interfaces/react-router.StaticHandlerContext.html)。

### opts.future

静态 [`DataRouter`](https://api.reactrouter.com/v7/interfaces/react-router.DataRouter.html) 的 future flag。

## 返回值

可用于渲染所提供路由的静态 [`DataRouter`](https://api.reactrouter.com/v7/interfaces/react-router.DataRouter.html)。
