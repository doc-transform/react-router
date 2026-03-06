---
title: 服务端渲染
toc: false
order: 6
---

# 服务端渲染

React Router 中最基本的服务器渲染相当简单。然而，除了渲染正确的路由之外，还有很多需要考虑的事情。以下是你需要处理的一个不完整列表：

- 为服务器和浏览器打包你的代码
- 不将仅服务器端的代码打包到浏览器 bundle 中
- 在服务器和浏览器中都能工作的代码分割
- 服务端数据加载，这样你实际上才有内容可渲染
- 在客户端和服务器上都能工作的数据加载策略
- 在服务器和客户端处理代码分割
- 正确的 HTTP 状态码和重定向
- 环境变量和密钥
- 部署

要将这一切设置好可能相当复杂，但服务端渲染带来的性能和用户体验特性是值得的。

如果你想对 React Router 应用进行服务端渲染，我们强烈建议你使用 [Remix][remix]。这是我们的另一个项目，构建在 React Router 之上，处理了上述所有事项以及更多。试试看吧！

如果你想自己处理，你需要在服务器上使用 `<StaticRouterProvider>` 或 `<StaticRouter>`，取决于你选择的[路由器][picking-a-router]。如果使用 `<StaticRouter>`，请跳到[不使用数据路由器][ssr-non-data]部分。

## 使用数据路由器

首先，你需要为数据路由器定义路由，这些路由将在服务器和客户端都使用：

```js filename=routes.jsx
const React = require("react");
const { json, useLoaderData } = require("react-router-dom");

const routes = [
  {
    path: "/",
    loader() {
      return json({ message: "Welcome to React Router!" });
    },
    Component() {
      let data = useLoaderData();
      return <h1>{data.message}</h1>;
    },
  },
];

module.exports = routes;
```

<docs-info>我们在这些示例中使用 CJS 模块是为了服务器端的简单性，但通常你会使用 ESM 模块并借助 `esbuild`、`vite` 或 `webpack` 等打包工具。</docs-info>

定义好路由后，我们可以在 express 服务器中创建一个处理程序，并使用 `createStaticHandler()` 为路由加载数据。记住，数据路由器的主要目标是将数据获取与渲染解耦，所以你会看到使用数据路由器进行服务端渲染时，数据获取和渲染是明确分开的步骤。

```js filename=server.jsx lines=[2-4,11,14-15]
const express = require("express");
const {
  createStaticHandler,
} = require("react-router-dom/server");

const createFetchRequest = require("./request");
const routes = require("./routes");

const app = express();

let handler = createStaticHandler(routes);

app.get("*", async (req, res) => {
  let fetchRequest = createFetchRequest(req, res);
  let context = await handler.query(fetchRequest);

  // We'll tackle rendering next...
});

const listener = app.listen(3000, () => {
  let { port } = listener.address();
  console.log(`Listening on port ${port}`);
});
```

注意我们需要先将传入的 Express 请求转换为 Fetch 请求，这是静态处理程序方法操作的对象。`createFetchRequest` 方法是针对 Express 请求的，在本示例中从 `@remix-run/express` 适配器中提取：

