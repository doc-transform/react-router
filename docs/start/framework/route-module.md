---
title: 路由模块
order: 3
---

# 路由模块

[MODES: framework]

## 简介

`routes.ts` 中引用的文件称为路由模块。

```tsx filename=app/routes.ts
route("teams/:teamId", "./team.tsx"),
//           路由模块 ^^^^^^^^
```

路由模块是 React Router 框架功能的基础，它们定义了：

- 自动代码拆分
- 数据加载
- 操作（Action）
- 重新验证
- 错误边界
- 以及更多

本指南是对路由模块所有功能的快速概览。后续的入门指南将更详细地介绍这些功能。

## Component（`default`）

路由模块中的 `default` 导出定义了当路由匹配时要渲染的组件。

```tsx filename=app/routes/my-route.tsx
export default function MyRouteComponent() {
  return (
    <div>
      <h1>Look ma!</h1>
      <p>
        I'm still using React Router after like 10 years.
      </p>
    </div>
  );
}
```

### 传递给组件的 Props

当组件渲染时，它会接收 React Router 自动生成的 `Route.ComponentProps` 中定义的 props。这些 props 包括：

1. `loaderData`：本路由模块中 `loader` 函数返回的数据
2. `actionData`：本路由模块中 `action` 函数返回的数据
3. `params`：包含路由参数的对象（如果有的话）
4. `matches`：当前路由树中所有匹配项的数组

你可以用这些 props 替代 `useLoaderData` 或 `useParams` 等 Hook。这可能更好，因为它们会自动正确地获得路由对应的类型。

### 使用 props

```tsx filename=app/routes/my-route-with-default-params.tsx
import type { Route } from "./+types/route-name";

export default function MyRouteComponent({
  loaderData,
  actionData,
  params,
  matches,
}: Route.ComponentProps) {
  return (
    <div>
      <h1>Welcome to My Route with Props!</h1>
      <p>Loader Data: {JSON.stringify(loaderData)}</p>
      <p>Action Data: {JSON.stringify(actionData)}</p>
      <p>Route Parameters: {JSON.stringify(params)}</p>
      <p>Matched Routes: {JSON.stringify(matches)}</p>
    </div>
  );
}
```

## `middleware`

路由[中间件][middleware]在服务器上按顺序在文档和数据请求前后运行。这为你提供了一个统一的位置来处理日志记录、身份认证和响应后处理等事务。`next` 函数继续沿链路向下执行，在叶子路由上 `next` 函数会执行该导航的 loader/action。

以下是一个在服务器上记录请求日志的中间件示例：

```tsx filename=root.tsx
async function loggingMiddleware(
  { request, context },
  next,
) {
  console.log(
    `${new Date().toISOString()} ${request.method} ${request.url}`,
  );
  const start = performance.now();
  const response = await next();
  const duration = performance.now() - start;
  console.log(
    `${new Date().toISOString()} Response ${response.status} (${duration}ms)`,
  );
  return response;
}

export const middleware = [loggingMiddleware];
```

以下是一个检查用户登录状态并在 `context` 中设置用户信息的中间件示例，之后你可以在 loader 中访问该用户信息：

```tsx filename=routes/_auth.tsx
async function authMiddleware({ request, context }) {
  const session = await getSession(request);
  const userId = session.get("userId");

  if (!userId) {
    throw redirect("/login");
  }

  const user = await getUserById(userId);
  context.set(userContext, user);
}

export const middleware = [authMiddleware];
```

<docs-warning>请确保你理解了[中间件何时运行][when-middleware-runs]，以确保你在路由中添加中间件时，应用的行为符合预期。</docs-warning>

另请参阅：

- [`middleware` 参数][middleware-params]
- [中间件][middleware]

## `clientMiddleware`

这是 `middleware` 的客户端等价物，在客户端导航期间在浏览器中运行。与服务端中间件的唯一区别是，客户端中间件不返回 Response，因为它们不是在服务器上包装 HTTP 请求。

以下是一个在客户端记录请求日志的中间件示例：

```tsx filename=root.tsx
async function loggingMiddleware(
  { request, context },
  next,
) {
  console.log(
    `${new Date().toISOString()} ${request.method} ${request.url}`,
  );
  const start = performance.now();
  await next(); // 👈 不返回 Response
  const duration = performance.now() - start;
  console.log(
    `${new Date().toISOString()} (${duration}ms)`,
  );
  // ✅ 无需返回任何内容
}

export const clientMiddleware = [loggingMiddleware];
```

另请参阅：

- [中间件][middleware]
- [客户端数据][client-data]

## `loader`

