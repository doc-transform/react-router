---
title: 路由对象
order: 3
---

# 路由对象

[MODES: data]

## 简介

传递给 `createBrowserRouter` 的对象称为路由对象。

```tsx lines=[2-5]
createBrowserRouter([
  {
    path: "/",
    Component: App,
  },
]);
```

路由模块是 React Router 数据功能的基础，它们定义了：

- 数据加载
- 操作（Action）
- 重新验证
- 错误边界
- 以及更多

本指南是对路由对象所有功能的快速概览。

## Component

路由对象中的 `Component` 属性定义了当路由匹配时要渲染的组件。

```tsx lines=[4]
createBrowserRouter([
  {
    path: "/",
    Component: MyRouteComponent,
  },
]);

function MyRouteComponent() {
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

## `middleware`

路由[中间件][middleware]在导航前后按顺序运行。这为你提供了一个统一的位置来处理日志记录和身份认证等事务。`next` 函数继续沿链路向下执行，在叶子路由上 `next` 函数会执行该导航的 loader/action。

```tsx
createBrowserRouter([
  {
    path: "/",
    middleware: [loggingMiddleware],
    loader: rootLoader,
    Component: Root,
    children: [{
      path: 'auth',
      middleware: [authMiddleware],
      loader: authLoader,
      Component: Auth,
      children: [...]
    }]
  },
]);

async function loggingMiddleware({ request }, next) {
  let url = new URL(request.url);
  console.log(`Starting navigation: ${url.pathname}${url.search}`);
  const start = performance.now();
  await next();
  const duration = performance.now() - start;
  console.log(`Navigation completed in ${duration}ms`);
}

const userContext = createContext<User>();

async function authMiddleware ({ context }) {
  const userId = getUserId();

  if (!userId) {
    throw redirect("/login");
  }

  context.set(userContext, await getUserById(userId));
};
```

另请参阅：

- [中间件][middleware]

## `loader`

路由 loader 在组件渲染之前为路由组件提供数据。

```tsx
import {
  useLoaderData,
  createBrowserRouter,
} from "react-router";

createBrowserRouter([
  {
    path: "/",
    loader: loader,
    Component: MyRoute,
  },
]);

async function loader({ params }) {
  return { message: "Hello, world!" };
}

function MyRoute() {
  let data = useLoaderData();
  return <h1>{data.message}</h1>;
}
```

另请参阅：

- [`loader` 参数][loader-params]

## `action`

路由 action 允许进行服务端数据变更，当通过 `<Form>`、`useFetcher` 和 `useSubmit` 调用时，页面上所有的 loader 数据会自动重新验证。

```tsx
import {
  createBrowserRouter,
  useLoaderData,
  useActionData,
  Form,
} from "react-router";
import { TodoList } from "~/components/TodoList";

createBrowserRouter([
  {
    path: "/items",
    action: action,
    loader: loader,
    Component: Items,
  },
]);

async function action({ request }) {
  const data = await request.formData();
  const todo = await fakeDb.addItem({
    title: data.get("title"),
  });
  return { ok: true };
}

// 这些数据会在 action 完成后自动重新验证……
async function loader() {
  const items = await fakeDb.getItems();
  return { items };
}

// ……因此这里的列表会自动更新
export default function Items() {
  let data = useLoaderData();
  return (
    <div>
      <List items={data.items} />
      <Form method="post" navigate={false}>
        <input type="text" name="title" />
        <button type="submit">Create Todo</button>
      </Form>
    </div>
  );
}
```

## `shouldRevalidate`

Loader 数据会在导航和表单提交等特定事件后自动重新验证。

此钩子允许你选择启用或禁用默认的重新验证行为。默认行为经过精心设计，以避免不必要的 loader 调用。

路由 loader 在以下情况下会重新验证：

- 自身路由参数发生变化
- URL 搜索参数发生任何变化
- action 被调用并返回非错误状态码后

定义此函数后，你将完全退出默认行为，可以手动控制导航和表单提交时何时重新验证 loader 数据。

```tsx
import type { ShouldRevalidateFunctionArgs } from "react-router";

function shouldRevalidate(
  arg: ShouldRevalidateFunctionArgs,
) {
  return true; // false
}

createBrowserRouter([
  {
    path: "/",
    shouldRevalidate: shouldRevalidate,
    Component: MyRoute,
  },
]);
```

[`ShouldRevalidateFunctionArgs` 参考文档 ↗](https://api.reactrouter.com/v7/interfaces/react-router.ShouldRevalidateFunctionArgs.html)

请注意，[框架模式](../modes)中的默认行为有所不同。

## `lazy`

大多数属性都可以延迟导入，以减小初始包体积。

```tsx
createBrowserRouter([
  {
    path: "/app",
    lazy: async () => {
      // 在渲染前并行加载组件和 loader
      const [Component, loader] = await Promise.all([
        import("./app"),
        import("./app-loader"),
      ]);
      return { Component, loader };
    },
  },
]);
```

---

下一节：[数据加载](./data-loading)

[loader-params]: https://api.reactrouter.com/v7/interfaces/react-router.LoaderFunctionArgs
[middleware]: ../../how-to/middleware
