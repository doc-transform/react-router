---
title: data
---

# data

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.data.html)

创建包含 `headers`/`status` 的"响应"，而不强制序列化为实际的 [`Response`](https://developer.mozilla.org/en-US/docs/Web/API/Response)。

```tsx
import { data } from "react-router";

export async function action({ request }: Route.ActionArgs) {
  let formData = await request.formData();
  let item = await createItem(formData);
  return data(item, {
    headers: { "X-Custom-Header": "value" }
    status: 201,
  });
}
```

## 函数签名

```tsx
function data<D>(data: D, init?: number | ResponseInit);
```

## 参数

### data

要包含在响应中的数据。

### init

要包含在响应中的状态码或 `ResponseInit` 对象。

## 返回值

包含数据和响应初始化信息的 `DataWithResponseInit` 实例。
