---
title: 中间件
---

# 中间件

[MODES: framework, data]

<br/>
<br/>

<docs-info>在框架模式中，你必须通过 [`future.v8_middleware`][future-flags] 标志选择启用中间件，因为它包含对 `getLoadContext` 函数和 loader/action `context` 参数的次要[破坏性变更][getloadcontext]。</docs-info>

中间件允许你在匹配路径的 [`Response`][Response] 生成前后运行代码。这使得[常见模式][common-patterns]如认证、日志记录、错误处理和数据预处理可以以可复用的方式实现。

中间件以嵌套链的方式运行，"向下"从父路由到子路由执行到你的路由处理器，然后在 [`Response`][Response] 生成后"向上"从子路由返回到父路由。

例如，对于 `GET /parent/child` 请求，中间件将按以下顺序运行：

```text
- Root 中间件开始
  - Parent 中间件开始
    - Child 中间件开始
      - 运行 loader，生成 HTML Response
    - Child 中间件结束
  - Parent 中间件结束
- Root 中间件结束
```

<docs-info>服务端中间件（框架模式）和客户端中间件（框架/数据模式）之间存在一些细微差异。在本文档中，我们大多数示例将使用服务端中间件，因为这对使用过其他 HTTP 服务器中间件的用户来说更为熟悉。更多信息请参阅下面的[服务端与客户端中间件][server-client]部分。</docs-info>

## 快速开始（框架模式）

### 1. 启用中间件标志

首先，在 [React Router 配置][rr-config]中启用中间件：

```ts filename=react-router.config.ts
import type { Config } from "@react-router/dev/config";

export default {
  future: {
    v8_middleware: true,
  },
} satisfies Config;
```

<docs-warning>启用中间件功能后，你的 [`action`][framework-action] 和 [`loader`][framework-loader] 的 `context` 参数类型将发生变化。如果你当前正在使用 `context`，请注意下面的 [`getLoadContext`][getloadcontext] 部分。</docs-warning>

### 2. 创建上下文

中间件使用 `context` provider 实例向下传递数据。
你可以使用 [`createContext`][createContext] 创建类型安全的上下文对象：

```ts filename=app/context.ts
import { createContext } from "react-router";
import type { User } from "~/types";

export const userContext = createContext<User | null>(null);
```

### 3. 从路由导出中间件

```tsx filename=app/routes/dashboard.tsx
import { redirect } from "react-router";
import { userContext } from "~/context";

// 服务端认证中间件
async function authMiddleware({ request, context }) {
  const user = await getUserFromSession(request);
  if (!user) {
    throw redirect("/login");
  }
  context.set(userContext, user);
}

export const middleware: Route.MiddlewareFunction[] = [
  authMiddleware,
];

// 客户端计时中间件
async function timingMiddleware({ context }, next) {
  const start = performance.now();
  await next();
  const duration = performance.now() - start;
  console.log(`导航耗时 ${duration}ms`);
}

export const clientMiddleware: Route.ClientMiddlewareFunction[] =
  [timingMiddleware];

export async function loader({
  context,
}: Route.LoaderArgs) {
  const user = context.get(userContext);
  const profile = await getProfile(user);
  return { profile };
}

export default function Dashboard({
  loaderData,
}: Route.ComponentProps) {
  return (
    <div>
      <h1>欢迎 {loaderData.profile.fullName}！</h1>
      <Profile profile={loaderData.profile} />
    </div>
  );
}
```

### 4. 更新 `getLoadContext` 函数（如适用）

如果你使用自定义服务器和 `getLoadContext` 函数，你需要更新实现以返回 [`RouterContextProvider`][RouterContextProvider] 实例，而非 JavaScript 对象：

```diff
+import {
+  createContext,
+  RouterContextProvider,
+} from "react-router";
import { createDb } from "./db";

+const dbContext = createContext<Database>();

function getLoadContext(req, res) {
-  return { db: createDb() };
+  const context = new RouterContextProvider();
+  context.set(dbContext, createDb());
+  return context;
}
```

## 快速开始（数据模式）

<docs-info>注意数据模式中没有 future flag，因为你可以通过向路由添加中间件来选择加入，不存在需要 future flag 的破坏性变更。</docs-info>

### 1. 创建上下文

中间件使用 `context` provider 实例向下传递数据。
你可以使用 [`createContext`][createContext] 创建类型安全的上下文对象：

