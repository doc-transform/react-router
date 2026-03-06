---
title: useFetchers
---

# useFetchers

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useFetchers.html)

返回所有进行中的 [`Fetcher`](https://api.reactrouter.com/v7/types/react-router.Fetcher.html) 数组。这对于应用中未创建 fetcher 但希望使用其提交数据来参与乐观 UI 的组件非常有用。

```tsx
import { useFetchers } from "react-router";

function SomeComponent() {
  const fetchers = useFetchers();
  fetchers[0].formData; // FormData
  fetchers[0].state; // 等等
  // ...
}
```

## 函数签名

```tsx
function useFetchers(): (Fetcher & {
  key: string;
})[];
```

## 返回值

所有进行中的 [`Fetcher`](https://api.reactrouter.com/v7/types/react-router.Fetcher.html) 数组，每个都包含唯一的 `key` 属性。
