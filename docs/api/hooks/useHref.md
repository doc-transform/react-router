---
title: useHref
---

# useHref

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useHref.html)

根据当前 [`Location`](https://api.reactrouter.com/v7/interfaces/react-router.Location.html) 解析 URL。

```tsx
import { useHref } from "react-router";

function SomeComponent() {
  let href = useHref("some/where");
  // "/resolved/some/where"
}
```

## 函数签名

```tsx
function useHref(
  to: To,
  {
    relative,
  }: {
    relative?: RelativeRoutingType;
  } = ,
): string {}
```

## 参数

### to

要解析的路径。

### options.relative

默认为 `"route"`，路由相对于路由树进行解析。设置为 `"path"` 使相对路由基于路径段进行操作。

## 返回值

解析后的 href 字符串。
