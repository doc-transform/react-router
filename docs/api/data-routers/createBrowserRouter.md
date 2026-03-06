---
title: createBrowserRouter
---

# createBrowserRouter

[MODES: data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.createBrowserRouter.html)

创建一个新的[数据路由](https://api.reactrouter.com/v7/interfaces/react-router.DataRouter.html)，通过 [`history.pushState`](https://developer.mozilla.org/en-US/docs/Web/API/History/pushState) 和 [`history.replaceState`](https://developer.mozilla.org/en-US/docs/Web/API/History/replaceState) 管理应用路径。

## 函数签名

```tsx
function createBrowserRouter(
  routes: RouteObject[],
  opts?: DOMRouterOpts,
): DataRouter;
```

## 参数

### routes

应用路由。

### opts.basename

应用的基础路径。

### opts.dataStrategy

覆盖默认的并行运行 loader 的数据策略 — 详情请参阅[文档](../../how-to/data-strategy)。

```tsx
let router = createBrowserRouter(routes, {
  async dataStrategy({
    matches,
    request,
    runClientMiddleware,
  }) {
    const matchesToLoad = matches.filter((m) =>
      m.shouldCallHandler(),
    );

    const results: Record<string, DataStrategyResult> = {};
    await runClientMiddleware(() =>
      Promise.all(
        matchesToLoad.map(async (match) => {
          results[match.route.id] = await match.resolve();
        }),
      ),
    );
    return results;
  },
});
```

### opts.future

路由器的 future flag。

### opts.getContext

返回 [`RouterContextProvider`](../utils/RouterContextProvider) 实例的函数，作为客户端 [`action`](../../start/data/route-object#action)、[`loader`](../../start/data/route-object#loader) 和[中间件](../../how-to/middleware)的 `context` 参数提供。每次导航或 fetcher 调用都会调用此函数生成新的 `context` 实例。

```tsx
import {
  createContext,
  RouterContextProvider,
} from "react-router";

const apiClientContext = createContext<APIClient>();

function createBrowserRouter(routes, {
  getContext() {
    let context = new RouterContextProvider();
    context.set(apiClientContext, getApiClient());
    return context;
  }
})
```

### opts.hydrationData

在服务端渲染并选择退出自动注水时，`hydrationData` 选项允许你传入服务端渲染的注水数据。这几乎总是来自 [`StaticHandler`](https://api.reactrouter.com/v7/interfaces/react-router.StaticHandler.html) 的 `query` 方法返回的 [`StaticHandlerContext`](https://api.reactrouter.com/v7/interfaces/react-router.StaticHandlerContext.html) 值的子集：

```tsx
const router = createBrowserRouter(routes, {
  hydrationData: {
    loaderData: {
      // [routeId]: serverLoaderData
    },
    // 也可能包含 `errors` 和/或 `actionData`
  },
});
```

**部分注水数据**

通常你会包含完整的 `loaderData` 来注水服务端渲染的应用。但在高级用例中（如框架模式的 [`clientLoader`](../../start/framework/route-module#clientLoader)），你可能只想为部分在服务端加载/渲染的路由包含 `loaderData`。这允许你注水*部分*路由（如应用布局/外壳），同时为其他路由显示 `HydrateFallback` 组件并在注水期间运行 [`loader`](../../start/data/route-object#loader)。

路由 [`loader`](../../start/data/route-object#loader) 在以下两种情况下会在注水期间运行：

1.  未提供注水数据
    在这种情况下，`HydrateFallback` 组件会在初始注水时渲染
2.  `loader.hydrate` 属性设为 `true`
    这允许你在未渲染初始注水备用组件的情况下仍运行 [`loader`](../../start/data/route-object#loader)（例如，用注水数据预填充缓存）

```tsx
const router = createBrowserRouter(
  [
    {
      id: "root",
      loader: rootLoader,
      Component: Root,
      children: [
        {
          id: "index",
          loader: indexLoader,
          HydrateFallback: IndexSkeleton,
          Component: Index,
        },
      ],
    },
  ],
  {
    hydrationData: {
      loaderData: {
        root: "ROOT DATA",
        // 未提供 index 数据
      },
    },
  },
);
```

### opts.unstable_instrumentations

插桩对象数组，允许你在路由器初始化之前（以及通过 `route.lazy` 或 `patchRoutesOnNavigation` 后续添加的路由上）对路由器和单个路由进行插桩。这主要用于可观测性，例如用日志记录和/或性能追踪包裹导航、fetch 以及路由的 loader/action/中间件。详情请参阅[文档](../../how-to/instrumentation)。

```tsx
let router = createBrowserRouter(routes, {
  unstable_instrumentations: [logging],
});

let logging = {
  router({ instrument }) {
    instrument({
      navigate: (impl, info) =>
        logExecution(`navigate ${info.to}`, impl),
      fetch: (impl, info) =>
        logExecution(`fetch ${info.to}`, impl),
    });
  },
  route({ instrument, id }) {
    instrument({
      middleware: (impl, info) =>
        logExecution(
          `middleware ${info.request.url} (route ${id})`,
          impl,
        ),
      loader: (impl, info) =>
        logExecution(
          `loader ${info.request.url} (route ${id})`,
          impl,
        ),
      action: (impl, info) =>
        logExecution(
          `action ${info.request.url} (route ${id})`,
          impl,
        ),
    });
  },
};

async function logExecution(
  label: string,
  impl: () => Promise<void>,
) {
  let start = performance.now();
  console.log(`start ${label}`);
  await impl();
  let duration = Math.round(performance.now() - start);
  console.log(`end ${label} (${duration}ms)`);
}
```

### opts.patchRoutesOnNavigation

在导航时延迟定义路由树的部分内容。
参见 [`PatchRoutesOnNavigationFunction`](https://api.reactrouter.com/v7/types/react-router.PatchRoutesOnNavigationFunction.html)。

默认情况下，React Router 希望你通过 `createBrowserRouter(routes)` 预先提供完整的路由树。这允许 React Router 以最优方式执行同步路由匹配、执行 loader，然后渲染路由组件，而不引入瀑布流。代价是你的初始 JS 包体积更大 — 随着应用增长可能会减慢应用启动时间。

为解决这个问题，我们在 [v6.9.0](https://github.com/remix-run/react-router/blob/main/CHANGELOG.md#v690) 中引入了 [`route.lazy`](../../start/data/route-object#lazy)，它允许你延迟加载路由*实现*（[`loader`](../../start/data/route-object#loader)、[`Component`](../../start/data/route-object#Component) 等），同时仍预先提供路由*定义*部分（`path`、`index` 等）。这是一个很好的折中方案：React Router 仍然预先知道你的路由定义（轻量部分），可以执行同步路由匹配，但延迟加载路由实现部分（较重部分），直到实际导航到该路由。

在某些情况下，这还不够。对于大型应用，预先提供所有路由定义可能代价过高。此外，在某些微前端或模块联邦架构中，可能根本无法预先提供所有路由定义。

这就是 `patchRoutesOnNavigation` 的用武之地（[RFC](https://github.com/remix-run/react-router/discussions/11113)）。此 API 适用于你无法预先提供完整路由树、需要在运行时延迟"发现"路由树部分的高级用例。此功能通常被称为["战争迷雾"](https://en.wikipedia.org/wiki/Fog_of_war)，类似于视频游戏中随着你的移动扩展"世界" — 路由器会随着用户在应用中导航而扩展其路由树 — 但只会加载用户访问过的部分。

`patchRoutesOnNavigation` 会在 React Router 无法匹配 `path` 时调用。参数包括 `path`、任何部分 `matches`，以及一个 `patch` 函数，你可以调用它将新路由补丁到树的特定位置。此方法在 `GET` 请求的导航 `loading` 阶段和非 `GET` 请求的导航 `submitting` 阶段执行。

<details>
  <summary><b><code>patchRoutesOnNavigation</code> 使用示例</b></summary>

**将子路由补丁到现有路由中**

```tsx
const router = createBrowserRouter(
  [
    {
      id: "root",
      path: "/",
      Component: RootComponent,
    },
  ],
  {
    async patchRoutesOnNavigation({ patch, path }) {
      if (path === "/a") {
        // 将 `a` 路由作为 id 为 `root` 的路由的子路由加载/补丁
        let route = await getARoute();
        //  ^ { path: 'a', Component: A }
        patch("root", [route]);
      }
    },
  },
);
```

在上面的例子中，如果用户点击链接到 `/a`，React Router 最初不会匹配任何路由，并会调用 `patchRoutesOnNavigation`，传入 `path = "/a"` 和包含根路由匹配的 `matches` 数组。通过调用 `patch('root', [route])`，新路由将作为 `root` 路由的子路由添加到路由树中，React Router 将对更新后的路由重新执行匹配。这次它将成功匹配 `/a` 路径，导航将成功完成。

**补丁新的根级路由**

如果你需要将新路由补丁到树的顶层（即它没有父路由），可以传 `null` 作为 `routeId`：

```tsx
const router = createBrowserRouter(
  [
    {
      id: "root",
      path: "/",
      Component: RootComponent,
    },
  ],
  {
    async patchRoutesOnNavigation({ patch, path }) {
      if (path === "/root-sibling") {
        // 将 `/root-sibling` 路由作为根路由的兄弟路由加载/补丁
        let route = await getRootSiblingRoute();
        //  ^ { path: '/root-sibling', Component: RootSibling }
        patch(null, [route]);
      }
    },
  },
);
```

**异步补丁子树**

你也可以通过异步匹配来延迟获取应用的整个部分：

```tsx
let router = createBrowserRouter(
  [
    {
      path: "/",
      Component: Home,
    },
  ],
  {
    async patchRoutesOnNavigation({ patch, path }) {
      if (path.startsWith("/dashboard")) {
        let children = await import("./dashboard");
        patch(null, children);
      }
      if (path.startsWith("/account")) {
        let children = await import("./account");
        patch(null, children);
      }
    },
  },
);
```

<docs-info>如果正在执行的 `patchRoutesOnNavigation` 被后续导航中断，则被中断执行中的任何剩余 `patch` 调用将不会更新路由树，因为操作已被取消。</docs-info>

**将路由发现与路由定义共置**

如果你不想执行自己的伪匹配，可以利用部分 `matches` 数组和路由上的 [`handle`](../../start/data/route-object#handle) 字段来保持子路由定义共置：

```tsx
let router = createBrowserRouter(
  [
    {
      path: "/",
      Component: Home,
    },
    {
      path: "/dashboard",
      children: [
        {
          // 如果我们想将 /dashboard 包含在关键路由中，我们还需要包含它的索引路由，
          // 因为导航到 `/dashboard` 时不会调用 patchRoutesOnNavigation，
          // 因为它已经成功匹配了 `/dashboard` 父路由
          index: true,
          // ...
        },
      ],
      handle: {
        lazyChildren: () => import("./dashboard"),
      },
    },
    {
      path: "/account",
      children: [
        {
          index: true,
          // ...
        },
      ],
      handle: {
        lazyChildren: () => import("./account"),
      },
    },
  ],
  {
    async patchRoutesOnNavigation({ matches, patch }) {
      let leafRoute = matches[matches.length - 1]?.route;
      if (leafRoute?.handle?.lazyChildren) {
        let children =
          await leafRoute.handle.lazyChildren();
        patch(leafRoute.id, children);
      }
    },
  },
);
```

**关于带参数路由的说明**

由于 React Router 使用排名路由来找到给定路径的最佳匹配，当在任意时间点只知道部分路由树时，会引入一个有趣的歧义。如果我们匹配了一个完全静态的路由（如 `path: "/about/contact-us"`），我们知道找到了正确的匹配，因为它完全由静态 URL 段组成。因此，我们不需要去查找其他可能得分更高的路由。

然而，带参数的路由（动态或通配符）无法做出这种假设，因为可能存在尚未发现的、得分更高的路由。考虑一个完整的路由树：

```tsx
// 假设这是你应用的完整路由树
const routes = [
  {
    path: "/",
    Component: Home,
  },
  {
    id: "blog",
    path: "/blog",
    Component: BlogLayout,
    children: [
      { path: "new", Component: NewPost },
      { path: ":slug", Component: BlogPost },
    ],
  },
];
```

然后假设我们想使用 `patchRoutesOnNavigation` 在用户导航时逐步填充：

```tsx
// 只从索引路由开始
const router = createBrowserRouter(
  [
    {
      path: "/",
      Component: Home,
    },
  ],
  {
    async patchRoutesOnNavigation({ patch, path }) {
      if (path === "/blog/new") {
        patch("blog", [
          {
            path: "new",
            Component: NewPost,
          },
        ]);
      } else if (path.startsWith("/blog")) {
        patch("blog", [
          {
            path: ":slug",
            Component: BlogPost,
          },
        ]);
      }
    },
  },
);
```

如果用户首先访问博客文章（即 `/blog/my-post`），我们会补丁 `:slug` 路由。然后，如果用户导航到 `/blog/new` 写新文章，我们会匹配 `/blog/:slug`，但这不是*正确的*匹配！我们需要调用 `patchRoutesOnNavigation` 以防存在尚未发现的、得分更高的路由——在这个例子中确实存在。

因此，每当 React Router 匹配的路径包含至少一个参数时，它都会调用 `patchRoutesOnNavigation` 并重新匹配路由，以确认找到了最佳匹配。

如果你的 `patchRoutesOnNavigation` 实现开销很大或向后端服务器发起副作用 [`fetch`](https://developer.mozilla.org/en-US/docs/Web/API/fetch) 调用，你可能需要考虑跟踪之前见过的路由，以避免在已知找到正确路由的情况下过度获取。通常只需维护一个先前 `path` 值的小缓存即可：

```tsx
let discoveredRoutes = new Set();

const router = createBrowserRouter(routes, {
  async patchRoutesOnNavigation({ patch, path }) {
    if (discoveredRoutes.has(path)) {
      // 我们之前见过这个，所以没有需要补丁的内容，让路由器使用它已知的路由
      return;
    }

    discoveredRoutes.add(path);

    // ... 相应地补丁路由
  },
});
```

</details>

### opts.window

[`Window`](https://developer.mozilla.org/en-US/docs/Web/API/Window) 对象覆盖。默认为全局 `window` 实例。

## 返回值

初始化后的[数据路由](https://api.reactrouter.com/v7/interfaces/react-router.DataRouter.html)，传递给 [`<RouterProvider>`](../data-routers/RouterProvider)。
