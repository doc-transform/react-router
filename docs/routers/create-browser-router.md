---
title: createBrowserRouter
new: true
---

# `createBrowserRouter`

这是所有 React Router Web 项目推荐使用的路由器。它使用 [DOM History API][historyapi] 来更新 URL 和管理历史栈。

它还启用了 v6.4 的数据 API，如 [loader][loader]、[action][action]、[fetcher][fetcher] 等。

<docs-info>由于数据 API 的设计将获取和渲染解耦，你应该在 React 树之外使用静态定义的路由集合来创建路由器。关于此设计的更多信息，请参阅 [Remixing React Router][remixing-react-router] 博客文章和 [When to Fetch][when-to-fetch] 会议演讲。</docs-info>

```tsx lines=[4,11-24]
import * as React from "react";
import * as ReactDOM from "react-dom";
import {
  createBrowserRouter,
  RouterProvider,
} from "react-router-dom";

import Root, { rootLoader } from "./routes/root";
import Team, { teamLoader } from "./routes/team";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    loader: rootLoader,
    children: [
      {
        path: "team",
        element: <Team />,
        loader: teamLoader,
      },
    ],
  },
]);

ReactDOM.createRoot(document.getElementById("root")).render(
  <RouterProvider router={router} />,
);
```

## 类型声明

```tsx
function createBrowserRouter(
  routes: RouteObject[],
  opts?: {
    basename?: string;
    future?: FutureConfig;
    hydrationData?: HydrationState;
    dataStrategy?: DataStrategyFunction;
    patchRoutesOnNavigation?: PatchRoutesOnNavigationFunction;
    window?: Window;
  },
): RemixRouter;
```

## `routes`

一个 [`Route`][route] 对象的数组，嵌套路由在 `children` 属性上。

```jsx
createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    loader: rootLoader,
    children: [
      {
        path: "events/:id",
        element: <Event />,
        loader: eventLoader,
      },
    ],
  },
]);
```

## `opts.basename`

当你无法部署到域名根目录而只能部署到子目录时，应用的基础路径名。

```jsx
createBrowserRouter(routes, {
  basename: "/app",
});
```

链接到根路径时会尊重末尾的斜杠：

```jsx
createBrowserRouter(routes, {
  basename: "/app",
});
<Link to="/" />; // results in <a href="/app" />

createBrowserRouter(routes, {
  basename: "/app/",
});
<Link to="/" />; // results in <a href="/app/" />
```

## `opts.future`

一组可选的 [Future Flags][api-development-strategy]，用于为此 Router 启用。我们建议尽早启用新发布的 future flag，以便将来更顺利地迁移到 v7。

```js
const router = createBrowserRouter(routes, {
  future: {
    // Normalize `useNavigation()`/`useFetcher()` `formMethod` to uppercase
    v7_normalizeFormMethod: true,
  },
});
```

当前可用的 future flag：

| Flag                                        | 描述                                                   |
| ------------------------------------------- | ------------------------------------------------------ |
| `v7_fetcherPersist`                         | 延迟活跃 fetcher 的清理直到它们返回 `idle` 状态        |
| `v7_normalizeFormMethod`                    | 将 `useNavigation().formMethod` 规范化为大写 HTTP 方法 |
| `v7_partialHydration`                       | 支持服务端渲染应用的部分注水                           |
| `v7_prependBasename`                        | 在导航/获取路径前添加路由器 basename                   |
| [`v7_relativeSplatPath`][relativesplatpath] | 修复通配符路由中相对路径解析的 bug                     |
| `v7_skipActionErrorRevalidation`            | 如果 action 返回 4xx/5xx `Response` 则默认不重新验证   |

## `opts.hydrationData`

当[服务端渲染][ssr]并[选择退出自动注水][hydrate-false]时，`hydrationData` 选项允许你传入服务端渲染的注水数据。这几乎总是你从 [handler.query][query] 获取的 `StaticHandlerContext` 值的数据子集：

```js
const router = createBrowserRouter(routes, {
  hydrationData: {
    loaderData: {
      // [routeId]: serverLoaderData
    },
    // may also include `errors` and/or `actionData`
  },
});
```

### 部分注水数据

你几乎总是会包含完整的 `loaderData` 来注水服务端渲染的应用。但在高级用例中（如 Remix 的 [`clientLoader`][clientloader]），你可能只想为服务端渲染的*部分*路由包含 `loaderData`。如果你想启用部分 `loaderData` 并选择使用细粒度的 [`route.HydrateFallback`][hydratefallback]，你需要启用 `future.v7_partialHydration` 标志。在此标志之前，提供的任何 `loaderData` 都被假定为完整的，不会导致初始注水时执行路由 loader。

