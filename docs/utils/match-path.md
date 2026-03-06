---
title: matchPath
---

# `matchPath`

<details>
  <summary>Type declaration</summary>

```tsx
declare function matchPath<
  ParamKey extends string = string,
>(
  pattern: PathPattern | string,
  pathname: string,
): PathMatch<ParamKey> | null;

interface PathMatch<ParamKey extends string = string> {
  params: Params<ParamKey>;
  pathname: string;
  pattern: PathPattern;
}

interface PathPattern {
  path: string;
  caseSensitive?: boolean;
  end?: boolean;
}
```

</details>

`matchPath` 将路由路径模式与 URL pathname 进行匹配，并返回匹配的信息。当你需要手动运行路由器的匹配算法来判断某个路由路径是否匹配时，这非常有用。如果模式与给定的 pathname 不匹配，则返回 `null`。

[`useMatch` hook][usematch] 在内部使用此函数来匹配相对于当前位置的路由路径。

[usematch]: ../hooks/use-match