```js filename=request.js
module.exports = function createFetchRequest(req, res) {
  let origin = `${req.protocol}://${req.get("host")}`;
  // Note: This had to take originalUrl into account for presumably vite's proxying
  let url = new URL(req.originalUrl || req.url, origin);

  let controller = new AbortController();
  res.on("close", () => controller.abort());

  let headers = new Headers();

  for (let [key, values] of Object.entries(req.headers)) {
    if (values) {
      if (Array.isArray(values)) {
        for (let value of values) {
          headers.append(key, value);
        }
      } else {
        headers.set(key, values);
      }
    }
  }

  let init = {
    method: req.method,
    headers,
    signal: controller.signal,
  };

  if (req.method !== "GET" && req.method !== "HEAD") {
    init.body = req.body;
  }

  return new Request(url.href, init);
};
```

一旦我们通过执行传入请求的所有匹配路由 loader 加载了数据，我们就使用 `createStaticRouter()` 和 `<StaticRouterProvider>` 渲染 HTML 并将响应发送回浏览器：

```js filename=server.jsx lines=[5-16]
app.get("*", async (req, res) => {
  let fetchRequest = createFetchRequest(req, res);
  let context = await handler.query(fetchRequest);

  let router = createStaticRouter(
    handler.dataRoutes,
    context,
  );
  let html = ReactDOMServer.renderToString(
    <StaticRouterProvider
      router={router}
      context={context}
    />,
  );

  res.send("<!DOCTYPE html>" + html);
});
```

<docs-info>我们这里使用 [`renderToString`][rendertostring] 是为了简单起见，因为我们已经在 `handler.query` 中加载了数据，而且在这个简单示例中没有使用任何流式功能。如果你需要支持流式功能，你需要使用 [`renderToPipeableStream`][rendertopipeablestream]。<br/><br/>如果你希望支持 [`defer`][defer]，你还需要管理服务端 Promise 通过网络序列化到客户端（提示，直接使用 [Remix][remix] 就好，通过 `Scripts` 组件已经帮你处理好了 😉）。</docs-info>

将 HTML 发送回浏览器后，我们需要在客户端使用 `createBrowserRouter()` 和 `<RouterProvider>` “注水”应用：

```jsx filename=entry-client.jsx lines=[10-15]
import * as React from "react";
import * as ReactDOM from "react-dom/client";
import {
  createBrowserRouter,
  RouterProvider,
} from "react-router-dom";

import { routes } from "./routes";

let router = createBrowserRouter(routes);

ReactDOM.hydrateRoot(
  document.getElementById("app"),
  <RouterProvider router={router} />,
);
```

这样你就有了一个服务端渲染并注水的应用！如需工作示例，你也可以参考 Github 仓库中的[示例][ssr-data-router-example]。

### 补充概念

如上所述，服务端渲染在大规模和生产级应用中是复杂的，我们强烈建议查看 [Remix][remix]。但如果你要手动处理，以下是一些你可能需要考虑的额外概念：

#### 注水

服务端渲染的核心概念是[注水][hydration]，它涉及将客户端 React 应用“附加”到服务端渲染的 HTML 上。为了正确执行此操作，我们需要以与服务端渲染时相同的状态创建客户端 React Router 应用。当你的服务端渲染通过 `loader` 函数加载了数据时，我们需要将这些数据发送过去，以便我们可以使用相同的 loader 数据为初始渲染/注水创建客户端路由器。

本指南中展示的 `<StaticRouterProvider>` 和 `createBrowserRouter` 的基本用法会在内部为你处理这些，但如果你需要控制注水过程，你可以通过 [`<StaticRouterProvider hydrate={false} />`][hydrate-false] 禁用自动注水过程。

在一些高级用例中，你可能希望部分注水客户端 React Router 应用。你可以通过传递给 `createBrowserRouter` 的 [`future.v7_partialHydration`][partialhydration] flag 来实现。

#### 重定向

如果任何 loader 进行重定向，`handler.query` 会直接返回 `Response`，因此你应该检查并发送重定向响应，而不是尝试渲染 HTML 文档：

```js filename=server.jsx lines=[5-10]
app.get("*", async (req, res) => {
  let fetchRequest = createFetchRequest(req, res);
  let context = await handler.query(fetchRequest);

  if (
    context instanceof Response &&
    [301, 302, 303, 307, 308].includes(context.status)
  ) {
    return res.redirect(
      context.status,
      context.headers.get("Location"),
    );
  }

  // Render HTML...
});
```

#### 延迟加载路由

如果你在路由中使用 [`route.lazy`][lazy]，那么在客户端你可能拥有注水所需的所有数据，但还没有路由定义！理想情况下，你的设置应该在服务器上确定匹配的路由并在关键路径上传递它们的路由 bundle，这样你就不会在初始匹配的路由上使用 `lazy`。然而，如果不是这种情况，你需要在注水之前加载这些路由并更新它们，以避免路由器回退到加载状态：

```jsx filename=entry-client.jsx
// Determine if any of the initial routes are lazy
let lazyMatches = matchRoutes(
  routes,
  window.location,
)?.filter((m) => m.route.lazy);

