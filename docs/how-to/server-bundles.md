---
title: 服务端包
---

# 服务端包

[MODES: framework]

<br/>
<br/>

<docs-warning>这是一个为托管提供商集成设计的高级功能。将应用编译为多个服务端包时，需要在应用前面有一个自定义路由层，将请求定向到正确的包。</docs-warning>

React Router 通常将你的服务端代码构建为单个包，该包导出一个请求处理器函数。然而，在某些场景下你可能想将路由树拆分为多个服务端包，每个包为一部分路由导出一个请求处理器函数。为了提供这种灵活性，[`react-router.config.ts`][react-router-config] 支持 `serverBundles` 选项，这是一个将路由分配到不同服务端包的函数。

[`serverBundles` 函数][server-bundles-function]为路由树中的每个路由调用（不可寻址的路由除外，例如无路径的布局路由），并返回你想将该路由分配到的服务端包 ID。这些包 ID 将用作服务端构建目录中的目录名。

对于每个路由，此函数接收一个包含通向该路由（含该路由本身）的路由数组，称为路由 `branch`。这允许你为路由树的不同部分创建服务端包。例如，你可以用它为特定布局路由内的所有路由创建一个单独的服务端包：

```ts filename=react-router.config.ts lines=[5-13]
import type { Config } from "@react-router/dev/config";

export default {
  // ...
  serverBundles: ({ branch }) => {
    const isAuthenticatedRoute = branch.some((route) =>
      route.id.split("/").includes("_authenticated"),
    );

    return isAuthenticatedRoute
      ? "authenticated"
      : "unauthenticated";
  },
} satisfies Config;
```

`branch` 数组中每个 `route` 包含以下属性：

- `id` — 该路由的唯一 ID，命名方式类似其 `file`，但相对于 app 目录且不含扩展名，例如 `app/routes/gists.$username.tsx` 的 `id` 为 `routes/gists.$username`
- `path` — 该路由用于匹配 URL 路径名的路径
- `file` — 该路由入口文件的绝对路径
- `index` — 该路由是否为索引路由

## 构建清单

构建完成后，React Router 会调用 `buildEnd` 钩子，传递一个 `buildManifest` 对象。如果你需要检查构建清单以确定如何将请求路由到正确的服务端包，这很有用。

```ts filename=react-router.config.ts lines=[5-7]
import type { Config } from "@react-router/dev/config";

export default {
  // ...
  buildEnd: async ({ buildManifest }) => {
    // ...
  },
} satisfies Config;
```

使用服务端包时，构建清单包含以下属性：

- `serverBundles` — 将包 ID 映射到包的 `id` 和 `file` 的对象
- `routeIdToServerBundleId` — 将路由 ID 映射到其服务端包 ID 的对象
- `routes` — 将路由 ID 映射到路由元数据的路由清单。这可用于驱动 React Router 请求处理器前面的自定义路由层

[react-router-config]: https://api.reactrouter.com/v7/types/_react-router_dev.config.Config.html
[server-bundles-function]: https://api.reactrouter.com/v7/types/_react-router_dev.config.ServerBundlesFunction.html
