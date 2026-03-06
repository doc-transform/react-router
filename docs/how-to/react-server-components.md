---
title: React Server Components
unstable: true
---

# React Server Components

[MODES: framework, data]

<br/>
<br/>

<docs-warning>React Server Components 支持是实验性的，可能在次要/补丁版本中发生破坏性变更。请谨慎使用，并**密切**关注发布说明中的相关变更。</docs-warning>

React Server Components（RSC）通常指 React 19 以来提供的一套架构和 API。

引用自官方文档：

> Server Components 是一种新的组件类型，在打包之前提前渲染，运行在与客户端应用或 SSR 服务器分离的环境中。
>
> <cite>- [React "Server Components" 文档][react-server-components-doc]</cite>

React Router 提供了一套用于与支持 RSC 的打包工具集成的 API，让你可以在 React Router 应用中使用 [Server Components][react-server-components-doc] 和 [Server Functions][react-server-functions-doc]。

如果你不熟悉这些 React 特性，我们建议在使用 React Router 的 RSC API 之前先阅读官方 [Server Components 文档][react-server-components-doc]。

RSC 支持在框架模式和数据模式中都可用。关于这两种模式的概念差异，请参阅["选择模式"][picking-a-mode]。不过请注意，RSC 和非 RSC 模式之间的 API 和功能有所不同，本指南将详细介绍。

## 快速开始

最快的入门方式是使用我们的模板之一。

这些模板已经预配置了 React Router RSC API，开箱即用地提供以下功能：

- Server Component 路由
- 服务端渲染 (SSR)
- Client Components（通过 [`"use client"`][use-client-docs] 指令）
- Server Functions（通过 [`"use server"`][use-server-docs] 指令）

### RSC 框架模式模板

[RSC 框架模式模板][framework-rsc-template]使用不稳定的 React Router RSC Vite 插件以及实验性的 [`@vitejs/plugin-rsc` 插件][vite-plugin-rsc]。

```shellscript
npx create-react-router@latest --template remix-run/react-router-templates/unstable_rsc-framework-mode
```

### RSC 数据模式模板

[Vite RSC 数据模式模板][vite-rsc-template]使用实验性的 Vite `@vitejs/plugin-rsc` 插件。

```shellscript
npx create-react-router@latest --template remix-run/react-router-templates/unstable_rsc-data-mode-vite
```

## RSC 框架模式

RSC 框架模式中的大部分 API 和功能与非 RSC 框架模式相同，因此本指南将重点介绍差异之处。

### 新的 React Router RSC Vite 插件

RSC 框架模式使用与非 RSC 框架模式不同的 Vite 插件，当前导出为 `unstable_reactRouterRSC`。

这个新的 Vite 插件还对实验性的 `@vitejs/plugin-rsc` 插件有对等依赖。注意 `@vitejs/plugin-rsc` 插件应放在 Vite 配置中 React Router RSC 插件之后。

```tsx filename=vite.config.ts
import { defineConfig } from "vite";
import { unstable_reactRouterRSC as reactRouterRSC } from "@react-router/dev/vite";
import rsc from "@vitejs/plugin-rsc";

export default defineConfig({
  plugins: [reactRouterRSC(), rsc()],
});
```

### 构建产物

RSC 框架模式的服务器构建文件（`build/server/index.js`）现在导出一个用于文档/数据请求的 `default` 请求处理函数（`(request: Request) => Promise<Response>`）。

如果需要，你可以使用 [@remix-run/node-fetch-server][node-fetch-server] 的 `createRequestListener` 函数将其转换为[标准 Node.js 请求监听器][node-request-listener]，以配合 Node 内置的 `http.createServer` 函数（或任何支持它的工具，如 [Express][express]）使用。

例如，在 Express 中：

