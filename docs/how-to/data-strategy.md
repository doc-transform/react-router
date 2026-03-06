---
title: 数据策略
---

# 数据策略

[MODES: data]

<br />
<br />

<docs-warning>这是一个面向高级用例的低级 API。它覆盖了 React Router 内部对 `action`/`loader` 执行的处理，如果使用不当将会破坏你的应用代码。请谨慎使用并进行适当的测试。</docs-warning>

## 概述

默认情况下，React Router 对数据的加载/提交方式有自己的约定——最值得注意的是，它会并行执行所有 [`loader`][loader] 函数以实现最优的数据获取。虽然我们认为这对大多数用例来说是正确的行为，但在面对各种应用需求的数据获取场景时，并没有"放之四海而皆准"的解决方案。

[`dataStrategy`][data-strategy] 选项让你完全控制 [`action`][action]/[`loader`][loader] 函数的执行方式，并为构建更高级的 API（如中间件、上下文和缓存层）奠定基础。随着时间推移，我们期望内部利用此 API 为 React Router 带来更多一流的 API，但在此之前（以及之后），这是你为应用数据需求添加更高级功能的方式。

## 用法

自定义 `dataStrategy` 接收 `loader`/`action` 参数（`request`、`params`、`context`）以及一些额外的参数，让你决定如何控制应用的执行：

- `matches`：当前 `request` 匹配路由的 `DataStrategyMatch` 实例数组
- `runClientMiddleware`：运行匹配路由中间件的辅助函数
- `fetcherKey`：如果这是 fetcher 请求而非导航，则为 fetcher 的 key

`DataStrategyMatch` 是普通路由匹配加上一些额外字段：

- `shouldCallHandler`：告诉你该路由的处理器是否应在此请求中被调用的函数
- `shouldRevalidateArgs`：此请求中传递给路由 `shouldRevalidate` 的参数
- ~~`shouldLoad`~~：该路由处理器是否应在此请求中运行的布尔字段
  - 已弃用，推荐使用更强大的 `shouldCallHandler` API
- `resolve`：处理对路由处理器的调用，并允许你自定义处理器的执行

以下是一个在处理器执行周围添加日志的基本示例：

```tsx
let router = createBrowserRouter(routes, {
  async dataStrategy({
    matches,
    request,
    runClientMiddleware,
  }) {
    // 确定此请求中哪些匹配项预期被执行。
    // - 对于加载导航，这将对新路由 + 需要重新验证的现有路由返回 true
    // - 对于提交导航，这只对 action 路由返回 true
    // - 对于 fetcher 调用，这只对 fetcher 路由返回 true
    const matchesToLoad = matches.filter((m) =>
      m.shouldCallHandler(),
    );

    // 对于每个我们要执行的匹配项，调用 match.resolve() 执行处理器并存储结果
    const results: Record<string, DataStrategyResult> = {};
    await runClientMiddleware(() =>
      Promise.all(
        matchesToLoad.map(async (match) => {
          console.log(`处理 ${match.route.id}`);
          // resolve 函数调用路由处理器
          results[match.route.id] = await match.resolve();
        }),
      ),
    );
    return results;
  },
});
```

`dataStrategy` 函数应返回一个 `Record<string, DataStrategyResult>`，包含每个被执行处理器的结果。`DataStrategyResult` 只是一个包装对象，指示处理器是返回了还是抛出了：

```ts
interface DataStrategyResult {
  type: "data" | "error";
  result: unknown; // data, Error, Response, data()
}
```

### 调用路由中间件

如果你的路由上使用了 `middleware`，需要利用 `callClientMiddleware` 辅助函数在处理器周围执行 `middleware`：

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

    // 运行中间件并在中间件链末尾执行处理器
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

`runClientMiddleware` 接受与 `dataStrategy` 相同的参数，因此也可以轻松与独立的 `dataStrategy` 实现组合：

```tsx
const loggingDataStrategy: DataStrategyFunction = () => {
  /* ... */
};

let router = createBrowserRouter(routes, {
  async dataStrategy({ runClientMiddleware }) {
    let results = await runClientMiddleware(
      loggingDataStrategy,
    );
    return results;
  },
});
```

### 高级处理器执行

如果你想更精细地控制处理器的执行，可以向 `match.resolve()` 传递回调：

```tsx
// 假设 loader 形式如下
function loader({ request }, customContext) {...}

// 在你的 dataStrategy 中，你可以从 resolve 回调内部传递这个上下文
await Promise.all(
  matchesToLoad.map((match, i) =>
    match.resolve((handler) => {
      let customContext = getCustomContext();
      // 调用处理器并将自定义参数作为处理器的第二个参数传递
      return handler(customContext);
    }),
  ),
);
```

