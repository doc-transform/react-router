---
title: generatePath
---

# generatePath

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.generatePath.html)

返回一个插入参数后的路径。

```tsx
import { generatePath } from "react-router";

generatePath("/users/:id", { id: "123" }); // "/users/123"
```

## 函数签名

```tsx
function generatePath<Path extends string>(
  originalPath: Path,
  params: {
    [key in PathParam<Path>]: string | null;
  } =  as any,
): string {}
```

## 参数

### originalPath

要生成的原始路径。

### params

要插入到路径中的参数。

## 返回值

插入参数后生成的路径。
