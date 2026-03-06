---
title: BrowserRouter
---

# `<BrowserRouter>`

<details>
  <summary>类型声明</summary>

```tsx
declare function BrowserRouter(
  props: BrowserRouterProps,
): React.ReactElement;

interface BrowserRouterProps {
  basename?: string;
  children?: React.ReactNode;
  future?: FutureConfig;
  window?: Window;
}
```

</details>

`<BrowserRouter>` 使用简洁的 URL 将当前位置存储在浏览器的地址栏中，并使用浏览器内置的历史栈进行导航。

```tsx
import * as React from "react";
import { createRoot } from "react-dom/client";
import { BrowserRouter } from "react-router-dom";

const root = createRoot(document.getElementById("root"));

root.render(
  <BrowserRouter>
    {/* 你的应用的其余部分放在这里 */}
  </BrowserRouter>,
);
```

## `basename`

配置你的应用在 URL 中的特定基础路径下运行：

```jsx
function App() {
  return (
    <BrowserRouter basename="/app">
      <Routes>
        <Route path="/" /> {/* 👈 在 /app/ 下渲染 */}
      </Routes>
    </BrowserRouter>
  );
}
```

## `future`

一组可选的 [Future Flags][api-development-strategy]。我们建议尽早启用新发布的 future flag，以便将来更顺利地迁移到 v7。

```jsx
function App() {
  return (
    <BrowserRouter future={{ v7_startTransition: true }}>
      <Routes>{/*...*/}</Routes>
    </BrowserRouter>
  );
}
```

## `window`

`BrowserRouter` 默认使用当前 [document 的 `defaultView`][defaultview]，但它也可以用于追踪另一个窗口 URL 的变化，例如在 `<iframe>` 中。

[defaultview]: https://developer.mozilla.org/en-US/docs/Web/API/Document/defaultView
[api-development-strategy]: ../guides/api-development-strategy
