---
title: Future Flag 与废弃项
order: 1
---

# Future Flag 与废弃项

本指南将引导你在 React Router 应用中采用 future flag。按照这个策略，你可以在最少的改动下升级到 React Router 的下一个主版本。要了解更多关于 future flag 的信息，请参阅 [API 开发策略](../community/api-development-strategy)。

我们强烈建议你在每一步完成后提交代码并部署，而不是一次性完成所有更改。大多数 flag 可以按任意顺序采用，特殊情况会在下方注明。

## 更新到最新的 v7.x

首先更新到 v7.x 的最新小版本，以获取最新的 future flag。升级过程中你可能会看到一些废弃警告，我们将在下面逐一介绍。

👉 更新到最新的 v7

```sh
npm install react-router@7 @react-router/{dev,node,etc.}@7
```

## `future.v8_middleware`

[MODES: framework]

<br/>
<br/>

**背景**

中间件允许你在匹配路径的 [`Response`][Response] 生成前后运行代码。这使得身份认证、日志记录、错误处理和数据预处理等常见模式可以被复用。更多信息请参阅[文档](../how-to/middleware)。

👉 **启用该 Flag**

```ts filename=react-router.config.ts
import type { Config } from "@react-router/dev/config";

export default {
  future: {
    v8_middleware: true,
  },
} satisfies Config;
```

**更新你的代码**

如果你使用的是 `react-router-serve`，则不需要对代码做任何更新。

只有当你在 `loader` 和 `action` 函数中使用了 `context` 参数时才需要更新代码。这仅适用于拥有自定义服务器且使用了 `getLoadContext` 函数的情况。请参阅中间件 [`getLoadContext` 变更](../how-to/middleware#changes-to-getloadcontextapploadcontext)的文档以及[迁移到新 API](../how-to/middleware#migration-from-apploadcontext) 的说明。

## `future.v8_splitRouteModules`

[MODES: framework]

<br/>
<br/>

**背景**

此功能支持将客户端路由导出（`clientLoader`、`clientAction`、`clientMiddleware`、`HydrateFallback`）拆分为可独立加载的单独代码块。这允许这些导出在组件代码仍在下载时就被获取和执行，从而提升客户端数据加载的性能。

可以设置为 `true` 启用该行为，或设置为 `"enforce"` 要求所有路由都可拆分（对于因共享代码而无法拆分的路由，构建将会失败）。

👉 **启用该 Flag**

```ts filename=react-router.config.ts
import type { Config } from "@react-router/dev/config";

export default {
  future: {
    v8_splitRouteModules: true,
  },
} satisfies Config;
```

**更新你的代码**

无需任何代码改动。这是一项优化功能，启用后自动生效。

## `future.v8_viteEnvironmentApi`

[MODES: framework]

<br/>
<br/>

**背景**

此功能启用对实验性 Vite Environment API 的支持，它提供了更灵活和强大的 Vite 环境配置方式。仅在使用 Vite 6+ 时可用。

👉 **启用该 Flag**

```ts filename=react-router.config.ts
import type { Config } from "@react-router/dev/config";

export default {
  future: {
    v8_viteEnvironmentApi: true,
  },
} satisfies Config;
```

**更新你的代码**

除非你有需要为 [Environment API][vite-environment] 更新的自定义 Vite 配置，否则不需要任何代码改动。大多数用户无需做任何更改。

[Response]: https://developer.mozilla.org/en-US/docs/Web/API/Response
[vite-environment]: https://vite.dev/guide/api-environment
