---
title: useLoaderData
new: true
---

# `useLoaderData`

此 hook 提供路由 loader 返回的值。

```tsx lines=[4,12]
import {
  createBrowserRouter,
  RouterProvider,
  useLoaderData,
} from "react-router-dom";

function loader() {
  return fetchFakeAlbums();
}

export function Albums() {
  const albums = useLoaderData();
  // ...
}

const router = createBrowserRouter([
  {
    path: "/",
    loader: loader,
    element: <Albums />,
  },
]);

ReactDOM.createRoot(el).render(
  <RouterProvider router={router} />,
);
```

路由 [action][actions] 被调用后，数据会自动重新验证并返回 loader 的最新结果。

请注意，`useLoaderData` _不会发起 fetch_。它只是读取 React Router 内部管理的 fetch 结果，所以你不需要担心它在路由之外的原因导致重新渲染时会重新获取数据。

这也意味着返回的数据在渲染之间是稳定的，所以你可以安全地将其传递给 React hooks（如 `useEffect`）的依赖数组。它只在 action 或某些导航后 loader 再次被调用时才会改变。在这些情况下，标识会改变（即使值不变）。

你可以在任何组件或任何自定义 hook 中使用此 hook，不仅限于 Route 元素。它会返回上下文中最近的路由的数据。

要获取页面上任何活跃路由的数据，请参阅 [`useRouteLoaderData`][routeloaderdata]。

[actions]: ../components/route#action
[routeloaderdata]: ./use-route-loader-data