```ts
import { createContext } from "react-router";
import type { User } from "~/types";

export const userContext = createContext<User | null>(null);
```

### 2. 向路由添加中间件

```tsx
import { redirect } from "react-router";
import { userContext } from "~/context";

const routes = [
  {
    path: "/",
    middleware: [timingMiddleware], // 👈
    Component: Root,
    children: [
      {
        path: "profile",
        middleware: [authMiddleware], // 👈
        loader: profileLoader,
        Component: Profile,
      },
      {
        path: "login",
        Component: Login,
      },
    ],
  },
];

async function timingMiddleware({ context }, next) {
  const start = performance.now();
  await next();
  const duration = performance.now() - start;
  console.log(`导航耗时 ${duration}ms`);
}

async function authMiddleware({ context }) {
  const user = await getUser();
  if (!user) {
    throw redirect("/login");
  }
  context.set(userContext, user);
}

export async function profileLoader({
  context,
}: Route.LoaderArgs) {
  const user = context.get(userContext);
  const profile = await getProfile(user);
  return { profile };
}

export default function Profile() {
  let loaderData = useLoaderData();
  return (
    <div>
      <h1>欢迎 {loaderData.profile.fullName}！</h1>
      <Profile profile={loaderData.profile} />
    </div>
  );
}
```

### 3. 添加 `getContext` 函数（可选）

如果你想在所有导航/fetch 中包含基础上下文，可以向路由器添加 [`getContext`][getContext] 函数。它将在每次导航/fetch 时被调用以填充一个新的上下文。

```tsx
let sessionContext = createContext();

const router = createBrowserRouter(routes, {
  getContext() {
    let context = new RouterContextProvider();
    context.set(sessionContext, getSession());
    return context;
  },
});
```

<docs-info>此 API 的目的是映射框架模式中服务端的 `getLoadContext` API，后者用于将 HTTP 服务器的值传递给 React Router 处理器。此 [`getContext`][getContext] API 可用于将 [`window`][window]/[`document`][document] 的全局值传递给 React Router，但由于它们都运行在同一个上下文（浏览器）中，你实际上可以通过根路由中间件达到同样的效果。因此，你可能不需要像在服务端那样使用此 API ——但为了一致性而提供。</docs-warning>

## 核心概念

### 服务端与客户端中间件

服务端中间件在框架模式下的服务器上运行，用于 HTML 文档请求和后续导航及 fetcher 调用的 `.data` 请求。因为服务端中间件在服务器上响应 HTTP [`Request`][request] 运行，它通过 `next` 函数将 HTTP [`Response`][Response] 返回到中间件链：

```ts
async function serverMiddleware({ request }, next) {
  console.log(request.method, request.url);
  let response = await next();
  console.log(response.status, request.method, request.url);
  return response;
}

// 仅框架模式
export const middleware: Route.MiddlewareFunction[] = [
  serverMiddleware,
];
```

客户端中间件在浏览器中运行（框架和数据模式），用于客户端导航和 fetcher 调用。客户端中间件与服务端中间件的不同之处在于没有 HTTP Request，因此没有 `Response` 需要冒泡。在大多数情况下，你可以忽略 `next` 的返回值，并在客户端中间件中不返回任何内容：

```ts
async function clientMiddleware({ request }, next) {
  console.log(request.method, request.url);
  await next();
  console.log(response.status, request.method, request.url);
}

// 框架模式
export const clientMiddleware: Route.ClientMiddlewareFunction[] =
  [clientMiddleware];

// 或者，数据模式
const route = {
  path: "/",
  middleware: [clientMiddleware],
  loader: rootLoader,
  Component: Root,
};
```

在 _某些_ 情况下，你可能想根据 loader/action 的结果进行后处理。`Response` 的替代是，客户端中间件冒泡从活动 [`dataStrategy`][datastrategy] 返回的值（`Record<string, DataStrategyResult>` —— 按路由 id 索引）。这允许你在中间件中根据已执行的 `loader`/`action` 函数的结果采取条件操作。

以下是作为客户端中间件实现的 [CMS 404 重定向][cms-redirect]用例示例：

