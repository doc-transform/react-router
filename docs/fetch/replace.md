---
title: replace
new: true
---

# `replace`

这是对 [`redirect`][redirect] 的一个小型包装，它会使用 `history.replaceState` 而不是 `history.pushState` 来触发客户端重定向到新位置。

## 类型声明

```ts
type RedirectFunction = (
  url: string,
  init?: number | ResponseInit,
) => Response;
```

## `url`

要重定向到的 URL。

```js
replace("/otherapp/login");
```

## `init`

`status` 或响应中要使用的 [Response][response] 选项。

[response]: https://developer.mozilla.org/en-US/docs/Web/API/Response/Response
[redirect]: ./redirect