```tsx filename=start.js
import express from "express";
import requestHandler from "./build/server/index.js";
import { createRequestListener } from "@remix-run/node-fetch-server";

const app = express();

app.use(
  "/assets",
  express.static("build/client/assets", {
    immutable: true,
    maxAge: "1y",
  }),
);
app.use(express.static("build/client"));
app.use(createRequestListener(requestHandler));
app.listen(3000);
```

### 从 Loader/Action 返回 React 元素

在 RSC 框架模式中，loader 和 action 现在可以返回 React 元素以及其他数据。这些元素只会在服务器上渲染。

```tsx
import type { Route } from "./+types/route";

export async function loader() {
  return {
    message: "来自服务器的消息！",
    element: <p>来自服务器的元素！</p>,
  };
}

export default function Route({
  loaderData,
}: Route.ComponentProps) {
  return (
    <>
      <h1>{loaderData.message}</h1>
      {loaderData.element}
    </>
  );
}
```

如果你需要在从 loader/action 返回的 React 元素中使用仅客户端的功能（如 [Hooks][hooks]、事件处理器），需要将使用这些功能的组件提取到[客户端模块][use-client-docs]中：

```tsx filename=src/routes/counter/counter.tsx
"use client";

export function Counter() {
  const [count, setCount] = useState(0);
  return (
    <button onClick={() => setCount(count + 1)}>
      计数: {count}
    </button>
  );
}
```

```tsx filename=src/routes/counter/route.tsx
import type { Route } from "./+types/route";
import { Counter } from "./counter";

export async function loader() {
  return {
    message: "来自服务器的消息！",
    element: (
      <>
        <p>来自服务器的元素！</p>
        <Counter />
      </>
    ),
  };
}

export default function Route({
  loaderData,
}: Route.ComponentProps) {
  return (
    <>
      <h1>{loaderData.message}</h1>
      {loaderData.element}
    </>
  );
}
```

### Server Component 路由

如果路由导出 `ServerComponent` 而非通常的 `default` 组件导出，该组件以及其他路由组件（`ErrorBoundary`、`HydrateFallback`、`Layout`）将是 server component 而非通常的 client component。

```tsx
import type { Route } from "./+types/route";
import { Outlet } from "react-router";
import { getMessage } from "./message";

export async function loader() {
  return {
    message: await getMessage(),
  };
}

export function ServerComponent({
  loaderData,
}: Route.ComponentProps) {
  return (
    <>
      <h1>Server Component 路由</h1>
      <p>来自服务器的消息: {loaderData.message}</p>
      <Outlet />
    </>
  );
}
```

如果你需要在服务器优先的路由中使用仅客户端的功能（如 [Hooks][hooks]、事件处理器），需要将使用这些功能的组件提取到[客户端模块][use-client-docs]中：

```tsx filename=src/routes/counter/counter.tsx
"use client";

export function Counter() {
  const [count, setCount] = useState(0);
  return (
    <button onClick={() => setCount(count + 1)}>
      计数: {count}
    </button>
  );
}
```

```tsx filename=src/routes/counter/route.tsx
import { Counter } from "./counter";

export function ServerComponent() {
  return (
    <>
      <h1>计数器</h1>
      <Counter />
    </>
  );
}
```

### `.server`/`.client` 模块

为了避免与 RSC 的 `"use server"` 和 `"use client"` 指令混淆，在使用 RSC 框架模式时不再内置对 [`.server` 模块][server-modules]和 [`.client` 模块][client-modules]的支持。

作为不依赖文件命名约定的替代方案，我们推荐使用 [`@vitejs/plugin-rsc`][vite-plugin-rsc] 提供的 `"server-only"` 和 `"client-only"` 导入。例如，要确保模块永远不会意外包含在客户端构建中，只需在你的服务端模块中以副作用方式从 `"server-only"` 导入：

```ts filename=app/utils/db.ts
import "server-only";

// 模块的其余部分...
```

注意虽然 React 团队创建了官方 npm 包 [`server-only`][server-only-package] 和 [`client-only`][client-only-package]，但不需要安装它们。`@vitejs/plugin-rsc` 内部处理这些导入并提供构建时验证而非运行时错误。

