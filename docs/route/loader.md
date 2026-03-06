---
title: loader
new: true
---

# `loader`

每个路由都可以定义一个 "loader" 函数，在路由元素渲染之前为其提供数据。

<docs-warning>此功能仅在使用数据路由器时有效，参见[选择路由器][pickingarouter]</docs-warning>

```js [5-7,12-14]
createBrowserRouter([
  {
    element: <Teams />,
    path: "teams",
    loader: async () => {
      return fakeDb.from("teams").select("*");
    },
    children: [
      {
        element: <Team />,
        path: ":teamId",
        loader: async ({ params }) => {
          return fetch(`/api/teams/${params.teamId}.json`);
        },
      },
    ],
  },
]);
```

当用户在应用中导航时，下一个匹配路由分支的 loader 会被并行调用，其数据通过 [`useLoaderData`][useloaderdata] 提供给组件。

## `params`

路由参数从[动态片段][dynamicsegments]解析后传递给 loader。这对于确定要加载哪个资源很有用：

```tsx
createBrowserRouter([
  {
    path: "/teams/:teamId",
    loader: ({ params }) => {
      return fakeGetTeam(params.teamId);
    },
  },
]);
```

注意，路径中的 `:teamId` 会被解析并以相同的名称作为 `params.teamId` 提供。

## `request`

这是发送到应用的 [Fetch Request][request] 实例。

```jsx
function loader({ request }) {}
```

> request?!

loader 接收一个 "request" 可能初看起来有些奇怪。思考一下 `<Link>` 做了类似以下代码的事情，问问自己，"这里阻止了什么默认行为？"

```tsx [4]
<a
  href={props.to}
  onClick={(event) => {
    event.preventDefault();
    navigate(props.to);
  }}
/>
```

没有 React Router 时，浏览器本会向服务器发送一个 <i>Request</i>，但 React Router 阻止了它！React Router 不是让浏览器将请求发送到服务器，而是将请求发送到你的 loader。

最常见的用例是创建一个 [URL][url] 并从中读取 [URLSearchParams][urlsearchparams]：

```jsx
function loader({ request }) {
  const url = new URL(request.url);
  const searchTerm = url.searchParams.get("q");
  return searchProducts(searchTerm);
}
```

注意这里的 API 不是 React Router 特有的，而是标准的 Web 对象：[Request][request]、[URL][url]、[URLSearchParams][urlsearchparams]。

## `loader.hydrate`

如果你使用[服务端渲染][ssr]并利用了 `future.v7_partialHydration` 标志进行[部分注水][partialhydration]，那么你可能希望选择在初始注水时运行路由 `loader`，_即使它有注水数据_（例如，让用户用注水数据填充缓存）。要在部分注水场景中强制 `loader` 在注水时运行，你可以在 `loader` 函数上设置一个 `hydrate` 属性：

## 返回 Response

虽然你可以从 loader 返回任何内容并通过 [`useLoaderData`][useloaderdata] 访问它，但你也可以返回一个 Web [Response][response]。

这可能看起来不是立即有用的，但考虑一下 `fetch`。由于 `fetch` 的返回值是一个 Response，而 loader 理解 Response，很多 loader 可以简单地返回一个 fetch！

```tsx
// HTTP/REST API
function loader({ request }) {
  return fetch("/api/teams.json", {
    signal: request.signal,
  });
}

// 甚至是 GraphQL 端点
function loader({ request, params }) {
  return fetch("/_gql", {
    signal: request.signal,
    method: "post",
    body: JSON.stringify({
      query: gql`...`,
      params: params,
    }),
  });
}
```

你也可以自己构造 Response：

```tsx [3-8]
function loader({ request, params }) {
  const data = { some: "thing" };
  return new Response(JSON.stringify(data), {
    status: 200,
    headers: {
      "Content-Type": "application/json; utf-8",
    },
  });
}
```

React Router 会自动调用 `response.json()`，因此你的组件在渲染时不需要自己解析：

```tsx
function SomeRoute() {
  const data = useLoaderData();
  // { some: "thing" }
}
```

使用 [`json`][json] 工具函数可以简化这个过程，不必自己构造。下面的示例与前面的示例效果相同：

```jsx
import { json } from "react-router-dom";

function loader({ request, params }) {
  const data = { some: "thing" };
  return json(data, { status: 200 });
}
```

如果你计划升级到 Remix，从每个 loader 返回 Response 将使迁移更加顺畅。

## 在 Loader 中抛出

你可以在 loader 中 `throw` 来跳出当前调用栈（停止运行当前代码），React Router 将重新走"错误路径"。

```tsx [4]
function loader({ request, params }) {
  const res = await fetch(`/api/properties/${params.id}`);
  if (res.status === 404) {
    throw new Response("Not Found", { status: 404 });
  }
  return res.json();
}
```

更多细节请阅读 [`errorElement`][errorelement] 文档。

[dynamicsegments]: ./route#dynamic-segments
[request]: https://developer.mozilla.org/en-US/docs/Web/API/Request
[response]: https://developer.mozilla.org/en-US/docs/Web/API/Response
[url]: https://developer.mozilla.org/en-US/docs/Web/API/URL
[urlsearchparams]: https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams
[migratingtoremix]: ../guides/migrating-to-remix
[useloaderdata]: ../hooks/use-loader-data
[json]: ../fetch/json
[errorelement]: ./error-element
[pickingarouter]: ../routers/picking-a-router
[ssr]: ../guides/ssr
[partialhydration]: ../routers/create-browser-router#partial-hydration-data
