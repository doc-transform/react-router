---
title: useOutletContext
---

# `useOutletContext`

<details>
  <summary>类型声明</summary>

```tsx
declare function useOutletContext<
  Context = unknown,
>(): Context;
```

</details>

父路由通常管理你想与子路由共享的状态或其他值。如果你愿意，可以创建自己的[上下文提供者](https://react.dev/learn/passing-data-deeply-with-context)，但这是一种非常常见的情况，所以它已经内置在 `<Outlet />` 中：

```tsx lines=[3]
function Parent() {
  const [count, setCount] = React.useState(0);
  return <Outlet context={[count, setCount]} />;
}
```

```tsx lines=[4]
import { useOutletContext } from "react-router-dom";

function Child() {
  const [count, setCount] = useOutletContext();
  const increment = () => setCount((c) => c + 1);
  return <button onClick={increment}>{count}</button>;
}
```

如果你使用 TypeScript，我们建议父组件提供一个自定义 hook 来访问上下文值。这使消费者更容易获得好的类型提示，控制消费者，以及了解谁在消费上下文值。以下是一个更实际的例子：

```tsx filename=src/routes/dashboard.tsx lines=[13,19]
import * as React from "react";
import type { User } from "./types";
import { Outlet, useOutletContext } from "react-router-dom";

type ContextType = { user: User | null };

export default function Dashboard() {
  const [user, setUser] = React.useState<User | null>(null);

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
