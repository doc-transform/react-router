---
title: "@react-router/serve"
---

# React Router App Server

React Router 的设计理念是让你拥有自己的服务器，但如果你不想自己搭建，可以使用 React Router App Server 作为替代。它是一个基于 [Express][express] 构建的、可用于生产环境的基础 Node.js 服务器。

按照设计，我们不提供自定义 React Router App Server 的选项，因为如果你需要自定义底层的 `express` 服务器，我们更希望你完全管理服务器，而不是创建一个抽象层来处理你可能需要的所有自定义。如果你发现需要自定义，可以[迁移到 `@react-router/express` 适配器][migrate-to-express]。

你可以在 [packages/react-router-serve/cli.ts][rr-serve-code] 中查看底层的 `express` 服务器配置。默认情况下，它使用以下 Express 中间件（请参阅它们的文档了解默认行为）：

- [`compression`][compression]
- [`express.static`][express-static]（以及 [`serve-static`][serve-static]）
- [`morgan`][morgan]

## `HOST` 环境变量

你可以通过 `process.env.HOST` 配置 Express 应用的主机名，该值将在启动服务器时传递给内部的 [`app.listen`][express-listen] 方法。

```shellscript nonumber
HOST=127.0.0.1 npx react-router-serve build/index.js
```

```shellscript nonumber
react-router-serve <server-build-path>
# 例如
react-router-serve build/index.js
```

## `PORT` 环境变量

你可以通过环境变量更改服务器端口。

```shellscript nonumber
PORT=4000 npx react-router-serve build/index.js
```

## 开发环境

服务器会根据 `process.env.NODE_ENV` 的值以开发模式或生产模式启动。

`server-build-path` 需要指向 [`react-router.config.ts`][rr-config] 中定义的 `serverBuildPath`。

由于只有构建产物（`build/`、`public/build/`）需要部署到生产环境，`react-router.config.ts` 在生产环境中不一定可用，因此你需要通过此选项告诉 React Router 你的服务器构建在哪里。

在开发环境中，`react-router-serve` 会在每次请求时清除 `require` 缓存以确保运行最新代码。这对你的代码有一些需要注意的影响：

- 模块作用域中的任何值都会被"重置"

  ```tsx lines=[1-3]
  // 这将在每次请求时重置，因为模块缓存被清除，
  // 此模块将被重新加载
  const cache = new Map();

  export async function loader({
    params,
  }: Route.LoaderArgs) {
    if (cache.has(params.foo)) {
      return cache.get(params.foo);
    }

    const record = await fakeDb.stuff.find(params.foo);
    cache.set(params.foo, record);
    return record;
  }
  ```

  如果你需要在开发中保留缓存的变通方案，可以在服务器中设置单例。

- 任何**模块副作用**将保持不变！这可能会导致问题，但无论如何都应该避免。

  ```tsx lines=[1-4]
  // 这在模块被导入的那一刻就开始运行
  setInterval(() => {
    console.log(Date.now());
  }, 1000);

  export async function loader() {
    // ...
  }
  ```

  如果你需要编写包含此类模块副作用的代码，应该搭建自己的 [@react-router/express][rr-express] 服务器，并使用开发工具如 [`pm2-dev`][pm2-dev] 或 [`nodemon`][nodemon] 在文件变更时重启服务器。

在生产环境中，这种情况不会发生。服务器启动后就是最终状态。

[rr-express]: ./adapter#react-routerexpress
[express-listen]: https://expressjs.com/en/api.html#app.listen
[rr-config]: ../framework-conventions/react-router.config.ts
[rr-serve-code]: https://github.com/remix-run/react-router/blob/main/packages/react-router-serve/cli.ts
[compression]: https://expressjs.com/en/resources/middleware/compression.html
[express-static]: https://expressjs.com/en/4x/api.html#express.static
[serve-static]: https://expressjs.com/en/resources/middleware/serve-static.html
[morgan]: https://expressjs.com/en/resources/middleware/morgan.html
[express]: https://expressjs.com
[migrate-to-express]: ./adapter#migrating-from-the-react-router-app-server
[pm2-dev]: https://npm.im/pm2-dev
[nodemon]: https://npm.im/nodemon
