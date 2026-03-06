---
title: createMemoryRouter
---

# createMemoryRouter

[MODES: data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.createMemoryRouter.html)

创建一个使用内存 [`History`](https://developer.mozilla.org/en-US/docs/Web/API/History) 栈管理应用路径的 [`DataRouter`](https://api.reactrouter.com/v7/interfaces/react-router.DataRouter.html)。适用于没有 DOM API 的非浏览器环境。

## 函数签名

```tsx
function createMemoryRouter(
  routes: RouteObject[],
  opts?: MemoryRouterOpts,
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

### opts.hydrationData

用于初始化路由器的注水数据，适用于已在服务端完成数据加载的场景。

### opts.initialEntries

内存历史栈中的初始条目。

### opts.initialIndex

应用应初始化到的 `initialEntries` 索引。

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

## 返回值

初始化后的 [`DataRouter`](https://api.reactrouter.com/v7/interfaces/react-router.DataRouter.html)，传递给 [`<RouterProvider>`](../data-routers/RouterProvider)。