```tsx
async function cmsFallbackMiddleware({ request }, next) {
  const results = await next();

  // 检查是否从任何路由收到 404，如果是，在 CMS 中查找重定向
  const found404 = Object.values(results).some(
    (r) =>
      isRouteErrorResponse(r.result) &&
      r.result.status === 404,
  );
  if (found404) {
    const cmsRedirect = await checkCMSRedirects(
      request.url,
    );
    if (cmsRedirect) {
      throw redirect(cmsRedirect, 302);
    }
  }
}
```

<docs-warning>在服务端中间件中，你不应该操作 `Response` 的 body，只应读取 status/headers 并设置 headers。类似地，在客户端中间件中此值应被视为只读，因为它代表导航的"body"或"数据"，应该由 loader/action 驱动 —— 而非中间件。这也意味着在客户端中间件中，通常不需要返回结果，即使你需要从 `await next()` 捕获它。</docs-warning>

### 中间件何时运行

理解你的中间件 _何时_ 运行非常重要，以确保应用按预期行为。

#### 服务端中间件

在已注水的框架模式应用中，服务端中间件的设计优先考虑 SPA 行为，默认不创建新的网络活动。中间件包裹 _现有_ 请求，只在你 _需要_ 访问服务器时才运行。

这引出了一个问题：React Router 中的"处理器"是什么？是路由？还是 `loader`？我们认为"这取决于"：

- 文档请求（`GET /route`）的处理器是路由 —— 因为响应包含 `loader` 和路由组件
- 客户端导航的数据请求（`GET /route.data`）的处理器是 [`action`][data-action]/[`loader`][data-loader]，因为这是响应中唯一包含的内容

因此：

- 无论 `loader` 是否存在，文档请求都运行服务端中间件，因为我们仍在渲染 UI 的"处理器"中
- 客户端导航只有在向服务器发出 `.data` 请求以获取 [`action`][framework-action]/[`loader`][framework-loader] 时才运行服务端中间件

对于请求标注中间件（如记录请求持续时间、检查/设置 session、设置外发缓存头等），这是重要的行为。如果没有理由去服务器，却要到服务器运行这些类型的中间件，那将是无用的。这会导致服务器负载增加和嘈杂的服务器日志。

```tsx filename=app/root.tsx
// 此中间件不会在没有 `.data` 请求的客户端导航中运行
async function loggingMiddleware({ request }, next) {
  console.log(`请求: ${request.method} ${request.url}`);
  let response = await next();
  console.log(
    `响应: ${response.status} ${request.method} ${request.url}`,
  );
  return response;
}

export const middleware: Route.MiddlewareFunction[] = [
  loggingMiddleware,
];
```

然而，在某些情况下你可能 _想要_ 在 _每次_ 客户端导航中运行某些服务端中间件 —— 即使不存在 `loader`。例如，网站认证区域中的表单不需要 `loader`，但你更希望在用户填写表单之前使用认证中间件重定向他们 —— 而不是在他们提交到 `action` 时。如果你的中间件符合这些条件，你可以在包含中间件的路由上放置一个 `loader`，以强制客户端导航涉及该路由时始终调用服务器。

```tsx filename=app/_auth.tsx
function authMiddleware({ request }, next) {
  if (!isLoggedIn(request)) {
    throw redirect("/login");
  }
}

export const middleware: Route.MiddlewareFunction[] = [
  authMiddleware,
];

// 通过添加 `loader`，我们强制 `authMiddleware` 在每次涉及该路由的客户端导航中运行。
export async function loader() {
  return null;
}
```

#### 客户端中间件

客户端中间件更简单，因为我们已经在客户端，导航时总是向路由器发出"请求"。无论是否有 `loader` 要运行，客户端中间件都会在每次客户端导航中运行。

### Context API

新的上下文系统提供类型安全并防止命名冲突，允许你向嵌套中间件和 `action`/`loader` 函数提供数据。在框架模式中，这取代了之前的 `AppLoadContext` API。

```ts
// ✅ 类型安全
import { createContext } from "react-router";
const userContext = createContext<User>();

// 在中间件/`loader` 中使用
context.set(userContext, user); // 必须是 `User` 类型
const user = context.get(userContext); // 返回 `User` 类型

// ❌ 旧方式（无类型安全）
context.user = user; // 可以是任何类型
```

#### `Context` 与 `AsyncLocalStorage`

