---
title: Router
---

# `<Router>`

<details>
  <summary>类型声明</summary>

```tsx
declare function Router(
  props: RouterProps,
): React.ReactElement | null;

interface RouterProps {
  basename?: string;
  children?: React.ReactNode;
  location: Partial<Location> | string;
  navigationType?: NavigationType;
  navigator: Navigator;
  static?: boolean;
}
```

</details>

`<Router>` 是所有路由器组件（如 `<BrowserRouter>` 和 `<StaticRouter>`）共享的底层接口。在 React 中，`<Router>` 是一个[上下文 Provider][context]，为应用的其余部分提供路由信息。

你可能永远不需要手动渲染 `<Router>`。相反，你应该根据你的环境使用更高级别的路由器之一。在任何给定的应用中，你只需要一个路由器。

`<Router basename>` 属性可用于使应用中的所有路由和链接都相对于它们共享的 URL 路径名的"基础"部分。这在使用 React Router 仅渲染大型应用的一部分时，或者当你的应用有多个入口点时非常有用。basename 不区分大小写。

[context]: https://reactjs.org/docs/context.html#contextprovider
