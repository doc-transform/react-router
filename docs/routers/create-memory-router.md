---
title: createMemoryRouter
new: true
---

# `createMemoryRouter`

内存路由器不使用浏览器的 history，而是在内存中管理自己的历史栈。它主要用于测试和组件开发工具（如 Storybook），但也可以在任何非浏览器环境中运行 React Router。

```jsx lines=[2-3,24-27]
import {
  RouterProvider,
  createMemoryRouter,
} from "react-router-dom";
import * as React from "react";
import {
  render,
  waitFor,
  screen,
} from "@testing-library/react";
import "@testing-library/jest-dom";
import CalendarEvent from "./routes/event";

test("event route", async () => {
  const FAKE_EVENT = { name: "test event" };
  const routes = [
    {
      path: "/events/:id",
      element: <CalendarEvent />,
      loader: () => FAKE_EVENT,
    },
  ];

  const router = createMemoryRouter(routes, {
    initialEntries: ["/", "/events/123"],
    initialIndex: 1,
  });

  render(<RouterProvider router={router} />);

  await waitFor(() => screen.getByRole("heading"));
  expect(screen.getByRole("heading")).toHaveTextContent(
    FAKE_EVENT.name,
  );
});
```

## 类型声明

```tsx
function createMemoryRouter(
  routes: RouteObject[],
  opts?: {
    basename?: string;
    future?: FutureConfig;
    hydrationData?: HydrationState;
    initialEntries?: InitialEntry[];
    initialIndex?: number;
  },
): RemixRouter;
```

## `initialEntries`

历史栈中的初始条目。这允许你在测试（或应用）中以多个位置已在历史栈中的状态启动（用于测试后退导航等）。

```tsx
createMemoryRouter(routes, {
  initialEntries: ["/", "/events/123"],
});
```

## `initialIndex`

要渲染的历史栈中的初始索引。这允许你在特定条目处开始测试。默认为 `initialEntries` 中的最后一个条目。

```tsx lines=[3]
createMemoryRouter(routes, {
  initialEntries: ["/", "/events/123"],
  initialIndex: 1, // 从 "/events/123" 开始
});
```

## 其他属性

关于所有其他属性，请参阅 [`createBrowserRouter`][createbrowserrouter]。

[createbrowserrouter]: ./create-browser-router
