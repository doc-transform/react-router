---
title: useMatch
---

# useMatch

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useMatch.html)

如果给定的模式匹配当前 URL，则返回一个 [`PathMatch`](https://api.reactrouter.com/v7/interfaces/react-router.PathMatch.html) 对象。这对于需要知道"激活"状态的组件很有用，例如 [`<NavLink>`](../components/NavLink)。

## 函数签名

```tsx
function useMatch<
  ParamKey extends ParamParseKey<Path>,
  Path extends string,
>(
  pattern: PathPattern<Path> | Path,
): PathMatch<ParamKey> | null;
```

## 参数

### pattern

要匹配当前 [`Location`](https://api.reactrouter.com/v7/interfaces/react-router.Location.html) 的模式。

## 返回值

如果模式匹配则返回路径匹配对象，否则返回 `null`。