当指定此标志时，loader 将在初始注水时在以下 2 种场景中运行：

- 没有提供注水数据
  - 在这些情况下，`HydrateFallback` 组件将在初始注水时渲染
- `loader.hydrate` 属性被设置为 `true`
  - 这允许你即使没有在初始注水时渲染后备组件也运行 `loader`（即，用注水数据填充缓存）

```js
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
    future: {
      v7_partialHydration: true,
    },
    hydrationData: {
      loaderData: {
        root: "ROOT DATA",
        // No index data provided
      },
    },
  },
);
```

## `opts.dataStrategy`

<docs-warning>这是一个为高级用例设计的低级 API。它会覆盖 React Router 内部对 `loader`/`action` 执行的处理，如果使用不当将会破坏你的应用代码。请谨慎使用并进行适当的测试。</docs-warning>

默认情况下，React Router 对你的数据如何加载/提交有其特定的处理方式——最显著的是并行执行所有 loader 以实现最优数据获取。虽然我们认为这是大多数用例的正确行为，但我们认识到对于广泛的应用需求而言，数据获取没有“一个尺寸适合所有人”的解决方案。

`dataStrategy` 选项让你完全控制 loader 和 action 的执行方式，并为构建更高级的 API（如中间件、上下文和缓存层）奠定了基础。随着时间推移，我们期望在内部利用此 API 为 React Router 带来更多一等公民 API，但在此之前（以及之后），这是你为应用的数据需求添加更高级功能的方式。

### 类型声明

```ts
interface DataStrategyFunction {
  (
    args: DataStrategyFunctionArgs,
  ): Promise<Record<string, DataStrategyResult>>;
}

interface DataStrategyFunctionArgs<Context = any> {
  request: Request;
  params: Params;
  context?: Context;
  matches: DataStrategyMatch[];
  fetcherKey: string | null;
}

interface DataStrategyMatch
  extends AgnosticRouteMatch<
    string,
    AgnosticDataRouteObject
  > {
  shouldLoad: boolean;
  resolve: (
    handlerOverride?: (
      handler: (ctx?: unknown) => DataFunctionReturnValue,
    ) => Promise<DataStrategyResult>,
  ) => Promise<DataStrategyResult>;
}

interface DataStrategyResult {
  type: "data" | "error";
  result: unknown; // data, Error, Response, DeferredData, DataWithResponseInit
}
```

### 概述

`dataStrategy` 接收与 `loader`/`action` 相同的参数（`request`、`params`），但它还接收 2 个新参数：`matches` 和 `fetcherKey`：

- **`matches`** - 匹配路由的数组，每个匹配项扩展了 2 个新字段供数据策略函数使用：
  - **`match.shouldLoad`** - 一个布尔值，表示是否应在此次传递中调用此路由的处理程序
    - `matches` 数组始终包含*所有*匹配的路由，即使只有*部分*路由处理程序需要被调用，这样中间件等功能就可以实现
    - `shouldLoad` 通常只在你完全跳过路由处理程序并实现自定义处理程序逻辑时才有意义——因为它让你确定自定义逻辑是否应该为此路由运行
    - 例如：
      - 如果你在 `/parent/child/a` 并导航到 `/parent/child/b` - 你会得到一个三个匹配项的数组（`[parent, child, b]`），但只有 `b` 会有 `shouldLoad=true`，因为 `parent` 和 `child` 的数据已经加载
      - 如果你在 `/parent/child/a` 并提交到 `a` 的 `action`，那么只有 `a` 会在 `dataStrategy` 的 action 执行中有 `shouldLoad=true`
        - `action` 之后，`dataStrategy` 会再次被调用进行 `loader` 重新验证，所有匹配项都会有 `shouldLoad=true`（假设没有自定义的 `shouldRevalidate` 实现）
  - **`match.resolve`** - 一个异步函数，将解析任何 `route.lazy` 实现并执行路由的处理程序（如果需要），返回一个 `DataStrategyResult`
    - 调用 `match.resolve` 并不意味着你在调用 `loader`/`action`（“处理程序”）—— `resolve` 只会在需要时*并且*你没有传递自己的 `handlerOverride` 函数参数时才在内部调用“处理程序”
    - 对所有匹配项调用 `match.resolve` 是安全的，即使它们有 `shouldLoad=false`，如果不需要加载它会执行空操作
    - 你通常应该总是为 `shouldLoad:true` 的路由调用 `match.resolve()` 以确保任何 `route.lazy` 实现都被处理
    - 请参阅下面的示例了解如何通过 `match.resolve` 实现自定义处理程序执行