路由 loader 在组件渲染之前为路由组件提供数据。在服务端渲染或使用预渲染构建时，它们只在服务端调用。

```tsx
export async function loader() {
  return { message: "Hello, world!" };
}

export default function MyRoute({ loaderData }) {
  return <h1>{loaderData.message}</h1>;
}
```

另请参阅：

- [`loader` 参数][loader-params]

## `clientLoader`

仅在浏览器中调用，路由客户端 loader 可以为路由组件提供数据，作为路由 loader 的补充或替代。

```tsx
export async function clientLoader({ serverLoader }) {
  // 调用服务端 loader
  const serverData = await serverLoader();
  // 和/或在客户端获取数据
  const data = getDataFromClient();
  // 返回数据，通过 useLoaderData() 暴露
  return data;
}
```

客户端 loader 可以通过在函数上设置 `hydrate` 属性来参与服务端渲染页面的初始页面加载注水：

```tsx
export async function clientLoader() {
  // ...
}
clientLoader.hydrate = true as const;
```

<docs-info>

通过使用 `as const`，TypeScript 会推断 `clientLoader.hydrate` 的类型为 `true` 而非 `boolean`。
这样，React Router 就可以根据 `clientLoader.hydrate` 的值推导 `loaderData` 的类型。

</docs-info>

另请参阅：

- [`clientLoader` 参数][client-loader-params]
- [客户端数据][client-data]

## `action`

路由 action 允许进行服务端数据变更，当通过 `<Form>`、`useFetcher` 和 `useSubmit` 调用时，页面上所有的 loader 数据会自动重新验证。

```tsx
// route("/list", "./list.tsx")
import { Form } from "react-router";
import { TodoList } from "~/components/TodoList";

// 这些数据会在 action 完成后自动加载……
export async function loader() {
  const items = await fakeDb.getItems();
  return { items };
}

// ……因此这里的列表会自动更新
export default function Items({ loaderData }) {
  return (
    <div>
      <List items={loaderData.items} />
      <Form method="post" navigate={false} action="/list">
        <input type="text" name="title" />
        <button type="submit">Create Todo</button>
      </Form>
    </div>
  );
}

export async function action({ request }) {
  const data = await request.formData();
  const todo = await fakeDb.addItem({
    title: data.get("title"),
  });
  return { ok: true };
}
```

另请参阅：

- [`action` 参数][action-params]

## `clientAction`

与路由 action 类似，但仅在浏览器中调用。

```tsx
export async function clientAction({ serverAction }) {
  fakeInvalidateClientSideCache();
  // 如果需要，仍然可以调用服务端 action
  const data = await serverAction();
  return data;
}
```

另请参阅：

- [`clientAction` 参数][client-action-params]
- [客户端数据][client-data]

## `ErrorBoundary`

当其他路由模块 API 抛出错误时，路由模块的 `ErrorBoundary` 将替代路由组件进行渲染。

```tsx
import {
  isRouteErrorResponse,
  useRouteError,
} from "react-router";

export function ErrorBoundary() {
  const error = useRouteError();

  if (isRouteErrorResponse(error)) {
    return (
      <div>
        <h1>
          {error.status} {error.statusText}
        </h1>
        <p>{error.data}</p>
      </div>
    );
  } else if (error instanceof Error) {
    return (
      <div>
        <h1>Error</h1>
        <p>{error.message}</p>
        <p>The stack trace is:</p>
        <pre>{error.stack}</pre>
      </div>
    );
  } else {
    return <h1>Unknown Error</h1>;
  }
}
```

另请参阅：

- [`useRouteError`][use-route-error]
- [`isRouteErrorResponse`][is-route-error-response]

## `HydrateFallback`

在初始页面加载时，路由组件仅在客户端 loader 完成后才渲染。如果导出了 `HydrateFallback`，它可以在路由组件位置立即渲染。

```tsx filename=routes/client-only-route.tsx
export async function clientLoader() {
  const data = await fakeLoadLocalGameData();
  return data;
}

export function HydrateFallback() {
  return <p>Loading Game...</p>;
}

export default function Component({ loaderData }) {
  return <Game data={loaderData} />;
}
```

## `headers`

路由的 `headers` 函数定义了在服务端渲染时随响应发送的 HTTP 头。

```tsx
export function headers() {
  return {
    "X-Stretchy-Pants": "its for fun",
    "Cache-Control": "max-age=300, s-maxage=3600",
  };
}
```

另请参阅：

- [`Headers`][headers]

## `handle`

路由 handle 允许应用在 `useMatches` 的路由匹配中添加任何内容，用于创建抽象（如面包屑等）。

```tsx
export const handle = {
  its: "all yours",
};
```