### 自定义重新验证行为

如果你想更改重新验证行为，可以向 `match.shouldCallHandler()` 传递自己的 `defaultShouldRevalidate`，它将传递给任何路由级别的 `shouldRevalidate` 函数。传递给路由级别 `shouldRevalidate` 的参数可在 `match.shouldRevalidateArgs` 上获取：

```tsx
const matchesToLoad = matches.filter((match) => {
  let defaultShouldRevalidate = customShouldRevalidate(
    match.shouldRevalidateArgs,
  );
  return m.shouldCallHandler(defaultShouldRevalidate);
});
```

## 从 `shouldLoad` 迁移

现在新的 `match.shouldCallHandler()`/`match.shouldRevalidateArgs` 字段已稳定，建议从现已弃用的 `match.shouldLoad` API 迁移。之前的布尔方法不允许自定义 `dataStrategy` 函数更改默认的重新验证行为，因此创建了新的基于函数的 API 来实现这一点。

这两个 API 之间的主要区别是，使用 `shouldLoad` 时，调用 `resolve()` _只有_ 在 `shouldLoad` 为 `true` 时才会调用处理器。你可以安全地对所有匹配项调用它，即使只有一部分需要执行它们的处理器。

使用 `shouldCallHandler` 时，你负责决定应该调用哪些处理器，因此调用 resolve 会自动调用处理器。你应该只对希望运行处理器的匹配项集合调用 resolve。

以下是从旧 API 到新 API 的示例变更。注意我们在调用 `resolve()` 之前预过滤了 `matchesToLoad`：

```diff
let results = {};
+let matchesToLoad = matches.filter(m => m.shouldCallHandler());
await Promise.all(() =>
-  matches.map((m) => {
+  matchesToLoad.map((m) => {
    results[m.route.id] = await m.resolve();
  }),
);
return results;
```

## 高级用例

### 自定义中间件

<docs-info>这是一个不太可能的用例，因为 React Router 已经内置了中间件，但如果你想使用自定义中间件，可以通过 `dataStrategy` 来实现。</docs-info>

让我们在每个路由上通过 [`handle`](../../start/data/route-object#handle) 定义中间件，
先按顺序调用中间件，然后并行调用所有
[`loader`](../../start/data/route-object#loader) —— 提供通过中间件获得的任何数据：

```ts
const routes = [
  {
    id: "parent",
    path: "/parent",
    loader({ request }, context) {
      // ...
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
          // ...
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
  async dataStrategy({ matches, params, request }) {
    // 按顺序运行中间件并让它们向 `context` 添加数据
    let context = {};
    for (const match of matches) {
      if (match.route.handle?.middleware) {
        await match.route.handle.middleware(
          { request, params },
          context,
        );
      }
    }

    // 使用 `context` 值并行运行 loader
    let matchesToLoad = matches.filter((m) =>
      m.shouldCallHandler(),
    );
    let results = await Promise.all(
      matchesToLoad.map((match, i) =>
        match.resolve((handler) => {
          // 你传递给 `handler` 的任何内容将作为第二个参数传递给你的 loader/action
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

### 自定义处理器

你甚至可能不想在路由级别定义 [`loader`](../../start/data/route-object#loader) 实现。也许你只想确定路由并为所有数据发出单个 GraphQL 请求。你可以通过设置 `route.loader=true` 使其具备"有 loader"的资格，然后在 `route.handle` 上存储 GQL 片段来实现：

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
  async dataStrategy({ matches, params, request }) {
    const matchesToLoad = matches.filter((m) =>
      m.shouldCallHandler(),
    );
    // 将路由片段组合成单个 GQL 载荷
    let gql = getFragmentsFromRouteHandles(matchesToLoad);
    let data = await fetchGql(gql);
    // 将结果解析回各个路由级别的 `DataStrategyResult`，按 `routeId` 索引
    let results = parseResultsFromGql(matchesToLoad, data);
    return results;
  },
});
```

注意在这个场景中我们实际上从未调用 `match.resolve()`，因为我们不想调用路由上定义的处理器。我们改为发出单个 GQL 调用，并将结果数据拆分回 `results` 中的正确路由。

[loader]: ../start/data/route-object#loader
[action]: ../start/data/route-object#action
[data-strategy]: ../api/data-routers/createBrowserRouter#optsdatastrategy