// Load the lazy matches and update the routes before creating your router
// so we can hydrate the SSR-rendered content synchronously
if (lazyMatches && lazyMatches?.length > 0) {
  await Promise.all(
    lazyMatches.map(async (m) => {
      let routeModule = await m.route.lazy();
      Object.assign(m.route, {
        ...routeModule,
        lazy: undefined,
      });
    }),
  );
}

let router = createBrowserRouter(routes);

ReactDOM.hydrateRoot(
  document.getElementById("app"),
  <RouterProvider router={router} fallbackElement={null} />,
);
```

另见：

- [`createStaticHandler`][createstatichandler]
- [`createStaticRouter`][createstaticrouter]
- [`<StaticRouterProvider>`][staticrouterprovider]

## 不使用数据路由器

首先你需要某种“应用”或“根”组件，它在服务器和浏览器中都会被渲染：

```js filename=App.js
export default function App() {
  return (
    <html>
      <head>
        <title>Server Rendered App</title>
      </head>
      <body>
        <Routes>
          <Route path="/" element={<div>Home</div>} />
          <Route path="/about" element={<div>About</div>} />
        </Routes>
        <script src="/build/client.entry.js" />
      </body>
    </html>
  );
}
```

以下是一个简单的 express 服务器，在服务器上渲染应用。注意使用了 `StaticRouter`。

```js filename=server.entry.js
import express from "express";
import ReactDOMServer from "react-dom/server";
import { StaticRouter } from "react-router-dom/server";
import App from "./App";

let app = express();

app.get("*", (req, res) => {
  let html = ReactDOMServer.renderToString(
    <StaticRouter location={req.url}>
      <App />
    </StaticRouter>,
  );
  res.send("<!DOCTYPE html>" + html);
});

app.listen(3000);
```

<docs-info>我们这里使用 [`renderToString`][rendertostring] 是为了简单起见，因为在这个简单示例中没有使用任何流式功能。如果你需要支持流式功能，你需要使用 [`renderToPipeableStream`][rendertopipeablestream]。</docs-info>

最后，你需要一个类似的文件来使用你的 JavaScript bundle “注水”应用，该 bundle 包含相同的 `App` 组件。注意使用了 `BrowserRouter` 而不是 `StaticRouter`。

```js filename=client.entry.js
import * as ReactDOM from "react-dom";
import { BrowserRouter } from "react-router-dom";
import App from "./App";

ReactDOM.hydrate(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  document.documentElement,
);
```

与客户端入口的唯一真正区别是：

- 使用 `StaticRouter` 而不是 `BrowserRouter`
- 从服务器传递 URL 到 `<StaticRouter url>`
- 使用 `ReactDOMServer.renderToString` 而不是 `ReactDOM.render`

你需要自己处理以下内容才能使其工作：

- 如何打包代码以在浏览器和服务器中工作
- 如何知道客户端入口在哪里，用于 `<App>` 组件中的 `<script>`
- 弄清楚数据加载（尤其是对于 `<title>`）

再次说明，我们建议你看看 [Remix](https://remix.run)。它是服务端渲染 React Router 应用的最佳方式——也许也是构建任何 React 应用的最佳方式 😉

[remix]: https://remix.run
[picking-a-router]: ../routers/picking-a-router
[ssr-non-data]: #without-a-data-router
[ssr-data-router-example]: https://github.com/remix-run/react-router/tree/main/examples/ssr-data-router
[createstatichandler]: ../routers/create-static-handler
[createstaticrouter]: ../routers/create-static-router
[staticrouterprovider]: ../routers/static-router-provider
[lazy]: ../route/lazy
[hydration]: https://react.dev/reference/react-dom/client/hydrateRoot
[hydrate-false]: ../routers/static-router-provider#hydrate
[partialhydration]: ../routers/create-browser-router#partial-hydration-data
[rendertostring]: https://react.dev/reference/react-dom/server/renderToString
[rendertopipeablestream]: https://react.dev/reference/react-dom/server/renderToPipeableStream
[defer]: ../utils/defer