Node 提供了 [`AsyncLocalStorage`][asynclocalstorage] API，它给你一种通过异步执行上下文提供值的方式。虽然这是 Node API，但大多数现代运行时已经（大部分）支持了它（即 [Cloudflare][cloudflare]、[Bun][bun]、[Deno][deno]）。

理论上，我们可以直接利用 [`AsyncLocalStorage`][asynclocalstorage] 作为从中间件向子路由传递值的方式，但缺乏 100% 跨平台兼容性令人担忧，因此我们仍然想提供一流的 `context` API，以保证有一种方式发布以运行时无关方式工作的可复用中间件包。

也就是说，此 API 与 React Router 中间件配合使用效果很好，可以替代或与 `context` API 并行使用：

<docs-info>[`AsyncLocalStorage`][asynclocalstorage] 在使用 [React Server Components](../how-to/react-server-components) 时 _特别_ 强大，因为它允许你从 `middleware` 向 Server Components 和 Server Actions 提供信息，因为它们运行在同一个服务器执行上下文中 🤯</docs-info>

```tsx filename=app/user-context.ts
import { AsyncLocalStorage } from "node:async_hooks";

const USER = new AsyncLocalStorage<User>();

export async function provideUser(
  request: Request,
  cb: () => Promise<Response>,
) {
  let user = await getUser(request);
  return USER.run(user, cb);
}

export function getUser() {
  return USER.getStore();
}
```

```tsx filename=app/root.tsx
import { provideUser } from "./user-context";

export const middleware: Route.MiddlewareFunction[] = [
  async ({ request, context }, next) => {
    return provideUser(request, async () => {
      let res = await next();
      return res;
    });
  },
];
```

```tsx filename=app/routes/_index.tsx
import { getUser } from "../user-context";

export async function loader() {
  let user = getUser();
  //...
}
```

### `next` 函数

`next` 函数的逻辑取决于从哪个路由中间件调用它：

- 从非叶子中间件调用时，它运行链中的下一个中间件
- 从叶子中间件调用时，它执行所有路由处理器并为请求生成 [`Response`][Response]

```ts
const middleware = async ({ context }, next) => {
  // 这里的代码在处理器之前运行
  console.log("之前");

  const response = await next();

  // 这里的代码在处理器之后运行
  console.log("之后");

  return response; // 客户端可选，服务端必需
};
```

<docs-warning>每个中间件只能调用 `next()` 一次。多次调用将抛出错误。</docs-warning>

### 跳过 `next()`

如果你不需要在处理器之后运行代码，可以跳过调用 `next()`：

```ts
const authMiddleware = async ({ request, context }) => {
  const user = await getUser(request);
  if (!user) {
    throw redirect("/login");
  }
  context.set(userContext, user);
  // next() 会自动调用
};
```

### `next()` 与错误处理

React Router 通过路由 [`ErrorBoundary`][ErrorBoundary] 导出包含内置错误处理。就像 `action`/`loader` 抛出时一样，如果 `middleware` 抛出，它将在适当的 [`ErrorBoundary`][ErrorBoundary] 被捕获和处理，并且 [`Response`][Response] 将通过祖先的 `next()` 调用返回。这意味着 `next()` 函数永远不应该抛出，应该始终返回 [`Response`][Response]，所以你不需要用 try/catch 包裹它。

这种行为对于从根 `middleware` 自动设置必需的外发响应头（如提交 session）等中间件模式很重要。如果 `middleware` 的任何错误导致 `next()` `throw`，我们会错过向上执行祖先中间件，那些必需的头就不会被设置。

```tsx filename=routes/parent.tsx
export const middleware: Route.MiddlewareFunction[] = [
  async (_, next) => {
    let res = await next();
    //  ^ res.status = 500
    // 此响应包含 ErrorBoundary
    return res;
  },
];
```

```tsx filename=routes/parent.child.tsx
export const middleware: Route.MiddlewareFunction[] = [
  async (_, next) => {
    let res = await next();
    //  ^ res.status = 200
    // 此响应包含成功的 UI 渲染
    throw new Error("哦不，出问题了！");
  },
];
```

