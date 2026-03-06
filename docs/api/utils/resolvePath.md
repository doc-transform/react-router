---
title: resolvePath
---

# resolvePath

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.resolvePath.html)

返回相对于给定 pathname 解析后的 [`Path`](https://api.reactrouter.com/v7/interfaces/react-router.Path.html) 对象。

## 函数签名

```tsx
function resolvePath(to: To, fromPathname = "/"): Path;
```

## 参数

### to

要解析的路径，可以是字符串或部分 [`Path`](https://api.reactrouter.com/v7/interfaces/react-router.Path.html) 对象。

### fromPathname

从哪个 pathname 解析路径。默认为 `/`。

## 返回值

包含解析后的 pathname、search 和 hash 的 [`Path`](https://api.reactrouter.com/v7/interfaces/react-router.Path.html) 对象。
