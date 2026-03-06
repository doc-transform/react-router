---
title: useLoaderData
---

# useLoaderData

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useLoaderData.html)

返回最近路由的 [`loader`](../../start/framework/route-module#loader) 或 [`clientLoader`](../../start/framework/route-module#clientloader) 返回的数据。

```tsx
import { useLoaderData } from "react-router";

export async function loader() {
  return await fakeDb.invoices.findAll();
}

export default function Invoices() {
  let invoices = useLoaderData<typeof loader>();
  // ...
}
```

## 函数签名

```tsx
function useLoaderData<T = any>(): SerializeFrom<T>;
```

## 返回值

路由的 [`loader`](../../start/framework/route-module#loader) 或 [`clientLoader`](../../start/framework/route-module#clientloader) 函数返回的数据。