渲染哪个 `ErrorBoundary` 取决于你的中间件是在调用 `next()` _之前_ 还是 _之后_ 抛出。如果在 _之后_ 抛出，它将像正常的 loader 错误一样从抛出的路由冒泡，因为我们已经运行了 loader 并有适当的 `loaderData` 在路由组件中渲染。但是，如果错误在调用 `next()` _之前_ 抛出，我们还没有调用任何 loader，也没有可用的 `loaderData`。在这种情况下，我们必须冒泡到有 `loader` 的最高级路由，并从那里开始查找 `ErrorBoundary`。在该级别或以下，没有任何 `loaderData` 就无法渲染路由组件。

## `getLoadContext`/`AppLoadContext` 的变更

<docs-info>这仅在你使用自定义服务器和自定义 `getLoadContext` 函数时适用</docs-info>

中间件引入了对 `getLoadContext` 生成的 `context` 参数的破坏性变更，该参数传递给你的 `action` 和 `loader`。当前通过模块增强 `AppLoadContext` 的方式并不真正类型安全，只是告诉 TypeScript "相信我"。

中间件需要客户端上等效的 `context` 用于 `clientMiddleware`，但我们不想在客户端复制服务端这个我们已经不太满意的模式，所以我们决定引入一个新 API 来解决类型安全问题。

选择启用中间件时，`context` 参数变为 [`RouterContextProvider`][RouterContextProvider] 的实例：

```ts
let dbContext = createContext<Database>();
let context = new RouterContextProvider();
context.set(dbContext, getDb());
//                     ^ 类型安全
let db = context.get(dbContext);
//  ^ Database
```

如果你使用自定义服务器和 `getLoadContext` 函数，需要更新实现以返回 [`RouterContextProvider`][RouterContextProvider] 实例，而非普通 JavaScript 对象：

```diff
+import {
+  createContext,
+  RouterContextProvider,
+} from "react-router";
import { createDb } from "./db";

+const dbContext = createContext<Database>();

function getLoadContext(req, res) {
-  return { db: createDb() };
+  const context = new RouterContextProvider();
+  context.set(dbContext, createDb());
+  return context;
}
```

### 从 `AppLoadContext` 迁移

如果你当前正在使用 `AppLoadContext`，可以通过使用现有的模块增强来增强 [`RouterContextProvider`][RouterContextProvider]（而非 `AppLoadContext`）进行增量迁移。然后，更新你的 `getLoadContext` 函数以返回 [`RouterContextProvider`][RouterContextProvider] 实例：

```diff
declare module "react-router" {
-  interface AppLoadContext {
+  interface RouterContextProvider {
    db: Database;
    user: User;
  }
}

function getLoadContext() {
  const loadContext = {...};
-  return loadContext;
+  let context = new RouterContextProvider();
+  Object.assign(context, loadContext);
+  return context;
}
```

这允许你在初始采用中间件期间保持 `action`/`loader` 不变，因为它们仍然可以直接读取值（即 `context.db`）。

<docs-warning>此方法仅作为在 React Router v7 中采用中间件时的迁移策略，允许你增量迁移到 `context.set`/`context.get`。不能安全地假设此方法在 React Router 的下一个主要版本中仍然有效。</docs-warning>

<docs-warning>[`RouterContextProvider`][RouterContextProvider] 类也用于通过 `<HydratedRouter getContext>` 和 `<RouterProvider getContext>` 提供的客户端 `context` 参数。由于 `AppLoadContext` 主要用于从 HTTP 服务器到 React Router 处理器的传递，你需要注意这些增强的字段在 `clientMiddleware`、`clientLoader` 或 `clientAction` 函数中将不可用，即使 TypeScript 告诉你它们可用（除非你当然通过客户端的 `getContext` 提供这些字段）。</docs-warning>

## 常见模式

### 认证

```tsx filename=app/middleware/auth.ts
import { redirect } from "react-router";
import { userContext } from "~/context";
import { getSession } from "~/sessions.server";

export const authMiddleware = async ({
  request,
  context,
}) => {
  const session = await getSession(request);
  const userId = session.get("userId");

  if (!userId) {
    throw redirect("/login");
  }

  const user = await getUserById(userId);
  context.set(userContext, user);
};
```

```tsx filename=app/routes/protected.tsx
import { authMiddleware } from "~/middleware/auth";

export const middleware: Route.MiddlewareFunction[] = [
  authMiddleware,
];

export async function loader({
  context,
}: Route.LoaderArgs) {
  const user = context.get(userContext); // 保证存在
  return { user };
}
```

### 日志记录

