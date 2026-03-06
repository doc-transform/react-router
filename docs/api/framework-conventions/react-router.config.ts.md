---
title: react-router.config.ts
order: 3
---

# react-router.config.ts

[MODES: framework]

## 概述

<docs-info>
此文件是可选的
</docs-info>

[参考文档 ↗](https://api.reactrouter.com/v7/types/_react-router_dev.config.Config.html)

React Router 框架配置文件，允许你自定义 React Router 应用的各个方面，如服务端渲染、目录位置和构建设置。

```tsx filename=react-router.config.ts
import type { Config } from "@react-router/dev/config";

export default {
  appDirectory: "app",
  buildDirectory: "build",
  ssr: true,
  prerender: ["/", "/about"],
} satisfies Config;
```

## 选项

### `allowedActionOrigins`

允许向 UI 路由提交 action 的来源主机数组（不适用于资源路由）。支持 micromatch glob 模式（`*` 匹配一个段，`**` 匹配多个段）。

```tsx filename=react-router.config.ts
export default {
  allowedActionOrigins: [
    "example.com",
    "*.example.com", // sub.example.com
    "**.example.com", // sub.domain.example.com
  ],
} satisfies Config;
```

如果你需要在运行时设置此值，可以在自定义服务器的 server build 上设置。例如使用 `express` 时：

```ts
import express from "express";
import { createRequestHandler } from "@react-router/express";
import type { ServerBuild } from "react-router";

export const app = express();

async function getBuild() {
  let build: ServerBuild = await import(
    "virtual:react-router/server-build"
  );
  return {
    ...build,
    allowedActionOrigins:
      process.env.NODE_ENV === "development"
        ? undefined
        : ["staging.example.com", "www.example.com"],
  };
}

app.use(createRequestHandler({ build: getBuild }));
```

### `appDirectory`

`app` 目录的路径，相对于根目录。默认为 `"app"`。

```tsx filename=react-router.config.ts
export default {
  appDirectory: "src",
} satisfies Config;
```

### `basename`

React Router 应用的基础路径。默认为 `"/"`。

```tsx filename=react-router.config.ts
export default {
  basename: "/my-app",
} satisfies Config;
```

### `buildDirectory`

构建目录的路径，相对于项目。默认为 `"build"`。

```tsx filename=react-router.config.ts
export default {
  buildDirectory: "dist",
} satisfies Config;
```

### `buildEnd`

完整的 React Router 构建完成后调用的函数。

```tsx filename=react-router.config.ts
export default {
  buildEnd: async ({
    buildManifest,
    reactRouterConfig,
    viteConfig,
  }) => {
    // 自定义构建逻辑
    console.log("Build completed!");
  },
} satisfies Config;
```

### `future`

已启用的 future flag，用于选择加入即将推出的功能。

详情请参阅 [Future Flag][future-flags]。

```tsx filename=react-router.config.ts
export default {
  future: {
    // 在这里启用 future flag
  },
} satisfies Config;
```

### `prerender`

构建时要预渲染为 HTML 文件的 URL 数组。也可以是返回数组的函数，用于动态生成 URL。

详情请参阅[预渲染][pre-rendering]。

```tsx filename=react-router.config.ts
export default {
  // 静态数组
  prerender: ["/", "/about", "/contact"],

  // 或动态函数
  prerender: async ({ getStaticPaths }) => {
    const paths = await getStaticPaths();
    return ["/", ...paths];
  },
} satisfies Config;
```

### `presets`

React Router 插件配置预设数组，便于与其他平台和工具集成。

详情请参阅[预设][presets]。

```tsx filename=react-router.config.ts
export default {
  presets: [
    // 在这里添加预设
  ],
} satisfies Config;
```

### `routeDiscovery`

配置客户端如何发现和加载路由。默认为 `mode: "lazy"`，`manifestPath: "/__manifest"`。

**选项：**

- `mode: "lazy"` - 用户导航时才发现路由（默认）
  - `manifestPath` - 使用 `lazy` 模式时 manifest 请求的自定义路径
- `mode: "initial"` - 所有路由都包含在初始 manifest 中

```tsx filename=react-router.config.ts
export default {
  // 启用延迟路由发现（默认）
  routeDiscovery: {
    mode: "lazy",
    manifestPath: "/__manifest",
  },

  // 使用自定义 manifest 路径
  routeDiscovery: {
    mode: "lazy",
    manifestPath: "/custom-manifest",
  },

  // 禁用延迟发现，初始包含所有路由
  routeDiscovery: { mode: "initial" },
} satisfies Config;
```

详情请参阅[延迟路由发现][lazy-route-discovery]。

### `serverBuildFile`

服务端构建输出的文件名。文件应以 `.js` 扩展名结尾，并部署到你的服务器。默认为 `"index.js"`。

```tsx filename=react-router.config.ts
export default {
  serverBuildFile: "server.js",
} satisfies Config;
```

### `serverBundles`

将路由分配到不同服务端包的函数。此函数应返回一个服务端包 ID，该 ID 将用作服务端构建目录中的包目录名称。

详情请参阅[服务端包][server-bundles]。

```tsx filename=react-router.config.ts
export default {
  serverBundles: ({ branch }) => {
    // 根据路由分支返回包 ID
    return branch.some((route) => route.id === "admin")
      ? "admin"
      : "main";
  },
} satisfies Config;
```

### `serverModuleFormat`

服务端构建的输出格式。默认为 `"esm"`。

```tsx filename=react-router.config.ts
export default {
  serverModuleFormat: "cjs", // 或 "esm"
} satisfies Config;
```

### `ssr`

如果为 `true`，React Router 将对你的应用进行服务端渲染。

如果为 `false`，React Router 将预渲染你的应用并将其保存为带有静态资源的 `index.html` 文件，这样你的应用就可以作为 SPA 部署而无需服务端渲染。详情请参阅["SPA 模式"][spa-mode]。

默认为 `true`。

```tsx filename=react-router.config.ts
export default {
  ssr: false, // 禁用服务端渲染
} satisfies Config;
```

[future-flags]: ../../upgrading/future
[presets]: ../../how-to/presets
[server-bundles]: ../../how-to/server-bundles
[pre-rendering]: ../../how-to/pre-rendering
[spa-mode]: ../../how-to/spa
[lazy-route-discovery]: ../../explanation/lazy-route-discovery
