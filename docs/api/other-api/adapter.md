---
title: "@react-router/{adapter}"
---

# 服务端适配器

## 官方适配器

惯用的 React Router 应用通常可以部署到任何地方，因为 React Router 通过适配器将服务器的请求/响应适配为 [Web Fetch API][web-fetch-api]。我们维护了以下几个适配器：

- `@react-router/architect`
- `@react-router/cloudflare`
- `@react-router/express`

这些适配器被导入到你的服务器入口文件中，而非在 React Router 应用本身中使用。

如果你使用 `npx create-react-router@latest` 初始化应用时，选择了内置 [React Router App Server][rr-serve]（`@react-router/serve`）以外的选项，你会看到一个 `server/index.js` 文件，它导入并使用了其中一个适配器。

<docs-info>如果你使用的是内置的 React Router App Server，则无需与此 API 交互。</docs-info>

每个适配器具有相同的 API。未来我们可能会提供针对你所部署平台的特定辅助函数。

## `@react-router/express`

[参考文档 ↗](https://api.reactrouter.com/v7/modules/_react-router_express.html)

以下是使用 [Express][express] 的示例：

```ts lines=[1-3,11-22]
const {
  createRequestHandler,
} = require("@react-router/express");
const express = require("express");

const app = express();

// 需要处理所有 HTTP 方法（GET、POST 等）
app.all(
  "*",
  createRequestHandler({
    // `react-router build` 和 `react-router dev` 将文件输出到 build 目录，
    // 你需要将该 build 传递给请求处理器
    build: require("./build"),

    // 在这里返回任何你想要的内容，它将作为 loader 和 action 中的 `context` 使用。
    // 这是你在服务器和 React Router 之间架起桥梁的地方
    getLoadContext(req, res) {
      return {};
    },
  }),
);
```

### 从 React Router App Server 迁移

如果你的应用最初使用 [React Router App Server][rr-serve] 启动，但发现需要控制 Express 服务器并进行自定义，从 `@react-router/serve` 迁移应该相当简单。

你可以参考 [Express 模板][express-template]作为参考，以下是主要的修改步骤：

**1. 更新依赖**

```shellscript nonumber
npm uninstall @react-router/serve
npm install @react-router/express compression express morgan cross-env
npm install --save-dev @types/express @types/express-serve-static-core @types/morgan
```

**2. 添加服务器**

在 `server/app.ts` 中创建你的 React Router Express 服务器：

```ts filename=server/app.ts
import "react-router";
import { createRequestHandler } from "@react-router/express";
import express from "express";

export const app = express();

app.use(
  createRequestHandler({
    build: () =>
      import("virtual:react-router/server-build"),
  }),
);
```

将 [`server.js`][express-template-server-js] 复制到你的应用中。这是我们推荐的样板配置，允许相同的服务器代码同时运行应用的开发和生产构建。这里使用两个独立的文件，以便主要的 Express 服务器代码可以用 TypeScript（`server/app.ts`）编写，由 React Router 编译到服务器构建中，然后通过 `node server.js` 执行。

**3. 更新 `vite.config.ts` 以编译服务器**

```tsx filename=vite.config.ts lines=[6-10]
import { reactRouter } from "@react-router/dev/vite";
import { defineConfig } from "vite";
import tsconfigPaths from "vite-tsconfig-paths";

export default defineConfig(({ isSsrBuild }) => ({
  build: {
    rollupOptions: isSsrBuild
      ? { input: "./server/app.ts" }
      : undefined,
  },
  plugins: [reactRouter(), tsconfigPaths()],
}));
```

**4. 更新 `package.json` 脚本**

更新 `dev` 和 `start` 脚本以使用新的 Express 服务器：

```json filename=package.json
{
  // ...
  "scripts": {
    "dev": "cross-env NODE_ENV=development node server.js",
    "start": "node server.js"
    // ...
  }
  // ...
}
```

## `@react-router/cloudflare`

[参考文档 ↗](https://api.reactrouter.com/v7/modules/_react-router_cloudflare.html)

以下是使用 Cloudflare 的示例：

```ts
import { createRequestHandler } from "react-router";

declare module "react-router" {
  export interface AppLoadContext {
    cloudflare: {
      env: Env;
      ctx: ExecutionContext;
    };
  }
}

const requestHandler = createRequestHandler(
  () => import("virtual:react-router/server-build"),
  import.meta.env.MODE,
);

export default {
  async fetch(request, env, ctx) {
    return requestHandler(request, {
      cloudflare: { env, ctx },
    });
  },
} satisfies ExportedHandler<Env>;
```

## `@react-router/node`

虽然不像上面那样是直接的"适配器"，但此包包含用于 Node 环境适配器的工具函数。

[参考文档 ↗](https://api.reactrouter.com/v7/modules/_react-router_node.html)

### Node 版本支持

React Router 在任何时间点都正式支持 **Active** 和 **Maintenance** 状态的 [Node LTS 版本][node-releases]。停止支持已终止生命周期的 Node 版本会在 React Router 的次要版本中完成。

[express]: https://expressjs.com
[node-releases]: https://nodejs.org/en/about/previous-releases
[web-fetch-api]: https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API
[rr-serve]: ./serve
[express-template]: https://github.com/remix-run/react-router-templates/tree/main/node-custom-server
[express-template-server-js]: https://github.com/remix-run/react-router-templates/blob/main/node-custom-server/server.js