如果你想快速迁移依赖 `.server` 和 `.client` 文件命名约定的现有代码，我们推荐直接使用 [`vite-env-only` 插件][vite-env-only]。例如，要确保 `.server` 模块不会意外包含在客户端构建中：

```tsx filename=vite.config.ts
import { defineConfig } from "vite";
import { denyImports } from "vite-env-only";
import { unstable_reactRouterRSC as reactRouterRSC } from "@react-router/dev/vite";
import rsc from "@vitejs/plugin-rsc";

export default defineConfig({
  plugins: [
    denyImports({
      client: { files: ["**/.server/*", "**/*.server.*"] },
    }),
    reactRouterRSC(),
    rsc(),
  ],
});
```

### MDX 路由支持

使用 `@mdx-js/rollup` v3.1.1+ 时，RSC 框架模式支持 MDX 路由。

注意从 MDX 路由导出的任何组件也必须在 RSC 环境中有效，即不能使用仅客户端的功能如 [Hooks][hooks]。需要使用这些功能的组件应提取到[客户端模块][use-client-docs]中。

### 自定义入口文件

RSC 框架模式支持自定义入口文件，允许你自定义 RSC 服务器、SSR 服务器和客户端入口点的行为。

插件会自动检测 `app` 目录中的自定义入口文件：

- `app/entry.rsc.ts`（或 `.tsx`）- 自定义 RSC 服务器入口
- `app/entry.ssr.ts`（或 `.tsx`）- 自定义 SSR 服务器入口
- `app/entry.client.tsx` - 自定义客户端入口

如果未找到这些文件，React Router 将使用框架提供的默认入口。

#### 基本覆盖模式

你可以创建一个包装或扩展默认行为的自定义入口文件。例如，要为 RSC 入口添加自定义日志记录：

```ts filename=app/entry.rsc.ts
import defaultEntry from "@react-router/dev/config/default-rsc-entries/entry.rsc";
import { RouterContextProvider } from "react-router";

export default {
  fetch(request: Request): Promise<Response> {
    console.log("自定义 RSC 入口处理请求:", request.url);

    const requestContext = new RouterContextProvider();

    return defaultEntry.fetch(request, requestContext);
  },
};

if (import.meta.hot) {
  import.meta.hot.accept();
}
```

同样，你可以自定义 SSR 入口：

```ts filename=app/entry.ssr.ts
import { generateHTML as defaultGenerateHTML } from "@react-router/dev/config/default-rsc-entries/entry.ssr";

export function generateHTML(
  request: Request,
  serverResponse: Response,
): Promise<Response> {
  console.log("自定义 SSR 入口生成 HTML:", request.url);

  return defaultGenerateHTML(request, serverResponse);
}
```

以及客户端入口：

```ts filename=app/entry.client.ts
import "@react-router/dev/config/default-rsc-entries/entry.client";
```

#### 复制默认入口

如果需要更高级的自定义，你可以复制默认入口并根据需要修改。要找到默认入口：

1. 在 IDE 中，对默认入口导入使用"转到定义"（或 Cmd/Ctrl+Click）：

   ```ts
   import defaultEntry from "@react-router/dev/config/default-rsc-entries/entry.rsc";
   ```

2. 将默认入口代码复制到你的自定义文件中

3. 根据需要修改

默认入口位于：

- [`@react-router/dev/config/default-rsc-entries/entry.rsc`][entry-rsc-source]
- [`@react-router/dev/config/default-rsc-entries/entry.ssr`][entry-ssr-source]
- [`@react-router/dev/config/default-rsc-entries/entry.client`][entry-client-source]

你可以通过上面的链接在 GitHub 上查看源代码，或直接在 `node_modules/@react-router/dev/dist/config/default-rsc-entries/` 中导航到这些文件。

<docs-info>

复制默认入口时，请确保保留必需的导出：