- **`fetcherKey`** - 我们正在为其调用 `dataStrategy` 的 fetcher 的键，否则对于导航执行为 `null`

`dataStrategy` 函数应该返回一个 `routeId -> DataStrategyResult` 的键/值对象，并应该包含任何执行了处理程序的路由的条目。`DataStrategyResult` 根据 `DataStrategyResult["type"]` 字段指示处理程序是否成功。如果返回的 `DataStrategyResult["result"]` 是 `Response`，React Router 会为你解包它（通过 `res.json` 或 `res.text`）。如果你需要对 `Response` 进行自定义解码但想保留状态码，你可以使用 `data` 工具函数将解码后的数据与 `ResponseInit` 一起返回。

### 示例用例

#### 添加日志

在最简单的情况下，让我们看看如何钩入此 API 以添加一些日志记录，当路由的 loader/action 执行时：

```ts
let router = createBrowserRouter(routes, {
  async dataStrategy({ request, matches }) {
    // Grab only the matches we need to run handlers for
    const matchesToLoad = matches.filter(
      (m) => m.shouldLoad,
    );
    // Run the handlers in parallel, logging before and after
    const results = await Promise.all(
      matchesToLoad.map(async (match) => {
        console.log(`Processing ${match.route.id}`);
        // Don't override anything - just resolve route.lazy + call loader
        const result = await match.resolve();
        return result;
      }),
    );

    // Aggregate the results into a bn object of `routeId -> DataStrategyResult`
    return results.reduce(
      (acc, result, i) =>
        Object.assign(acc, {
          [matchesToLoad[i].route.id]: result,
        }),
      {},
    );
  },
});
```

如果你想避免使用 `reduce`，你可以手动构建 `results` 对象，但你需要手动构造 `DataStrategyResult`——指示处理程序是否成功：

```ts
let router = createBrowserRouter(routes, {
  async dataStrategy({ request, matches }) {
    const matchesToLoad = matches.filter(
      (m) => m.shouldLoad,
    );
    const results = {};
    await Promise.all(
      matchesToLoad.map(async (match) => {
        console.log(`Processing ${match.route.id}`);
        try {
          const result = await match.resolve();
          results[match.route.id] = {
            type: "data",
            result,
          };
        } catch (e) {
          results[match.route.id] = {
            type: "error",
            result: e,
          };
        }
      }),
    );

    return results;
  },
});
```

#### 中间件

让我们通过 `handle` 在每个路由上定义中间件，先顺序调用中间件，然后并行调用所有 loader——将中间件提供的数据传递给 loader：

```ts
const routes = [
  {
    id: "parent",
    path: "/parent",
    loader({ request }, context) {
      /*...*/
    },
    handle: {
      async middleware({ request }, context) {
        context.parent = "PARENT MIDDLEWARE";
      },
    },
    children: [
      {
        id: "child",
        path: "child",
        loader({ request }, context) {
          /*...*/
        },
        handle: {
          async middleware({ request }, context) {
            context.child = "CHILD MIDDLEWARE";
          },
        },
      },
    ],
  },
];

let router = createBrowserRouter(routes, {
  async dataStrategy({ request, params, matches }) {
    // Run middleware sequentially and let them add data to `context`
    let context = {};
    for (const match of matches) {
      if (match.route.handle?.middleware) {
        await match.route.handle.middleware(
          { request, params },
          context,
        );
      }
    }

    // Run loaders in parallel with the `context` value
    let matchesToLoad = matches.filter((m) => m.shouldLoad);
    let results = await Promise.all(
      matchesToLoad.map((match, i) =>
        match.resolve((handler) => {
          // Whatever you pass to `handler` will be passed as the 2nd parameter
          // to your loader/action
          return handler(context);
        }),
      ),
    );
    return results.reduce(
      (acc, result, i) =>
        Object.assign(acc, {
          [matchesToLoad[i].route.id]: result,
        }),
      {},
    );
  },
});
```

#### 自定义处理程序

你甚至可能不想在路由级别定义 loader 实现。也许你只想确定路由并为所有数据发出单个 GraphQL 请求？你可以通过设置 `route.loader=true` 使其符合“拥有 loader”的条件，然后将 GQL 片段存储在 `route.handle` 上：

