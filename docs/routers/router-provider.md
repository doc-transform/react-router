---
title: RouterProvider
new: true
---

# `<RouterProvider>`

<details>
  <summary>类型声明</summary>

```tsx
declare function RouterProvider(
  props: RouterProviderProps,
): React.ReactElement;

interface RouterProviderProps {
  fallbackElement?: React.ReactNode;
  router: Router;
  future?: Partial<FutureConfig>;
}
```

</details>

所有[数据路由器][picking-a-router]对象都传递给此组件来渲染你的应用并启用其余的数据 API。

<docs-info>由于数据 API 的设计将获取和渲染解耦，你应该在 React 树之外使用静态定义的路由集合来创建路由器。关于此设计的更多信息，请参阅 [Remixing React Router][remixing-react-router] 博客文章和 [When to Fetch][when-to-fetch] 会议演讲。</docs-info>

```jsx lines=[24]
import {
  createBrowserRouter,
  RouterProvider,
} from "react-router-dom";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    children: [
      {
        path: "dashboard",
        element: <Dashboard />,
      },
      {
        path: "about",
        element: <About />,
      },
    ],
  },
]);

ReactDOM.createRoot(document.getElementById("root")).render(
  <RouterProvider
    router={router}
    fallbackElement={<BigSpinner />}
  />,
);
```

## `fallbackElement`

如果你没有对应用进行服务端渲染，`createBrowserRouter` 在挂载时会启动所有匹配的路由 loader。在此期间，你可以提供一个 `fallbackElement` 给用户一些应用正在工作的提示。让静态托管的 TTFB 物有所值！

```tsx
<RouterProvider
  router={router}
  fallbackElement={<SpinnerOfDoom />}
/>
```

## `future`

一组可选的 [Future Flags][api-development-strategy]。我们建议尽早启用新发布的 future flag，以便将来更顺利地迁移到 v7。

```jsx
function App() {
  return (
    <RouterProvider
      router={router}
      future={{ v7_startTransition: true }}
    />
  );
}
```

[picking-a-router]: ./picking-a-router
[api-development-strategy]: ../guides/api-development-strategy
[remixing-react-router]: https://remix.run/blog/remixing-react-router
[when-to-fetch]: https://www.youtube.com/watch?v=95B8mnhzoCM
