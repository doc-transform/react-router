---
title: useInRouterContext
---

# `useInRouterContext`

<details>
  <summary>类型声明</summary>

```tsx
declare function useInRouterContext(): boolean;
```

</details>

`useInRouterContext` hook 在组件在 `<Router>` 的上下文中渲染时返回 `true`，否则返回 `false`。这对于某些需要知道自己是否在 React Router 应用的上下文中渲染的第三方扩展很有用。
