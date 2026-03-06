---
title: createStaticHandler
---

# createStaticHandler

[MODES: data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.createStaticHandler.html)

创建静态处理器以执行服务端数据加载。

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
function createStaticHandler(
  routes: RouteObject[],
  opts?: CreateStaticHandlerOptions,
);
```

## 参数

### routes

要创建静态处理器的[路由对象](https://api.reactrouter.com/v7/types/react-router.RouteObject.html)。

### opts.basename

静态处理器的基础 URL（默认：`/`）。

### opts.future

静态处理器的 future flag。

## 返回值

可用于查询所提供路由数据的静态处理器。