```ts
const routes = [
  {
    id: "parent",
    path: "/parent",
    loader: true,
    handle: {
      gql: gql`
        fragment Parent on Whatever {
          parentField
        }
      `,
    },
    children: [
      {
        id: "child",
        path: "child",
        loader: true,
        handle: {
          gql: gql`
            fragment Child on Whatever {
              childField
            }
          `,
        },
      },
    ],
  },
];

let router = createBrowserRouter(routes, {
  dataStrategy({ request, params, matches }) {
    // Compose route fragments into a single GQL payload
    let gql = getFragmentsFromRouteHandles(matches);
    let data = await fetchGql(gql);
    // Parse results back out into individual route level `DataStrategyResult`'s
    // keyed by `routeId`
    let results = parseResultsFromGql(data);
    return results;
  },
});
```

## `opts.patchRoutesOnNavigation`

默认情况下，React Router 希望你通过 `createBrowserRouter(routes)` 预先提供完整的路由树。这允许 React Router 执行同步路由匹配、执行 loader，然后以最优化的方式渲染路由组件，不会引入瀑布式加载。代价是你的初始 JS 包定义上更大——这可能会随着应用的增长而减慢应用启动时间。

为了解决这个问题，我们在 [v6.9.0][6-9-0] 中引入了 [`route.lazy`][route-lazy]，它允许你懒加载路由*实现*（`loader`、`Component` 等），同时仍然预先提供路由*定义*方面（`path`、`index` 等）。这是一个好的折中方案，因为 React Router 仍然预先了解你的路由定义（轻量的部分）并可以执行同步路由匹配，但会延迟加载任何路由实现方面（较重的部分）直到实际导航到该路由。

在某些情况下，这仍然不够。对于非常大的应用，预先提供所有路由定义可能代价过高。此外，在某些微前端或模块联邦架构中，甚至可能无法预先提供所有路由定义。

这就是 `patchRoutesOnNavigation` 的用武之地（[RFC][fog-of-war-rfc]）。此 API 适用于无法预先提供完整路由树并需要在运行时延迟“发现”路由树的一部分的高级用例。此功能通常被称为 [“战争迷雾”][fog-of-war]，因为类似于视频游戏如何随着你的移动扩展“世界”——路由器会随着用户在应用中的导航扩展其路由树——但最终只会加载用户访问的部分。

### 类型声明

```ts
export interface PatchRoutesOnNavigationFunction {
  (opts: {
    path: string;
    matches: RouteMatch[];
    patch: (
      routeId: string | null,
      children: RouteObject[],
    ) => void;
  }): void | Promise<void>;
}
```

### 概述

当 React Router 无法匹配 `path` 时，`patchRoutesOnNavigation` 将被调用。参数包括 `path`、任何部分 `matches`以及一个 `patch` 函数，你可以调用它将新路由补丁到树的特定位置。此方法在 `GET` 请求的导航 `loading` 阶段和非 `GET` 请求的导航 `submitting` 阶段执行。

**将子路由补丁到现有路由**

```jsx
const router = createBrowserRouter(
  [
    {
      id: "root",
      path: "/",
      Component: RootComponent,
    },
  ],
  {
    async patchRoutesOnNavigation({ path, patch }) {
      if (path === "/a") {
        // Load/patch the `a` route as a child of the route with id `root`
        let route = await getARoute();
        //  ^ { path: 'a', Component: A }
        patch("root", [route]);
      }
    },
  },
);
```

在上面的示例中，如果用户点击一个指向 `/a` 的链接，React Router 最初不会匹配任何路由，并会以 `path = "/a"` 和包含根路由匹配的 `matches` 数组调用 `patchRoutesOnNavigation`。通过调用 `patch('root', [route])`，新路由将作为 `root` 路由的子路由添加到路由树中，React Router 将对更新后的路由执行匹配。这次它将成功匹配 `/a` 路径，导航将成功完成。

**补丁新的根级路由**

如果你需要将新路由补丁到树的顶部（即它没有父路由），你可以传递 `null` 作为 `routeId`：

```jsx
const router = createBrowserRouter(
  [
    {
      id: "root",
      path: "/",
      Component: RootComponent,
    },
  ],
  {
    async patchRoutesOnNavigation({ path, patch }) {
      if (path === "/root-sibling") {
        // Load/patch the `/root-sibling` route as a sibling of the root route
        let route = await getRootSiblingRoute();
        //  ^ { path: '/root-sibling', Component: RootSibling }
        patch(null, [route]);
      }
    },
  },
);
```

