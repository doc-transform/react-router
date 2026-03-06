---
title: useRouteLoaderData
---

# useRouteLoaderData

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useRouteLoaderData.html)

通过路由 ID 返回指定路由的 [`loader`](../../start/framework/route-module#loader) 数据。

路由 ID 是自动创建的，它们就是路由文件相对于 app 文件夹的路径（不含扩展名）。

| 路由文件名                   | 路由 ID                |
| ---------------------------- | ---------------------- |
| `app/root.tsx`               | `"root"`               |
| `app/routes/teams.tsx`       | `"routes/teams"`       |
| `app/whatever/teams.$id.tsx` | `"whatever/teams.$id"` |

```tsx
import { useRouteLoaderData } from "react-router";

function SomeComponent() {
  const { user } = useRouteLoaderData("root");
}

// 你也可以在 routes.ts 文件中手动指定路由 ID：
route("/", "containers/app.tsx", { id: "app" });
useRouteLoaderData("app");
```

## 函数签名

```tsx
function useRouteLoaderData<T = any>(
  routeId: string,
): SerializeFrom<T> | undefined;
```

## 参数

### routeId

要返回 loader 数据的路由 ID。

## 返回值

指定路由的 [`loader`](../../start/framework/route-module#loader) 函数返回的数据，如果未找到则返回 `undefined`。
