---
title: useAsyncValue
---

# useAsyncValue

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useAsyncValue.html)

返回最近的 [`<Await>`](../components/Await) 组件的已解析 Promise 值。

```tsx
function SomeDescendant() {
  const value = useAsyncValue();
  // ...
}

// 在应用的某处
<Await resolve={somePromise}>
  <SomeDescendant />
</Await>;
```

## 函数签名

```tsx
function useAsyncValue(): unknown;
```

## 返回值

最近的 [`Await`](../components/Await) 组件的已解析值。
