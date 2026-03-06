---
title: 数据加载
order: 4
---

# 数据加载

[MODES: data]

## 提供数据

数据通过路由 loader 提供给路由组件：

```tsx
createBrowserRouter([
  {
    path: "/",
    loader: async () => {
      // 从这里返回数据
      return { records: await getSomeRecords() };
    },
    Component: MyRoute,
  },
]);
```

## 访问数据

在路由组件中通过 `useLoaderData` 访问数据。

```tsx
import { useLoaderData } from "react-router";

function MyRoute() {
  const { records } = useLoaderData();
  return <div>{records.length}</div>;
}
```

当用户在路由之间导航时，loader 会在路由组件渲染之前被调用。

---

下一节：[操作（Action）](./actions)
