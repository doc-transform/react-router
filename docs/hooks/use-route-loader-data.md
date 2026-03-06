---
title: useRouteLoaderData
new: true
---

# `useRouteLoaderData`

此 hook 使当前渲染的任何路由的数据在树中的任何位置都可用。这对于深层组件需要更上层路由的数据，以及父路由需要更深层子路由的数据都很有用。

<docs-warning>此功能仅在使用数据路由器时有效，参见[选择路由器][pickingarouter]</docs-warning>

```tsx
import { useRouteLoaderData } from "react-router-dom";

function SomeComp() {
  const user = useRouteLoaderData("root");
  // ...
}
```

React Router 使用确定性的、自动生成的路由 id 在内部存储数据，但你可以提供自己的路由 id 来使此 hook 更容易使用。考虑一个定义了 id 的路由器：

```tsx [6]
createBrowserRouter([
  {
    path: "/",
    loader: () => fetchUser(),
    element: <Root />,
    id: "root",
    children: [
      {
        path: "jobs/:jobId",
        loader: loadJob,
        element: <JobListing />,
      },
    ],
  },
]);
```

现在用户数据在应用的任何其他地方都可用。

```tsx
const user = useRouteLoaderData("root");
```

唯一可用的数据是当前渲染的路由的数据。如果你请求当前未渲染的路由的数据，此 hook 将返回 `undefined`。

[pickingarouter]: ../routers/picking-a-router