```tsx filename=app/middleware/logging.ts
import { requestIdContext } from "~/context";

export const loggingMiddleware = async (
  { request, context },
  next,
) => {
  const requestId = crypto.randomUUID();
  context.set(requestIdContext, requestId);

  console.log(
    `[${requestId}] ${request.method} ${request.url}`,
  );

  const start = performance.now();
  const response = await next();
  const duration = performance.now() - start;

  console.log(
    `[${requestId}] 响应 ${response.status} (${duration}ms)`,
  );

  return response;
};
```

### CMS 404 重定向

```tsx filename=app/middleware/cms-fallback.ts
export const cmsFallbackMiddleware = async (
  { request },
  next,
) => {
  const response = await next();

  // 检查是否收到 404
  if (response.status === 404) {
    // 在 CMS 中查找重定向
    const cmsRedirect = await checkCMSRedirects(
      request.url,
    );
    if (cmsRedirect) {
      throw redirect(cmsRedirect, 302);
    }
  }

  return response;
};
```

### 响应头

```tsx filename=app/middleware/headers.ts
export const headersMiddleware = async (
  { context },
  next,
) => {
  const response = await next();

  // 添加安全头
  response.headers.set("X-Frame-Options", "DENY");
  response.headers.set("X-Content-Type-Options", "nosniff");

  return response;
};
```

### 条件中间件

```tsx
export const middleware: Route.MiddlewareFunction[] = [
  async ({ request, context }, next) => {
    // 仅对 POST 请求运行认证
    if (request.method === "POST") {
      await ensureAuthenticated(request, context);
    }
    return next();
  },
];
```

### 在 `action` 和 `loader` 之间共享 Context

<docs-info>在服务端，此方法仅适用于文档 POST 请求，因为 `context` 的作用域是一个请求。SPA 导航提交使用独立的 POST/GET 请求，因此你无法在它们之间共享 `context`。此模式在 `clientMiddleware`/`clientLoader`/`clientAction` 中始终有效，因为没有独立的 HTTP 请求。</docs-info>

```tsx
const sharedDataContext = createContext<any>();

export const middleware: Route.MiddlewareFunction[] = [
  async ({ request, context }, next) => {
    // 如果数据不存在则设置
    // 对于文档请求只运行一次
    // 在 SPA 提交中会运行两次（action 请求 + loader 请求）
    if (!context.get(sharedDataContext)) {
      context.set(
        sharedDataContext,
        await getExpensiveData(),
      );
    }
    return next();
  },
];

export async function action({
  context,
}: Route.ActionArgs) {
  const data = context.get(sharedDataContext);
  // 使用数据...
}

export async function loader({
  context,
}: Route.LoaderArgs) {
  const data = context.get(sharedDataContext);
  // 同样的数据在这里也可用
}
```

[future-flags]: ../upgrading/future
[Response]: https://developer.mozilla.org/en-US/docs/Web/API/Response
[common-patterns]: #common-patterns
[server-client]: #server-vs-client-middleware
[rr-config]: ../api/framework-conventions/react-router.config.ts
[framework-action]: ../start/framework/route-module#action
[framework-loader]: ../start/framework/route-module#loader
[getloadcontext]: #changes-to-getloadcontextapploadcontext
[datastrategy]: ../api/data-routers/createBrowserRouter#optsdatastrategy
[cms-redirect]: #cms-redirect-on-404
[createContext]: ../api/utils/createContext
[RouterContextProvider]: ../api/utils/RouterContextProvider
[getContext]: ../api/data-routers/createBrowserRouter#optsgetContext
[window]: https://developer.mozilla.org/en-US/docs/Web/API/Window
[document]: https://developer.mozilla.org/en-US/docs/Web/API/Document
[request]: https://developer.mozilla.org/en-US/docs/Web/API/Request
[data-action]: ../start/data/route-object#action
[data-loader]: ../start/data/route-object#loader
[asynclocalstorage]: https://nodejs.org/api/async_context.html#class-asynclocalstorage
[cloudflare]: https://developers.cloudflare.com/workers/runtime-apis/nodejs/asynclocalstorage/
[bun]: https://bun.sh/blog/bun-v0.7.0#asynclocalstorage-support
[deno]: https://docs.deno.com/api/node/async_hooks/~/AsyncLocalStorage
[ErrorBoundary]: ../start/framework/route-module#errorboundary
