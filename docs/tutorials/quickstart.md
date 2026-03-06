---
title: 快速开始
order: 1
---

# 快速开始

[MODES: framework]

<br />
<br />

本指南将带你以最快的速度了解运行 React Router 应用所需的基本配置。虽然有许多使用不同运行时、部署目标和数据库的起手模板，但我们将从零开始创建一个最基础的项目。

## 安装

如果你更喜欢初始化一个开箱即用的 React Router 项目，可以使用 `create-react-router` CLI 配合我们的任何一个[模板][templates]来开始：

```shellscript nonumber
npx create-react-router@latest
```

不过，本指南将解释 CLI 为你的项目设置的所有内容。除了使用 CLI，你也可以按照以下步骤操作。如果你刚开始使用 React Router，我们建议跟着本指南走，以了解组成 React Router 应用的各个部分。

```shellscript nonumber
mkdir my-react-router-app
cd my-react-router-app
npm init -y

# 安装运行时依赖
npm i react-router @react-router/node @react-router/serve isbot react react-dom

# 安装开发依赖
npm i -D @react-router/dev vite
```

## Vite 配置

```shellscript nonumber
touch vite.config.js
```

由于 React Router 使用 [Vite]，你需要提供一个包含 React Router Vite 插件的 [Vite 配置][vite-config]。以下是你需要的基本配置：

```js filename=vite.config.js
import { reactRouter } from "@react-router/dev/vite";
import { defineConfig } from "vite";

export default defineConfig({
  plugins: [reactRouter()],
});
```

## 根路由

```shellscript nonumber
mkdir app
touch app/root.jsx
```

`app/root.jsx` 就是我们所说的"根路由"。它是整个应用的根布局。以下是任何项目都需要的基本元素集：

```jsx filename=app/root.jsx
import { Outlet, Scripts } from "react-router";

export default function App() {
  return (
    <html>
      <head>
        <link
          rel="icon"
          href="data:image/x-icon;base64,AA"
        />
      </head>
      <body>
        <h1>Hello world!</h1>
        <Outlet />
        <Scripts />
      </body>
    </html>
  );
}
```

## 其他路由

```shellscript nonumber
touch app/routes.js
```

`app/routes.js` 是你定义路由的地方。本指南专注于最基本的配置来运行 React Router 应用，所以我们不需要定义任何路由，只需导出一个空数组：

```js filename=app/routes.js
export default [];
```

`routes.js` 文件是构建 React Router 应用所必需的；如果你在使用 React Router，我们假设你最终都会用到路由功能。你可以在[路由][routing]指南中了解更多关于定义路由的内容。

## 构建和运行

首先，你需要在 `package.json` 中指定 type 为 `module`，以满足 `react-router` 和未来版本 Vite 的 ES 模块要求。

```shellscript nonumber
npm pkg set type="module"
```

接下来为生产环境构建应用：

```shellscript nonumber
npx react-router build
```

现在你应该能看到一个 `build` 文件夹，包含一个 `server` 文件夹（应用的服务端版本）和一个 `client` 文件夹（浏览器版本），其中包含一些构建产物。（这些都是[可配置的][react-router-config]。）

👉 **使用 `react-router-serve` 运行应用**

现在你可以使用 `react-router-serve` 运行应用：

```shellscript nonumber
npx react-router-serve build/server/index.js
```

