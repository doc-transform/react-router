---
title: MemoryRouter
---

# `<MemoryRouter>`

<details>
  <summary>类型声明</summary>

```tsx
declare function MemoryRouter(
  props: MemoryRouterProps,
): React.ReactElement;

interface MemoryRouterProps {
  basename?: string;
  children?: React.ReactNode;
  initialEntries?: InitialEntry[];
  initialIndex?: number;
  future?: FutureConfig;
}
```

</details>

`<MemoryRouter>` 将其位置信息存储在内部的数组中。与 `<BrowserHistory>` 和 `<HashHistory>` 不同，它不依赖于外部来源（如浏览器中的历史栈）。这使得它非常适合需要完全控制历史栈的场景，比如测试。

- `<MemoryRouter initialEntries>` 默认为 `["/"]`（根 `/` URL 处的单个条目）
- `<MemoryRouter initialIndex>` 默认为 `initialEntries` 的最后一个索引

> **提示：**
>
> React Router 的大多数测试都使用 `<MemoryRouter>` 作为数据源，
> 因此你可以通过[浏览我们的测试][tests]看到很多使用它的优秀示例。

```tsx
import * as React from "react";
import { create } from "react-test-renderer";
import {
  MemoryRouter,
  Routes,
  Route,
} from "react-router-dom";

describe("My app", () => {
  it("renders correctly", () => {
    let renderer = create(
      <MemoryRouter initialEntries={["/users/mjackson"]}>
        <Routes>
          <Route path="users" element={<Users />}>
            <Route path=":id" element={<UserProfile />} />
          </Route>
        </Routes>
      </MemoryRouter>,
    );

    expect(renderer.toJSON()).toMatchSnapshot();
  });
});
```

## `basename`

配置你的应用在 URL 中的特定基础路径下运行：

```jsx
function App() {
  return (
    <MemoryRouter basename="/app">
      <Routes>
        <Route path="/" /> {/* 👈 在 /app/ 下渲染 */}
      </Routes>
    </MemoryRouter>
  );
}
```

## `future`

一组可选的 [Future Flags][api-development-strategy]。我们建议尽早启用新发布的 future flag，以便将来更顺利地迁移到 v7。

```jsx
function App() {
  return (
    <MemoryRouter future={{ v7_startTransition: true }}>
      <Routes>{/*...*/}</Routes>
    </MemoryRouter>
  );
}
```

[defaultview]: https://developer.mozilla.org/en-US/docs/Web/API/Document/defaultView
[api-development-strategy]: ../guides/api-development-strategy
[tests]: https://github.com/remix-run/react-router/tree/main/packages/react-router/__tests__
