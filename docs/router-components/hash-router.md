---
title: HashRouter
---

# `<HashRouter>`

<details>
  <summary>类型声明</summary>

```tsx
declare function HashRouter(
  props: HashRouterProps,
): React.ReactElement;

interface HashRouterProps {
  basename?: string;
  children?: React.ReactNode;
  future?: FutureConfig;
  window?: Window;
}
```

</details>

`<HashRouter>` 适用于在 Web 浏览器中当 URL 因某些原因不应该（或不能）发送到服务器时使用。这可能发生在某些共享主机场景中，你对服务器没有完全的控制权。在这些情况下，`<HashRouter>` 可以将当前位置存储在当前 URL 的 `hash` 部分中，因此它永远不会被发送到服务器。

```tsx
import * as React from "react";
import * as ReactDOM from "react-dom";
import { HashRouter } from "react-router-dom";

ReactDOM.render(
  <HashRouter>
    {/* 你的应用的其余部分放在这里 */}
  </HashRouter>,
  root,
);
```

<docs-warning>我们强烈建议你不要使用 `HashRouter`，除非你不得不这样做。</docs-warning>

## `basename`

配置你的应用在 URL 中的特定基础路径下运行：

```jsx
function App() {
  return (
    <HashRouter basename="/app">
      <Routes>
        <Route path="/" /> {/* 👈 在 /#/app/ 下渲染 */}
      </Routes>
    </HashRouter>
  );
}
```

## `future`

一组可选的 [Future Flags][api-development-strategy]。我们建议尽早启用新发布的 future flag，以便将来更顺利地迁移到 v7。

```jsx
function App() {
  return (
    <HashRouter future={{ v7_startTransition: true }}>
      <Routes>{/*...*/}</Routes>
    </HashRouter>
  );
}
```

## `window`

`HashRouter` 默认使用当前 [document 的 `defaultView`][defaultview]，但它也可以用于追踪另一个窗口 URL 的变化，例如在 `<iframe>` 中。

[defaultview]: https://developer.mozilla.org/en-US/docs/Web/API/Document/defaultView
[api-development-strategy]: ../guides/api-development-strategy
