---
title: useRevalidator
---

# useRevalidator

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useRevalidator.html)

用于在正常数据变更之外的原因重新验证页面数据，如 [`Window` 聚焦](https://developer.mozilla.org/en-US/docs/Web/API/Window/focus_event)或定时轮询。

注意：在 action 之后页面数据会自动重新验证。如果你发现自己在用户交互中使用此 Hook 进行普通的 CRUD 操作，那你可能没有充分利用其他 API，如 [`useFetcher`](../hooks/useFetcher)、[`Form`](../components/Form)、[`useSubmit`](../hooks/useSubmit)，它们会自动完成这些操作。

```tsx
import { useRevalidator } from "react-router";

function WindowFocusRevalidator() {
  const revalidator = useRevalidator();

  useFakeWindowFocus(() => {
    revalidator.revalidate();
  });

  return (
    <div hidden={revalidator.state === "idle"}>
      Revalidating...
    </div>
  );
}
```

## 函数签名

```tsx
function useRevalidator(): {
  revalidate: () => Promise<void>;
  state: DataRouter["state"]["revalidation"];
};
```

## 返回值

包含 `revalidate` 函数和当前重新验证 `state` 的对象。
