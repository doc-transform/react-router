---
title: useMatch
---

# `useMatch`

<details>
  <summary>类型声明</summary>

```tsx
declare function useMatch<
  ParamKey extends ParamParseKey<Path>,
  Path extends string,
>(
  pattern: PathPattern<Path> | Path,
): PathMatch<ParamKey> | null;
```

</details>

返回相对于当前位置在给定路径上的路由匹配数据。

更多信息请参阅 [`matchPath`][matchpath]。

[matchpath]: ../utils/match-path
