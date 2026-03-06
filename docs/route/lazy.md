---
title: lazy
new: true
---

# `lazy`

为了保持应用包的小体积并支持路由的代码分割，每个路由可以提供一个异步函数来解析路由定义中非路由匹配的部分（`loader`、`action`、`Component`/`element`、`ErrorBoundary`/`errorElement` 等）。

懒加载路由在初始加载以及导航或 fetcher 调用的 `loading` 或 `submitting` 阶段被解析。你不能延迟定义路由匹配属性（`path`、`index`、`children`、`caseSensitive`），因为我们只在匹配已知路由后才执行懒加载路由函数。

<docs-warning>此功能仅在使用数据路由器时有效，参见[选择路由器][pickingarouter]</docs-warning>

每个 `lazy` 函数通常返回动态导入的结果。

```jsx
let routes = createRoutesFromElements(
  <Route path="/" element={<Layout />}>
    <Route path="a" lazy={() => import("./a")} />
    <Route path="b" lazy={() => import("./b")} />
  </Route>,
);
```

然后在你的懒加载路由模块中，导出你想要为路由定义的属性（`loader`、`Component`、`ErrorBoundary`）：

```jsx
export async function loader({ request }) {
  let data = await fetchData(request);
  return json(data);
}

export function Component() {
  let data = useLoaderData();

  return (
    <>
      <h1>You made it!</h1>
      <p>{data}</p>
    </>
  );
}

// 如果你想在 React 开发工具中自定义组件显示名称：
Component.displayName = "SampleLazyRoute";

export function ErrorBoundary() {
  let error = useRouteError();
  return isRouteErrorResponse(error) ? (
    <h1>
      {error.status} {error.statusText}
    </h1>
  ) : (
    <h1>{error.message || error}</h1>
  );
}

// 如果你想在 React 开发工具中自定义组件显示名称：
ErrorBoundary.displayName = "SampleErrorBoundary";
```

<docs-info>
注意这个懒加载文件中没有 `default` 导出。这是因为 `default` 不是路由对象上的有效键。这些文件通常应该只导出你会在路由对象上定义的键，比如 `loader`、`action`、`Component`、`ErrorBoundary` 等。所有导出都会直接展开到路由对象上，除非你从 `lazy` 手动返回一个对象。
</docs-info>

## 静态定义的属性

任何在路由上静态定义的属性都不能被 `lazy` 函数覆盖，如果你尝试覆盖它们，你会收到控制台警告。

此外，作为一种优化，如果你静态定义了 `loader`/`action`，它将与 `lazy` 函数并行调用。如果你有不介意放在关键包中的轻量 loader，并且想要并行启动数据获取和组件下载，这很有用。这接近于 Remix 处理获取的方式，因为每个路由都是自己的 API 路由。

```js
let route = {
  path: "projects",
  loader: ({ request }) => fetchDataForUrl(request.url),
  lazy: () => import("./projects"),
};
```

这还允许你进行更细粒度的代码分割。例如，你可以将 `loader` 和 `Component` 分到不同的文件中以便并行下载：

```js
let route = {
  path: "projects",
  async loader({ request, params }) {
    let { loader } = await import("./projects-loader");
    return loader({ request, params });
  },
  lazy: () => import("./projects-component"),
};
```

## 单个文件中的多个路由

虽然 `lazy` 通常按 1:1 的方式每个路由对应一个异步 `import()` 使用，但你可以自由实现更高级的 `lazy` 函数，只需返回你想添加到该路由的属性即可。这开启了一些有趣的可能性。

例如，如果你想避免为嵌套路由加载多个代码块，你可以将它们全部存储在同一个文件中，并将它们分别返回给各个路由。现代打包工具对于不同 `import()` 调用的同一个 Promise 会进行合并。

```js
// 假设 pages/Dashboard.jsx 包含了多个仪表盘路由的所有 loader/组件
let dashboardRoute = {
  path: "dashboard",
  async lazy() {
    let { Layout } = await import("./pages/Dashboard");
    return { Component: Layout };
  },
  children: [
    {
      index: true,
      async lazy() {
        let { Index } = await import("./pages/Dashboard");
        return { Component: Index };
      },
    },
    {
      path: "messages",
      async lazy() {
        let { messagesLoader, Messages } = await import(
          "./pages/Dashboard"
        );
        return {
          loader: messagesLoader,
          Component: Messages,
        };
      },
    },
  ],
};
```

[pickingarouter]: ../routers/picking-a-router