另请参阅：

- [`useMatches`][use-matches]

## `links`

路由 links 定义要在文档 `<head>` 中渲染的 [`<link>` 元素][link-element]。

```tsx
export function links() {
  return [
    {
      rel: "icon",
      href: "/favicon.png",
      type: "image/png",
    },
    {
      rel: "stylesheet",
      href: "https://example.com/some/styles.css",
    },
    {
      rel: "preload",
      href: "/images/banner.jpg",
      as: "image",
    },
  ];
}
```

所有路由的 links 会被聚合并通过 `<Links />` 组件渲染，通常在应用根组件中：

```tsx
import { Links } from "react-router";

export default function Root() {
  return (
    <html>
      <head>
        <Links />
      </head>

      <body />
    </html>
  );
}
```

## `meta`

路由 meta 定义要在 `<Meta />` 组件中渲染的 [meta 标签][meta-element]，通常放在 `<head>` 中。

<docs-warning>

从 React 19 开始，推荐使用[内置的 `<meta>` 元素](https://react.dev/reference/react-dom/components/meta)，而非路由模块的 `meta` 导出。

以下是使用它和 `<title>` 元素的示例：

```tsx
export default function MyRoute() {
  return (
    <div>
      <title>Very cool app</title>
      <meta property="og:title" content="Very cool app" />
      <meta
        name="description"
        content="This app is the best"
      />
      {/* 路由的其余内容... */}
    </div>
  );
}
```

</docs-warning>

```tsx filename=app/product.tsx
export function meta() {
  return [
    { title: "Very cool app" },
    {
      property: "og:title",
      content: "Very cool app",
    },
    {
      name: "description",
      content: "This app is the best",
    },
  ];
}
```

```tsx filename=app/root.tsx
import { Meta } from "react-router";

export default function Root() {
  return (
    <html>
      <head>
        <Meta />
      </head>

      <body />
    </html>
  );
}
```

最后匹配的路由的 meta 会被使用，允许你覆盖父路由的 meta。需要注意的是，整个 meta 描述符数组是被替换而非合并的。这让你可以灵活地在不同层级的页面之间构建自己的 meta 组合逻辑。

**另请参阅**

- [`meta` 参数][meta-params]
- [`meta` 函数返回类型][meta-function]

## `shouldRevalidate`

在带有 SSR 的框架模式中，路由 loader 会在所有导航和表单提交后自动重新验证（这与[数据模式][data-mode-should-revalidate]不同）。这使得中间件和 loader 可以共享请求上下文，并以不同于数据模式的方式进行优化。

定义此函数允许你在导航和表单提交时选择退出路由 loader 的重新验证。

```tsx
import type { ShouldRevalidateFunctionArgs } from "react-router";

export function shouldRevalidate(
  arg: ShouldRevalidateFunctionArgs,
) {
  return true;
}
```

使用 [SPA 模式][spa-mode]时，没有服务端 loader 可在导航时调用，因此 `shouldRevalidate` 的行为与[数据模式][data-mode-should-revalidate]相同。

[`ShouldRevalidateFunctionArgs` 参考文档 ↗](https://api.reactrouter.com/v7/interfaces/react-router.ShouldRevalidateFunctionArgs.html)

---

下一节：[渲染策略](./rendering)

[middleware-params]: https://api.reactrouter.com/v7/types/react-router.MiddlewareFunction.html
[middleware]: ../../how-to/middleware
[when-middleware-runs]: ../../how-to/middleware#when-middleware-runs
[loader-params]: https://api.reactrouter.com/v7/interfaces/react-router.LoaderFunctionArgs
[client-loader-params]: https://api.reactrouter.com/v7/types/react-router.ClientLoaderFunctionArgs
[action-params]: https://api.reactrouter.com/v7/interfaces/react-router.ActionFunctionArgs
[client-action-params]: https://api.reactrouter.com/v7/types/react-router.ClientActionFunctionArgs
[use-route-error]: ../../api/hooks/useRouteError
[is-route-error-response]: ../../api/utils/isRouteErrorResponse
[headers]: https://developer.mozilla.org/en-US/docs/Web/API/Response/headers
[use-matches]: ../../api/hooks/useMatches
[link-element]: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link
[meta-element]: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta
[meta-params]: https://api.reactrouter.com/v7/interfaces/react-router.MetaArgs
[meta-function]: https://api.reactrouter.com/v7/types/react-router.MetaDescriptor.html
[data-mode-should-revalidate]: ../data/route-object#shouldrevalidate
[spa-mode]: ../../how-to/spa
[client-data]: ../../how-to/client-data
