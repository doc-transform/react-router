---
title: createContext
---

# createContext

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.createContext.html)

创建一个类型安全的 [`RouterContext`](https://api.reactrouter.com/v7/interfaces/react-router.RouterContext.html) 对象，可用于在 [`action`](../../start/framework/route-module#action)、[`loader`](../../start/framework/route-module#loader) 和[中间件](../../how-to/middleware)中存储和检索任意值。类似于 React 的 [`createContext`](https://react.dev/reference/react/createContext)，但专为 React Router 的请求/响应生命周期设计。

如果提供了 `defaultValue`，当没有为上下文设置值时，`context.get()` 将返回该默认值。否则，在没有设置值的情况下读取此上下文将抛出错误。

```tsx filename=app/context.ts
import { createContext } from "react-router";

// 为用户数据创建上下文
export const userContext = createContext<User | null>(null);
```

```tsx filename=app/middleware/auth.ts
import { getUserFromSession } from "~/auth.server";
import { userContext } from "~/context";

export const authMiddleware = async ({
  context,
  request,
}) => {
  const user = await getUserFromSession(request);
  context.set(userContext, user);
};
```

```tsx filename=app/routes/profile.tsx
import { userContext } from "~/context";

export async function loader({
  context,
}: Route.LoaderArgs) {
  const user = context.get(userContext);

  if (!user) {
    throw new Response("Unauthorized", { status: 401 });
  }

  return { user };
}
```

## 函数签名

```tsx
function createContext<T>(
  defaultValue?: T,
): RouterContext<T>;
```

## 参数

### defaultValue

上下文的可选默认值。如果没有为此上下文设置值，将返回此默认值。

## 返回值

一个 [`RouterContext`](https://api.reactrouter.com/v7/interfaces/react-router.RouterContext.html) 对象，可与 [`action`](../../start/framework/route-module#action)、[`loader`](../../start/framework/route-module#loader) 和[中间件](../../how-to/middleware)中的 `context.get()` 和 `context.set()` 一起使用。
