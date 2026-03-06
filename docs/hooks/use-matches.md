---
title: useMatches
new: true
---

# `useMatches`

返回页面上当前的路由匹配项。这在父布局中创建抽象以访问其子路由数据时最为有用。

```js
import { useMatches } from "react-router-dom";

function SomeComponent() {
  const matches = useMatches();
  // [match1, match2, ...]
}
```

一个 `match` 具有以下结构：

```js
{
  // 路由 id
  id,

  // 路由匹配的 URL 部分
  pathname,

  // 来自 loader 的数据
  data,

  // 从 URL 解析的参数
  params,

  // <Route handle> 以及任何应用特定数据
  handle,
};
```

将 `<Route handle>` 与 `useMatches` 配合使用非常强大，因为你可以在路由 `handle` 上放置任何你想要的内容，并在任何地方访问 `useMatches`。

<docs-warning>`useMatches` 仅在数据路由器（如 [`createBrowserRouter`][createbrowserrouter]）中有效，因为它们预先知道完整的路由树并可以提供所有当前的匹配项。此外，`useMatches` 不会匹配任何后代路由树，因为路由器不知道后代路由。</docs-warning>

## 面包屑导航

这里的典型用例是在父布局中添加面包屑导航，使用子路由的数据。

```jsx filename=app.jsx
<Route element={<Root />}>
  <Route
    path="messages"
    element={<Messages />}
    loader={loadMessages}
    handle={{
      // 你可以在路由 handle 上放置任何你想要的内容
      // 这里我们使用 "crumb" 并返回一些元素，
      // 这是我们将在面包屑导航中渲染的内容
      // 对于这个路由
      crumb: () => <Link to="/messages">Messages</Link>,
    }}
  >
    <Route
      path="conversation/:id"
      element={<Thread />}
      loader={loadThread}
      handle={{
        // `crumb` 是你自己的抽象，我们决定
        // 将其设置为一个函数，因此我们可以传递
        // 来自 loader 的数据到它，因此我们的
        // 面包屑导航由动态内容组成
        crumb: (data) => <span>{data.threadName}</span>,
      }}
    />
  </Route>
</Route>
```

现在我们可以创建一个 `Breadcrumbs` 组件，利用我们自定义的 `crumb` 抽象以及 `useMatches` 和 `handle`。

```tsx filename=components/breadcrumbs.jsx
function Breadcrumbs() {
  let matches = useMatches();
  let crumbs = matches
    // 首先去掉任何没有 handle 和 crumb 的匹配项
    .filter((match) => Boolean(match.handle?.crumb))
    // 现在将它们映射为元素数组，将 loader
    // 数据传递给每个元素
    .map((match) => match.handle.crumb(match.data));

  return (
    <ol>
      {crumbs.map((crumb, index) => (
        <li key={index}>{crumb}</li>
      ))}
    </ol>
  );
}
```

现在你可以在任何你想要的地方渲染 `<Breadcrumbs/>`，大概是在根组件中。

[createbrowserrouter]: ../routers/create-browser-router
