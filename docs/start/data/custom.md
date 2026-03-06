---
title: 自定义框架
order: 8
---

# 自定义框架

[MODES: data]

## 简介

你可以不使用 `@react-router/dev`，而是通过数据模式将 React Router 的框架功能（如 loader、action、fetcher 等）集成到你自己的打包器和服务端抽象中。

## 客户端渲染

### 1. 创建路由

启用路由模块 API（loader、action 等）的浏览器端运行时 API 是 `createBrowserRouter`。

它接受一个路由对象数组，支持 loader、action、错误边界等。React Router Vite 插件会从 `routes.ts` 创建路由，但你也可以手动创建（或通过抽象层）并使用自己的打包器。

```tsx
import { createBrowserRouter } from "react-router";

let router = createBrowserRouter([
  {
    path: "/",
    Component: Root,
    children: [
      {
        path: "shows/:showId",
        Component: Show,
        loader: ({ request, params }) =>
          fetch(`/api/show/${params.showId}.json`, {
            signal: request.signal,
          }),
      },
    ],
  },
]);
```

### 2. 渲染路由

在浏览器中渲染路由，使用 `<RouterProvider>`。

```tsx
import {
  createBrowserRouter,
  RouterProvider,
} from "react-router";
import { createRoot } from "react-dom/client";

createRoot(document.getElementById("root")).render(
  <RouterProvider router={router} />,
);
```

### 3. 懒加载

路由的大部分定义可以通过 `lazy` 属性进行懒加载。

```tsx
createBrowserRouter([
  {
    path: "/show/:showId",
    lazy: {
      loader: async () =>
        (await import("./show.loader.js")).loader,
      action: async () =>
        (await import("./show.action.js")).action,
      Component: async () =>
        (await import("./show.component.js")).Component,
    },
  },
]);
```

## 服务端渲染

要对自定义配置进行服务端渲染，有一些服务端 API 可用于渲染和数据加载。

本指南只是简单介绍其工作原理。如需深入了解，请参阅[自定义框架示例仓库](https://github.com/remix-run/custom-react-router-framework-example)。

### 1. 定义路由

服务端的路由与客户端的路由对象格式相同。

```tsx
export default [
  {
    path: "/",
    Component: Root,
    children: [
      {
        path: "shows/:showId",
        Component: Show,
        loader: ({ params }) => {
          return db.loadShow(params.id);
        },
      },
    ],
  },
];
```

### 2. 创建静态处理器

使用 `createStaticHandler` 将路由转换为请求处理器：

```tsx
import { createStaticHandler } from "react-router";
import routes from "./some-routes";

let { query, dataRoutes } = createStaticHandler(routes);
```

### 3. 获取路由上下文并渲染

React Router 使用 Web Fetch [Request](https://developer.mozilla.org/en-US/docs/Web/API/Request) 对象，因此如果你的服务器不支持，需要将其使用的对象适配为 Web Fetch `Request` 对象。

以下步骤假设你的服务器接收 `Request` 对象。

```tsx
import { renderToString } from "react-dom/server";
import {
  createStaticHandler,
  createStaticRouter,
  StaticRouterProvider,
} from "react-router";

import routes from "./some-routes.js";

let { query, dataRoutes } = createStaticHandler(routes);

export async function handler(request: Request) {
  // 1. 使用 `query` 运行 action/loader 获取路由上下文
  let context = await query(request);

  // 如果 `query` 返回了 Response，则直接发送（可能是路由重定向）
  if (context instanceof Response) {
    return context;
  }

  // 2. 为 SSR 创建静态路由
  let router = createStaticRouter(dataRoutes, context);

  // 3. 使用 StaticRouterProvider 渲染所有内容
  let html = renderToString(
    <StaticRouterProvider
      router={router}
      context={context}
    />,
  );

  // 从最深层匹配的 action 和 loader 设置响应头
  let leaf = context.matches[context.matches.length - 1];
  let actionHeaders = context.actionHeaders[leaf.route.id];
  let loaderHeaders = context.loaderHeaders[leaf.route.id];
  let headers = new Headers(actionHeaders);
  if (loaderHeaders) {
    for (let [key, value] of loaderHeaders.entries()) {
      headers.append(key, value);
    }
  }

  headers.set("Content-Type", "text/html; charset=utf-8");

  // 4. 发送响应
  return new Response(`<!DOCTYPE html>${html}`, {
    status: context.statusCode,
    headers,
  });
}
```

### 4. 在浏览器中注水

注水数据嵌入在 `window.__staticRouterHydrationData` 中，使用它来初始化客户端路由并渲染 `<RouterProvider>`。

```tsx
import { StrictMode } from "react";
import { hydrateRoot } from "react-dom/client";
import { RouterProvider } from "react-router/dom";
import routes from "./app/routes.js";
import { createBrowserRouter } from "react-router";

let router = createBrowserRouter(routes, {
  hydrationData: window.__staticRouterHydrationData,
});

hydrateRoot(
  document,
  <StrictMode>
    <RouterProvider router={router} />
  </StrictMode>,
);
```