**异步补丁子树**

你还可以执行异步匹配来延迟获取应用的整个部分：

```jsx
let router = createBrowserRouter(
  [
    {
      path: "/",
      Component: Home,
    },
  ],
  {
    async patchRoutesOnNavigation({ path, patch }) {
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

<docs-info>如果正在执行的 `patchRoutesOnNavigation` 被后续的导航中断，那么被中断的执行中任何剩余的 `patch` 调用将不会更新路由树，因为该操作已被取消。</docs-info>

**将路由发现与路由定义放在一起**

如果你不希望执行自己的伪匹配，你可以利用部分 `matches` 数组和路由上的 `handle` 字段来将子路由定义与路由定义放在一起：

```jsx
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
          // If we want to include /dashboard in the critical routes, we need to
          // also include it's index route since patchRoutesOnNavigation will not be
          // called on a navigation to `/dashboard` because it will have successfully
          // matched the `/dashboard` parent route
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

### 关于带参数的路由的说明

因为 React Router 使用排名路由来为给定路径找到最佳匹配，当在任何给定时间点只知道部分路由树时，会引入一个有趣的模糊性。如果我们匹配一个完全静态的路由（如 `path: "/about/contact-us"`），那么我们知道我们已找到正确的匹配，因为它完全由静态 URL 片段组成，因此我们不需要费心询问任何其他可能得分更高的路由。

然而，带参数的路由（动态或通配符）不能做出这种假设，因为可能存在尚未发现的得分更高的路由。考虑一个完整的路由树，如：

```js
// Assume this is the full route tree for your app
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

然后假设我们想使用 `patchRoutesOnNavigation` 在用户导航时填充它：

```js
// Start with only the index route
const router = createBrowserRouter(
  [
    {
      path: "/",
      Component: Home,
    },
  ],
  {
    patchRoutesOnNavigation({ path, patch }) {
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

如果用户先访问一篇博客文章（即 `/blog/my-post`），我们会补丁 `:slug` 路由。然后如果用户导航到 `/blog/new` 来写新文章，我们会匹配 `/blog/:slug` 但它不是*正确的*匹配！我们需要调用 `patchRoutesOnNavigation` 以防存在尚未发现的得分更高的路由，在这种情况下确实存在。

因此，任何时候 React Router 匹配到的路径包含至少一个参数时，它会调用 `patchRoutesOnNavigation` 并再次匹配路由，以确认已找到最佳匹配。

如果你的 `patchRoutesOnNavigation` 实现开销较大或者向后端服务器发起副作用的 `fetch` 调用，你可能需要考虑跟踪之前见过的路由，以避免在你知道已经找到正确路由的情况下过度获取。这通常可以简单地维护一个小型缓存，记录之前已经补丁了正确路由的 `path` 值：

```js
let discoveredRoutes = new Set();

const router = createBrowserRouter(routes, {
  patchRoutesOnNavigation({ path, patch }) {
    if (discoveredRoutes.has(path)) {
      // We've seen this before so nothing to patch in and we can let the router
      // use the routes it already knows about
      return;
    }

    discoveredRoutes.add(path);

    // ... patch routes in accordingly
  },
});
```

## `opts.window`

对于浏览器开发工具插件或测试等需要使用与全局 `window` 不同的窗口的环境很有用。

[loader]: ../route/loader
[action]: ../route/action
[fetcher]: ../hooks/use-fetcher
[route]: ../route/route
[historyapi]: https://developer.mozilla.org/en-US/docs/Web/API/History
[api-development-strategy]: ../guides/api-development-strategy
[remixing-react-router]: https://remix.run/blog/remixing-react-router
[when-to-fetch]: https://www.youtube.com/watch?v=95B8mnhzoCM
[ssr]: ../guides/ssr
[hydrate-false]: ../routers/static-router-provider#hydrate
[query]: ./create-static-handler#handlerqueryrequest-opts
[clientloader]: https://remix.run/route/client-loader
[hydratefallback]: ../route/hydrate-fallback-element
[relativesplatpath]: ../hooks/use-resolved-path#splat-paths
[route-lazy]: ../route/lazy
[6-9-0]: https://github.com/remix-run/react-router/blob/main/CHANGELOG.md#v690
[fog-of-war]: https://en.wikipedia.org/wiki/Fog_of_war
[fog-of-war-rfc]: https://github.com/remix-run/react-router/discussions/11113