你应该能够打开 [http://localhost:3000][http-localhost-3000] 看到 "hello world" 页面。

除了 `node_modules` 中大量的代码外，我们的 React Router 应用只有四个文件：

```
├── app/
│   ├── root.jsx
│   └── routes.js
├── package.json
└── vite.config.js
```

## 自带服务器

`react-router build` 创建的 `build/server` 目录只是一个模块，你可以在任何服务器中运行它，如 Express、Cloudflare Workers、Netlify、Vercel、Fastly、AWS、Deno、Azure、Fastify、Firebase 等等……任何地方都可以。

<docs-info>

你也可以将 React Router 用作不带服务器的单页应用。更多信息请参阅[单页应用][spa]指南。

</docs-info>

如果你不想自己搭建服务器，可以使用 `react-router-serve`。它是由 React Router 维护者维护的一个基于 `express` 的简单服务器。但是，React Router 的设计初衷就是可以在 _任何_ JavaScript 环境中运行，让你掌控自己的技术栈。预计许多（甚至大多数）生产应用都会使用自己的服务器。

为了好玩，让我们停止使用 `react-router-serve`，改用 `express`。

👉 **安装 Express、React Router Express 适配器和 [cross-env]（用于在生产模式下运行）**

```shellscript nonumber
npm i express @react-router/express cross-env

# 不再使用这个了
npm uninstall @react-router/serve
```

👉 **创建 Express 服务器**

```shellscript nonumber
touch server.js
```

```js filename=server.js
import { createRequestHandler } from "@react-router/express";
import express from "express";

const app = express();
app.use(express.static("build/client"));

// 注意你的应用"只是一个请求处理器"
app.use(
  createRequestHandler({
    // `react-router build` 的结果"只是一个模块"
    build: await import("./build/server/index.js"),
  }),
);

app.listen(3000, () => {
  console.log("App listening on http://localhost:3000");
});
```

👉 **使用 `express` 运行应用**

```shellscript nonumber
node server.js
```

现在你拥有了自己的服务器，可以使用服务器自带的任何工具来调试应用。例如，你可以使用 [Node.js inspect 标志][inspect]通过 Chrome DevTools 检查应用：

```shellscript nonumber
node --inspect server.js
```

## 开发工作流

你不需要反复停止、重建和启动服务器，而是可以使用 [Vite 中间件模式][vite-middleware]在开发环境中运行 React Router。这通过 React Refresh（热模块替换）和 React Router 热数据重新验证为你的应用变更提供即时反馈。

首先，为方便起见，在 `package.json` 中添加 `dev` 和 `start` 命令，分别用于在开发和生产模式下运行服务器：

👉 **在 `package.json` 中添加 "scripts" 条目**

```jsonc filename=package.json lines=[2-4] nocopy
{
  "scripts": {
    "dev": "node ./server.js",
    "start": "cross-env NODE_ENV=production node ./server.js",
  },
  // ...
}
```

👉 **将 Vite 开发中间件添加到服务器**

当 `process.env.NODE_ENV` 设为 `"production"` 时不会应用 Vite 中间件，此时你仍然会像之前一样运行常规的构建产物。

```js filename=server.js lines=[6,13-28]
import { createRequestHandler } from "@react-router/express";
import express from "express";

const app = express();

if (process.env.NODE_ENV === "production") {
  app.use(express.static("build/client"));
  app.use(
    createRequestHandler({
      build: await import("./build/server/index.js"),
    }),
  );
} else {
  const viteDevServer = await import("vite").then((vite) =>
    vite.createServer({
      server: { middlewareMode: true },
    }),
  );
  app.use(viteDevServer.middlewares);
  app.use(
    createRequestHandler({
      build: () =>
        viteDevServer.ssrLoadModule(
          "virtual:react-router/server-build",
        ),
    }),
  );
}

app.listen(3000, () => {
  console.log(`Server is running on http://localhost:3000`);
});
```

👉 **启动开发服务器**

```shellscript nonumber
npm run dev
```

现在你可以获得即时反馈来开发应用了。试试修改 `root.jsx` 中的文字，看看变更是否立即呈现！

## 控制服务端和浏览器端入口

React Router 使用了一些默认的魔法文件，大多数应用不需要修改，但如果你想自定义 React Router 的服务端和浏览器端入口，可以运行 `react-router reveal`，它们会被导出到你的项目中。

```shellscript nonumber
npx react-router reveal
```

```
Entry file entry.client created at app/entry.client.tsx.
Entry file entry.server created at app/entry.server.tsx.
```

## 总结

恭喜，你可以把 React Router 加到简历上了！总结一下，我们学到了：

- React Router 框架模式将你的应用编译为两部分：
  - 一个请求处理器，添加到你自己的 JavaScript 服务器中
  - 一堆放在 public 目录中供浏览器使用的静态资源
- 你可以通过适配器自带服务器来部署到任何地方
- 你可以搭建一个内置 HMR 的开发工作流

总的来说，React Router 有点"暴露内部机制"。它需要几分钟的样板配置，但这样你就拥有了自己的技术栈。

接下来做什么？

- [通讯录教程][address-book-tutorial]

[templates]: ../start/framework/deploying#templates
[spa]: ../how-to/spa
[inspect]: https://nodejs.org/en/docs/guides/debugging-getting-started/
[vite-config]: https://vite.dev/config
[routing]: ../start/framework/routing
[http-localhost-3000]: http://localhost:3000
[vite]: https://vitejs.dev
[react-router-config]: https://api.reactrouter.com/v7/types/_react-router_dev.config.Config.html
[vite-middleware]: https://vitejs.dev/guide/ssr#setting-up-the-dev-server
[cross-env]: https://www.npmjs.com/package/cross-env
[address-book-tutorial]: ./address-book
