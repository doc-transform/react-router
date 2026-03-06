---
title: useViewTransitionState
---

# `useViewTransitionState`

<details>
  <summary>类型声明</summary>

```tsx
declare function useViewTransitionState(
  to: To,
  opts: { relative?: "route" : "path" } = {}
): boolean;

type To = string | Partial<Path>;

interface Path {
  pathname: string;
  search: string;
  hash: string;
}
```

</details>

当存在到指定位置的活跃[视图过渡][view-transitions]时，此 hook 返回 `true`。这可以用于对元素应用更细粒度的样式，以进一步自定义视图过渡。这要求已通过 `Link`（或 `Form`、`navigate`、`submit` 调用）上的 [viewTransition][link-view-transition] prop 为给定导航启用了视图过渡。

假设在列表中点击一个图片，你需要将其展开为目标页面上的主图：

```jsx
function NavImage({ src, alt, id }) {
  const to = `/images/${id}`;
  const isTransitioning = useViewTransitionState(to);
  return (
    <Link to={to} viewTransition>
      <img
        src={src}
        alt={alt}
        style={{
          viewTransitionName: isTransitioning
            ? "image-expand"
            : "",
        }}
      />
    </Link>
  );
}
```

[link-view-transition]: ../components/link#viewtransition
[view-transitions]: https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API
