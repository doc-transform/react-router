---
title: useAsyncError
new: true
---

# `useAsyncError`

返回最近的 [`<Await>`][await] 组件的拒绝值。

```tsx [4,12]
import { useAsyncError, Await } from "react-router-dom";

function ErrorElement() {
  const error = useAsyncError();
  return (
    <p>Uh Oh, something went wrong! {error.message}</p>
  );
}

<Await
  resolve={promiseThatRejects}
  errorElement={<ErrorElement />}
/>;
```

更多信息请参阅[延迟数据指南][deferred]和 [`<Await>` 文档][await docs]。

[await docs]: ../components/await
[deferred]: ../guides/deferred
