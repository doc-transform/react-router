---
title: useResolvedPath
---

# useResolvedPath

<!--
⚠️ ⚠️ IMPORTANT ⚠️ ⚠️

Thank you for helping improve our documentation!

This file is auto-generated from the JSDoc comments in the source
code, so please edit the JSDoc comments in the file below and this
file will be re-generated once those changes are merged.

https://github.com/remix-run/react-router/blob/main/packages/react-router/lib/hooks.tsx
-->

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useResolvedPath.html)

根据当前 [`Location`](https://api.reactrouter.com/v7/interfaces/react-router.Location.html) 解析给定 `to` 值的路径名。类似于 [`useHref`](../hooks/useHref)，但返回 [`Path`](https://api.reactrouter.com/v7/interfaces/react-router.Path.html) 对象而非字符串。

```tsx
import { useResolvedPath } from "react-router";

function SomeComponent() {
  // 如果用户在 /dashboard/profile
  let path = useResolvedPath("../accounts");
  path.pathname; // "/dashboard/accounts"
  path.search; // ""
  path.hash; // ""
}
```

## 函数签名

```tsx
function useResolvedPath(
  to: To,
  {
    relative,
  }: {
    relative?: RelativeRoutingType;
  } = ,
): Path {}
```

## 参数

### to

要解析的路径。

### options.relative

默认为 `"route"`，路由相对于路由树进行解析。设置为 `"path"` 使相对路由基于路径段进行操作。

## 返回值

包含 `pathname`、`search` 和 `hash` 的解析后的 [`Path`](https://api.reactrouter.com/v7/interfaces/react-router.Path.html) 对象。
