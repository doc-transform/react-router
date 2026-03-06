---
title: useHref
---

# `useHref`

<details>
  <summary>类型声明</summary>

```tsx
declare function useHref(
  to: To,
  options?: { relative?: RelativeRoutingType },
): string;
```

</details>

`useHref` hook 返回一个可用于链接到给定 `to` 位置的 URL，甚至可以在 React Router 之外使用。

<docs-info>你可能有兴趣查看 `react-router-dom` 中 `<Link>` 组件的源码，了解它如何在内部使用 `useHref` 来确定自己的 `href` 值</docs-info>

<docs-info>请参阅 `useResolvedPath` 文档中的 [通配符路径][relativesplatpath] 部分，了解 `future.v7_relativeSplatPath` future flag 对通配符路由内相对 `useHref()` 行为的说明</docs-info>

[relativesplatpath]: ../hooks/use-resolved-path#splat-paths