- `entry.rsc.ts` 必须导出一个带有 `fetch` 方法的默认对象
- `entry.ssr.ts` 必须导出一个 `generateHTML` 函数
- `entry.client.tsx` 应该处理客户端注水

</docs-info>

### 不支持的配置选项

在初始不稳定版本中，以下 `react-router.config.ts` 中的选项在 RSC 框架模式中尚不支持：

- `buildEnd`
- `prerender`
- `presets`
- `routeDiscovery`
- `serverBundles`
- `ssr: false`（SPA 模式）
- `future.v8_splitRouteModules`
- `future.unstable_subResourceIntegrity`

## RSC 数据模式

上述 RSC 框架模式 API 构建在更低级的 RSC 数据模式 API 之上。

RSC 数据模式缺少 RSC 框架模式的一些功能（如 `routes.ts` 配置和文件系统路由、HMR 和热数据重新验证），但更加灵活，允许你与自己的打包工具和服务器抽象集成。

### 配置路由

路由作为 [`matchRSCServerRequest`][match-rsc-server-request] 的参数进行配置。至少需要一个路径和组件：

```tsx
function Root() {
  return <h1>Hello world</h1>;
}

matchRSCServerRequest({
  // ...其他选项
  routes: [{ path: "/", Component: Root }],
});
```

虽然你可以内联定义组件，但我们推荐使用 `lazy()` 选项并定义[路由模块][route-module]，以兼顾启动性能和代码组织。

<docs-info>

[路由模块 API][route-module] 到目前为止一直是[框架模式][framework-mode]独有的功能。然而，RSC 路由配置的 `lazy` 字段期望与路由模块相同的导出，进一步统一了 API。

</docs-info>

```tsx filename=app/routes.ts
import type { unstable_RSCRouteConfig as RSCRouteConfig } from "react-router";

export function routes() {
  return [
    {
      id: "root",
      path: "",
      lazy: () => import("./root/route"),
      children: [
        {
          id: "home",
          index: true,
          lazy: () => import("./home/route"),
        },
        {
          id: "about",
          path: "about",
          lazy: () => import("./about/route"),
        },
      ],
    },
  ] satisfies RSCRouteConfig;
}
```

### Server Component 路由

默认情况下，每个路由的 `default` 导出渲染一个 Server Component

```tsx
export default function Home() {
  return (
    <main>
      <article>
        <h1>欢迎来到 React Router RSC</h1>
        <p>你不会发现我在浏览器中运行任何 JavaScript！</p>
      </article>
    </main>
  );
}
```

Server Components 的一个好处是你可以通过将组件设为异步来直接在组件中获取数据。

```tsx
export default async function Home() {
  let user = await getUserData();

  return (
    <main>
      <article>
        <h1>欢迎来到 React Router RSC</h1>
        <p>你不会发现我在浏览器中运行任何 JavaScript！</p>
        <p>你好，{user ? user.name : "匿名用户"}！</p>
      </article>
    </main>
  );
}
```

<docs-info>

Server Components 也可以从 loader 和 action 返回。通常，如果你使用 RSC 构建应用，loader 主要用于设置 `status` 状态码或返回 `redirect`。

在 loader 中使用 Server Components 对于渐进式采用 RSC 很有帮助。

</docs-info>

### Server Functions

[Server Functions][react-server-functions-doc] 是 React 提供的功能，允许你调用在服务器上执行的异步函数。它们通过 [`"use server"`][use-server-docs] 指令定义。

```tsx
"use server";

export async function updateFavorite(formData: FormData) {
  let movieId = formData.get("id");
  let intent = formData.get("intent");
  if (intent === "add") {
    await addFavorite(Number(movieId));
  } else {
    await removeFavorite(Number(movieId));
  }
}
```

```tsx
import { updateFavorite } from "./action.ts";
export async function AddToFavoritesForm({
  movieId,
}: {
  movieId: number;
}) {
  let isFav = await isFavorite(movieId);
  return (
    <form action={updateFavorite}>
      <input type="hidden" name="id" value={movieId} />
      <input
        type="hidden"
        name="intent"
        value={isFav ? "remove" : "add"}
      />
      <AddToFavoritesButton isFav={isFav} />
    </form>
  );
}
```

