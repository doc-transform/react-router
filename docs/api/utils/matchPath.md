---
title: matchPath
---

# matchPath

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.matchPath.html)

对 URL pathname 执行模式匹配，并返回匹配信息。

## 函数签名

```tsx
function matchPath<
  ParamKey extends ParamParseKey<Path>,
  Path extends string,
>(
  pattern: PathPattern<Path> | Path,
  pathname: string,
): PathMatch<ParamKey> | null;
```

## 参数

### pattern

要与 URL pathname 匹配的模式。可以是字符串或 [`PathPattern`](https://api.reactrouter.com/v7/interfaces/react-router.PathPattern.html) 对象。如果提供字符串，将被视为 `caseSensitive` 设为 `false` 且 `end` 设为 `true` 的模式。

### pathname

要与模式匹配的 URL pathname。

## 返回值

如果模式匹配 pathname 则返回路径匹配对象，否则返回 `null`。
