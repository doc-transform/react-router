---
title: useFetcher
---

# useFetcher

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useFetcher.html)

用于创建复杂的动态用户界面，这些界面需要多个并发的数据交互，但不触发导航。

Fetcher 跟踪自己独立的状态，可用于加载数据、提交表单，以及与 [`action`](../../start/framework/route-module#action) 和 [`loader`](../../start/framework/route-module#loader) 函数进行交互。

```tsx
import { useFetcher } from "react-router"

function SomeComponent() {
  let fetcher = useFetcher()

  // fetcher 上有各种状态
  fetcher.state // "idle" | "loading" | "submitting"
  fetcher.data // action 或 loader 返回的数据

  // 渲染表单
  <fetcher.Form method="post" />

  // 加载数据
  fetcher.load("/some/route")

  // 提交数据
  fetcher.submit(someFormRef, { method: "post" })
  fetcher.submit(someData, {
    method: "post",
    encType: "application/json"
  })

  // 重置 fetcher
  fetcher.reset()
}
```

## 函数签名

```tsx
function useFetcher<T = any>({
  key,
}: {
  key?: string;
} = ): FetcherWithComponents<SerializeFrom<T>> {}
```

## 参数

### options.key

用于标识 fetcher 的唯一 key。

默认情况下，`useFetcher` 会生成一个仅限于该组件的唯一 fetcher。如果你想用自己的 key 标识一个 fetcher，以便在应用的其他地方访问它，可以使用 `key` 选项：

```tsx
function SomeComp() {
  let fetcher = useFetcher({ key: "my-key" });
  // ...
}

// 在其他地方
function AnotherComp() {
  // 这将是同一个 fetcher，在应用中共享状态
  let fetcher = useFetcher({ key: "my-key" });
  // ...
}
```

## 返回值

一个 [`FetcherWithComponents`](https://api.reactrouter.com/v7/types/react-router.FetcherWithComponents.html) 对象，包含 fetcher 的状态、数据，以及用于提交表单和加载数据的组件。
