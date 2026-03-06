---
title: useOutletContext
---

# useOutletContext

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useOutletContext.html)

返回父路由 [`<Outlet context>`](../components/Outlet) 的值。

父路由经常需要管理状态或其他需要与子路由共享的值。你当然可以创建自己的 [context provider](https://react.dev/learn/passing-data-deeply-with-context)，但这是一个非常常见的场景，因此它已内置在 [`<Outlet>`](../components/Outlet) 中。

```tsx
// 父路由
function Parent() {
  const [count, setCount] = React.useState(0);
  return <Outlet context={[count, setCount]} />;
}
```

```tsx
// 子路由
import { useOutletContext } from "react-router";

function Child() {
  const [count, setCount] = useOutletContext();
  const increment = () => setCount((c) => c + 1);
  return <button onClick={increment}>{count}</button>;
}
```

如果你使用 TypeScript，我们建议父组件提供一个自定义 Hook 来访问上下文值。这样可以让使用者更容易获取好的类型推导、控制使用者，并知道谁在消费上下文值。

以下是一个更实际的例子：

```tsx filename=src/routes/dashboard.tsx lines=[14,20]
import { useState } from "react";
import { Outlet, useOutletContext } from "react-router";

import type { User } from "./types";

type ContextType = { user: User | null };

export default function Dashboard() {
  const [user, setUser] = useState<User | null>(null);

  return (
    <div>
      <h1>Dashboard</h1>
      <Outlet context={{ user } satisfies ContextType} />
    </div>
  );
}

export function useUser() {
  return useOutletContext<ContextType>();
}
```

```tsx filename=src/routes/dashboard/messages.tsx lines=[1,4]
import { useUser } from "../dashboard";

export default function DashboardMessages() {
  const { user } = useUser();
  return (
    <div>
      <h2>Messages</h2>
      <p>Hello, {user.name}!</p>
    </div>
  );
}
```

## 函数签名

```tsx
function useOutletContext<Context = unknown>(): Context;
```

## 返回值

传递给父 [`Outlet`](../components/Outlet) 组件的上下文值。