注意在调用 server function 后，React Router 会自动重新验证路由并用新的服务器内容更新 UI。你不需要处理任何缓存失效。

### 客户端属性

路由在服务器运行时定义，但我们仍然可以通过客户端引用和 `"use client"` 来提供 `clientLoader`、`clientAction` 和 `shouldRevalidate`。

```tsx filename=src/routes/root/client.tsx
"use client";

export function clientAction() {}

export function clientLoader() {}

export function shouldRevalidate() {}
```

然后我们可以从懒加载的路由模块中重新导出：

```tsx filename=src/routes/root/route.tsx
export {
  clientAction,
  clientLoader,
  shouldRevalidate,
} from "./route.client";

export default function Root() {
  // ...
}
```

这也是将整个路由设为 Client Component 的方式。

```tsx filename=src/routes/root/route.tsx lines=[1,11]
import { default as ClientRoot } from "./route.client";
export {
  clientAction,
  clientLoader,
  shouldRevalidate,
} from "./route.client";

export default function Root() {
  // 如果你使用 css 副作用导入，打包工具需要在根路由添加一个 Server Component
  return <ClientRoot />;
}
```

### 打包工具配置

React Router 提供了多个 API，让你可以轻松与支持 RSC 的打包工具集成。如果你使用 React Router 数据模式来构建自己的[自定义框架][custom-framework]，这很有用。

以下步骤展示了如何设置 React Router 应用使用 Server Components (RSC) 进行服务端渲染 (SSR) 页面并在客户端注水以支持单页应用 (SPA) 导航。你不必使用 SSR（甚至不需要客户端注水）。你也可以利用 HTML 生成来实现静态站点生成 (SSG) 或增量静态再生 (ISR)。本指南仅用于解释如何为典型的基于 RSC 的应用连接所有不同的 API。

### 入口点

