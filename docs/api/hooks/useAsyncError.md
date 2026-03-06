---
title: useAsyncError
---

# useAsyncError

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useAsyncError.html)

返回最近的 [`<Await>`](../components/Await) 组件的 Promise 拒绝值。

```tsx
import { Await, useAsyncError } from "react-router";

function ErrorElement() {
  const error = useAsyncError();
  return (
    <p>Uh Oh, something went wrong! {error.message}</p>
  );
}

// 在应用的某处
<Await
  resolve={promiseThatRejects}
  errorElement={<ErrorElement />}
/>;
```

## 函数签名

```tsx
function useAsyncError(): unknown;
```

## 返回值

最近的 [`Await`](../components/Await) 组件中抛出的错误。
