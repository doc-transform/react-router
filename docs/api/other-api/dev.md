---
title: "@react-router/dev (CLI)"
---

# React Router CLI

React Router CLI 来自 `@react-router/dev` 包。请确保它在你的 `package.json` 的 `devDependencies` 中，这样不会被部署到服务器。

要获取完整的可用命令和选项列表，运行：

```shellscript nonumber
npx @react-router/dev -h
```

## `react-router build`

使用 [Vite][vite] 为生产环境构建你的应用。此命令会将 `process.env.NODE_ENV` 设为 `production` 并压缩输出以便部署。

```shellscript nonumber
react-router build
```

| 选项                  | 描述                             | 类型                                                | 默认值      |
| --------------------- | -------------------------------- | --------------------------------------------------- | ----------- |
| `--assetsInlineLimit` | 静态资源 base64 内联阈值（字节） | `number`                                            | `4096`      |
| `--clearScreen`       | 允许/禁用日志时清屏              | `boolean`                                           |             |
| `--config`, `-c`      | 使用指定的配置文件               | `string`                                            |             |
| `--emptyOutDir`       | 当 outDir 在 root 外部时强制清空 | `boolean`                                           |             |
| `--logLevel`, `-l`    | 使用指定的日志级别               | `"info" \| "warn" \| "error" \| "silent" \| string` |             |
| `--minify`            | 启用/禁用压缩，或指定压缩器      | `boolean \| "terser" \| "esbuild"`                  | `"esbuild"` |
| `--mode`, `-m`        | 设置环境模式                     | `string`                                            |             |
| `--profile`           | 启动内置 Node.js 检查器          |                                                     |             |
| `--sourcemapClient`   | 输出客户端构建的 source map      | `boolean \| "inline" \| "hidden"`                   | `false`     |
| `--sourcemapServer`   | 输出服务端构建的 source map      | `boolean \| "inline" \| "hidden"`                   | `false`     |

## `react-router dev`

使用 HMR 和热数据重新验证（HDR）在开发模式下运行你的应用，基于 [Vite][vite] 驱动。

```shellscript nonumber
react-router dev
```

<docs-info>

什么是"热数据重新验证"？

与 HMR 类似，HDR 是一种无需刷新页面即可热更新应用的方式，让你可以在应用状态保持不变的同时应用编辑。HMR 处理客户端代码更新，如组件、标记或样式的变更。同样，HDR 处理服务端代码更新。

这意味着每当你对当前页面（或当前页面依赖的任何代码）进行更改时，React Router 都会从你的 [loader][loaders] 重新获取数据。这样你的应用就*始终*与最新的代码变更保持同步，无论是客户端还是服务端。

</docs-info>

| 选项               | 描述                         | 类型                                                | 默认值 |
| ------------------ | ---------------------------- | --------------------------------------------------- | ------ |
| `--clearScreen`    | 允许/禁用日志时清屏          | `boolean`                                           |        |
| `--config`, `-c`   | 使用指定的配置文件           | `string`                                            |        |
| `--cors`           | 启用 CORS                    | `boolean`                                           |        |
| `--force`          | 强制优化器忽略缓存并重新打包 | `boolean`                                           |        |
| `--host`           | 指定主机名                   | `string`                                            |        |
| `--logLevel`, `-l` | 使用指定的日志级别           | `"info" \| "warn" \| "error" \| "silent" \| string` |        |
| `--mode`, `-m`     | 设置环境模式                 | `string`                                            |        |
| `--open`           | 启动时打开浏览器             | `boolean \| string`                                 |        |
| `--port`           | 指定端口                     | `number`                                            |        |
| `--profile`        | 启动内置 Node.js 检查器      |                                                     |        |
| `--strictPort`     | 指定端口已被占用时退出       | `boolean`                                           |        |

## `react-router reveal`

React Router 默认处理应用的入口文件。

如果你想控制这些入口文件，可以运行 `npx react-router reveal` 在你的 `app` 目录中生成 [`entry.client.tsx`][entry-client] 和 [`entry.server.tsx`][entry-server] 文件。当这些文件存在时，React Router 将使用它们而非默认值。

```shellscript nonumber
npx react-router reveal
```

| 选项              | 描述                   | 类型      | 默认值  |
| ----------------- | ---------------------- | --------- | ------- |
| `--config`, `-c`  | 使用指定的配置文件     | `string`  |         |
| `--mode`, `-m`    | 设置环境模式           | `string`  |         |
| `--no-typescript` | 生成纯 JavaScript 文件 | `boolean` | `false` |
| `--typescript`    | 生成 TypeScript 文件   | `boolean` | `true`  |

## `react-router routes`

在终端中打印应用的路由。

```shellscript nonumber
react-router routes
```

路由树默认以 JSX 格式显示。你也可以使用 `--json` 选项以 JSON 格式获取路由。

```shellscript nonumber
react-router routes --json
```

| 选项             | 描述                 | 类型      | 默认值  |
| ---------------- | -------------------- | --------- | ------- |
| `--config`, `-c` | 使用指定的配置文件   | `string`  |         |
| `--json`         | 以 JSON 格式输出路由 | `boolean` | `false` |
| `--mode`, `-m`   | 设置环境模式         | `string`  |         |

## `react-router typegen`

为你的路由生成 TypeScript 类型。这在开发时会自动发生，但你可以在需要时手动运行，例如在 CI 中运行 `tsc` 之前生成类型。详情请参阅[类型安全][type-safety]。

```shellscript nonumber
react-router typegen
```

| 选项             | 描述               | 类型      | 默认值  |
| ---------------- | ------------------ | --------- | ------- |
| `--config`, `-c` | 使用指定的配置文件 | `string`  |         |
| `--mode`, `-m`   | 设置环境模式       | `string`  |         |
| `--watch`        | 监听变更           | `boolean` | `false` |

[loaders]: ../../start/framework/data-loading
[vite]: https://vite.dev
[entry-server]: ../framework-conventions/entry.server.tsx
[entry-client]: ../framework-conventions/entry.client.tsx
[type-safety]: ../../explanation/type-safety
