---
title: StaticRouterProvider
---

# StaticRouterProvider

[MODES: data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.StaticRouterProvider.html)

一个不会导航到其他 [`Location`](https://api.reactrouter.com/v7/interfaces/react-router.Location.html) 的 [`DataRouter`](https://api.reactrouter.com/v7/interfaces/react-router.DataRouter.html)。适用于没有有状态 UI 的服务端。

```tsx
export async function handleRequest(request: Request) {
  let { query, dataRoutes } = createStaticHandler(routes);
  let context = await query(request));

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
function StaticRouterProvider({
  context,
  router,
  hydrate = true,
  nonce,
}: StaticRouterProviderProps);
```

## Props

### context

[`StaticHandler`](https://api.reactrouter.com/v7/interfaces/react-router.StaticHandler.html) 的 `query` 返回的 [`StaticHandlerContext`](https://api.reactrouter.com/v7/interfaces/react-router.StaticHandlerContext.html)。

### hydrate

是否在客户端注水路由器（默认 `true`）。

### nonce

用于注水 [`<script>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script) 标签的 [`nonce`](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Global_attributes/nonce)。

### router

来自 [`createStaticRouter`](../data-routers/createStaticRouter) 的静态 [`DataRouter`](https://api.reactrouter.com/v7/interfaces/react-router.DataRouter.html)。
