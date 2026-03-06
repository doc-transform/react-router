---
title: useActionData
---

# useActionData

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useActionData.html)

返回最近一次 `POST` 导航表单提交的 [`action`](../../start/framework/route-module#action) 数据，如果没有提交过则返回 `undefined`。

```tsx
import { Form, useActionData } from "react-router";

export async function action({ request }) {
  const body = await request.formData();
  const name = body.get("visitorsName");
  return { message: `Hello, ${name}` };
}

export default function Invoices() {
  const data = useActionData();
  return (
    <Form method="post">
      <input type="text" name="visitorsName" />
      {data ? data.message : "Waiting..."}
    </Form>
  );
}
```

## 函数签名

```tsx
function useActionData<T = any>():
  | SerializeFrom<T>
  | undefined;
```

## 返回值

路由 [`action`](../../start/framework/route-module#action) 函数返回的数据，如果没有调用过 [`action`](../../start/framework/route-module#action) 则返回 `undefined`。