除了[路由定义](#configuring-routes)外，我们还需要配置以下内容：

1. 一个服务器来处理传入请求、获取 RSC 载荷并将其转换为 HTML
2. 一个 React 服务器来生成 RSC 载荷
3. 一个浏览器处理器来注水生成的 HTML 并设置 `callServer` 函数以支持注水后的 server action

以下命名约定是为了熟悉性和简洁性而选择的。你可以根据需要命名和配置入口点。

有关每个入口点的具体代码示例，请参阅下面的相关打包工具文档。

这些示例都使用 [express][express] 和 [@remix-run/node-fetch-server][node-fetch-server] 来处理服务器和请求。

**路由**

参见[配置路由](#configuring-routes)。

**服务器**

<docs-info>

你完全不必使用 SSR。你可以选择使用 RSC 来"预渲染"HTML 以实现静态站点生成 (SSG) 或类似增量静态再生 (ISR) 的功能。

</docs-info>

`entry.ssr.tsx` 是服务器的入口点。它负责处理请求、调用 RSC 服务器，以及在文档请求时将 RSC 载荷转换为 HTML（服务端渲染）。

相关 API：

- [`routeRSCServerRequest`][route-rsc-server-request]
- [`RSCStaticRouter`][rsc-static-router]

**RSC 服务器**

<docs-info>

虽然你有一个"React 服务器"和一个负责请求处理/SSR 的服务器，但实际上不需要有 2 个独立的服务器。你只需在同一个服务器中有 2 个独立的模块图。这很重要，因为 React 在生成 RSC 载荷和生成要在客户端注水的 HTML 时行为不同。

</docs-info>

`entry.rsc.tsx` 是 React 服务器的入口点。它负责将请求匹配到路由并生成 RSC 载荷。

相关 API：

- [`matchRSCServerRequest`][match-rsc-server-request]

**浏览器**

`entry.browser.tsx` 是客户端的入口点。它负责注水生成的 HTML 并设置 `callServer` 函数以支持注水后的 server action。

相关 API：

- [`createCallServer`][create-call-server]
- [`getRSCStream`][get-rsc-stream]
- [`RSCHydratedRouter`][rsc-hydrated-router]

### Vite

更多信息请参阅 [@vitejs/plugin-rsc 文档][vite-plugin-rsc]。你也可以参考我们的 [Vite RSC 数据模式模板][vite-rsc-template] 来查看可运行的版本。

除了 `react`、`react-dom` 和 `react-router` 外，你还需要以下依赖：

```shellscript
npm i -D vite @vitejs/plugin-react @vitejs/plugin-rsc
```

#### `vite.config.ts`

要配置 Vite，在你的 `vite.config.ts` 中添加以下内容：

```ts filename=vite.config.ts
import rsc from "@vitejs/plugin-rsc/plugin";
import react from "@vitejs/plugin-react";
import { defineConfig } from "vite";

export default defineConfig({
  plugins: [
    react(),
    rsc({
      entries: {
        client: "src/entry.browser.tsx",
        rsc: "src/entry.rsc.tsx",
        ssr: "src/entry.ssr.tsx",
      },
    }),
  ],
});
```

```tsx filename=src/routes/config.ts
import type { unstable_RSCRouteConfig as RSCRouteConfig } from "react-router";

export function routes() {
  return [
    {
      id: "root",
      path: "",
      lazy: () => import("./root/route"),
      children: [
        {
          id: "home",
          index: true,
          lazy: () => import("./home/route"),
        },
        {
          id: "about",
          path: "about",
          lazy: () => import("./about/route"),
        },
      ],
    },
  ] satisfies RSCRouteConfig;
}
```

#### `entry.ssr.tsx`

以下是 Vite SSR 服务器的简化示例。

```tsx filename=src/entry.ssr.tsx
import { createFromReadableStream } from "@vitejs/plugin-rsc/ssr";
import { renderToReadableStream as renderHTMLToReadableStream } from "react-dom/server.edge";
import {
  unstable_routeRSCServerRequest as routeRSCServerRequest,
  unstable_RSCStaticRouter as RSCStaticRouter,
} from "react-router";

export async function generateHTML(
  request: Request,
  serverResponse: Response,
): Promise<Response> {
  return await routeRSCServerRequest({
    // 传入的请求
    request,
    // React Server 的响应
    serverResponse,
    // 提供 React Server 的接触点
    createFromReadableStream,
    // 将路由器渲染为 HTML
    async renderHTML(getPayload) {
      const payload = getPayload();

      const bootstrapScriptContent =
        await import.meta.viteRsc.loadBootstrapScriptContent(
          "index",
        );

      return await renderHTMLToReadableStream(
        <RSCStaticRouter getPayload={getPayload} />,
        {
          bootstrapScriptContent,
          formState: payload.formState,
        },
      );
    },
  });
}
```

#### `entry.rsc.tsx`

以下是 Vite RSC 服务器的简化示例。

```tsx filename=src/entry.rsc.tsx
import {
  createTemporaryReferenceSet,
  decodeAction,
  decodeFormState,
  decodeReply,
  loadServerAction,
  renderToReadableStream,
} from "@vitejs/plugin-rsc/rsc";
import { unstable_matchRSCServerRequest as matchRSCServerRequest } from "react-router";

import { routes } from "./routes/config";

function fetchServer(request: Request) {
  return matchRSCServerRequest({
    // 提供 React Server 的接触点
    createTemporaryReferenceSet,
    decodeAction,
    decodeFormState,
    decodeReply,
    loadServerAction,
    // 传入的请求
    request,
    // 应用路由
    routes: routes(),
    // 使用 React Server 实现编码匹配结果
    generateResponse(match) {
      return new Response(
        renderToReadableStream(match.payload),
        {
          status: match.statusCode,
          headers: match.headers,
        },
      );
    },
  });
}

export default async function handler(request: Request) {
  // 从客户端环境导入 generateHTML 函数
  const ssr = await import.meta.viteRsc.loadModule<
    typeof import("./entry.ssr")
  >("ssr", "index");

  return ssr.generateHTML(
    request,
    await fetchServer(request),
  );
}
```

#### `entry.browser.tsx`

```tsx filename=src/entry.browser.tsx
import {
  createFromReadableStream,
  createTemporaryReferenceSet,
  encodeReply,
  setServerCallback,
} from "@vitejs/plugin-rsc/browser";
import { startTransition, StrictMode } from "react";
import { hydrateRoot } from "react-dom/client";
import {
  unstable_createCallServer as createCallServer,
  unstable_getRSCStream as getRSCStream,
  unstable_RSCHydratedRouter as RSCHydratedRouter,
  type unstable_RSCPayload as RSCServerPayload,
} from "react-router";

// 创建并设置 callServer 函数以支持注水后的 server action
setServerCallback(
  createCallServer({
    createFromReadableStream,
    createTemporaryReferenceSet,
    encodeReply,
  }),
);

// 获取并解码初始服务器载荷
createFromReadableStream<RSCServerPayload>(
  getRSCStream(),
).then((payload) => {
  startTransition(async () => {
    const formState =
      payload.type === "render"
        ? await payload.formState
        : undefined;

    hydrateRoot(
      document,
      <StrictMode>
        <RSCHydratedRouter
          createFromReadableStream={
            createFromReadableStream
          }
          payload={payload}
        />
      </StrictMode>,
      {
        formState,
      },
    );
  });
});
```

[picking-a-mode]: ../start/modes
[react-server-components-doc]: https://react.dev/reference/rsc/server-components
[react-server-functions-doc]: https://react.dev/reference/rsc/server-functions
[use-client-docs]: https://react.dev/reference/rsc/use-client
[use-server-docs]: https://react.dev/reference/rsc/use-server
[route-module]: ../start/framework/route-module
[framework-mode]: ../start/modes#framework
[custom-framework]: ../start/data/custom
[vite-plugin-rsc]: https://github.com/vitejs/vite-plugin-react/tree/main/packages/plugin-rsc
[match-rsc-server-request]: ../api/rsc/matchRSCServerRequest
[route-rsc-server-request]: ../api/rsc/routeRSCServerRequest
[rsc-static-router]: ../api/rsc/RSCStaticRouter
[create-call-server]: ../api/rsc/createCallServer
[get-rsc-stream]: ../api/rsc/getRSCStream
[rsc-hydrated-router]: ../api/rsc/RSCHydratedRouter
[express]: https://expressjs.com/
[node-fetch-server]: https://www.npmjs.com/package/@remix-run/node-fetch-server
[framework-rsc-template]: https://github.com/remix-run/react-router-templates/tree/main/unstable_rsc-framework-mode
[vite-rsc-template]: https://github.com/remix-run/react-router-templates/tree/main/unstable_rsc-data-mode-vite
[node-request-listener]: https://nodejs.org/api/http.html#httpcreateserveroptions-requestlistener
[hooks]: https://react.dev/reference/react/hooks
[vite-env-only]: https://github.com/pcattori/vite-env-only
[server-modules]: ../api/framework-conventions/server-modules
[client-modules]: ../api/framework-conventions/client-modules
[server-only-package]: https://www.npmjs.com/package/server-only
[client-only-package]: https://www.npmjs.com/package/client-only
[entry-rsc-source]: https://github.com/remix-run/react-router/blob/main/packages/react-router-dev/config/default-rsc-entries/entry.rsc.tsx
[entry-ssr-source]: https://github.com/remix-run/react-router/blob/main/packages/react-router-dev/config/default-rsc-entries/entry.ssr.tsx
[entry-client-source]: https://github.com/remix-run/react-router/blob/main/packages/react-router-dev/config/default-rsc-entries/entry.client.tsx
