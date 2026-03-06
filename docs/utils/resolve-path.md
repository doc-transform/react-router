---
title: resolvePath
---

# `resolvePath`

<details>
  <summary>Type declaration</summary>

```tsx
declare function resolvePath(
  to: To,
  fromPathname?: string,
): Path;

type To = string | Partial<Path>;

interface Path {
  pathname: string;
  search: string;
  hash: string;
}
```

</details>

`resolvePath` 将给定的 `To` 值解析为具有绝对 `pathname` 的实际 `Path` 对象。当你需要知道相对 `To` 值的确切路径时，这非常有用。例如，`<Link>` 组件使用此函数来知道它指向的实际 URL。

[`useResolvedPath` hook][useresolvedpath] 在内部使用 `resolvePath` 来解析 pathname。如果 `to` 包含 pathname，则相对于当前路由的 pathname 进行解析。否则，相对于当前 URL（`location.pathname`）进行解析。

[useresolvedpath]: ../hooks/use-resolved-path
