---
title: createSearchParams
---

# `createSearchParams`

<details>
  <summary>Type declaration</summary>

```tsx
declare function createSearchParams(
  init?: URLSearchParamsInit,
): URLSearchParams;
```

</details>

`createSearchParams` 是 [`new URLSearchParams(init)`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/URLSearchParams) 的轻量封装，增加了对值为数组的对象的支持。这与 `useSearchParams` 内部使用的函数相同，用于从 `URLSearchParamsInit` 值创建 `URLSearchParams` 对象。
