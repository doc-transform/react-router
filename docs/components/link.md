---
title: Link
---

# `<Link>`

<docs-info>这是 `<Link>` 的 Web 版本。React Native 版本请[点击这里][link-native]。</docs-info>

<details>
  <summary>类型声明</summary>

```tsx
declare function Link(props: LinkProps): React.ReactElement;

interface LinkProps
  extends Omit<
    React.AnchorHTMLAttributes<HTMLAnchorElement>,
    "href"
  > {
  to: To;
  preventScrollReset?: boolean;
  relative?: "route" | "path";
  reloadDocument?: boolean;
  replace?: boolean;
  state?: any;
  viewTransition?: boolean;
}

type To = string | Partial<Path>;

interface Path {
  pathname: string;
  search: string;
  hash: string;
}
```

</details>

`<Link>` 是一个让用户通过点击或触摸导航到另一个页面的元素。在 `react-router-dom` 中，`<Link>` 渲染一个可访问的 `<a>` 元素，其 `href` 指向所链接的资源。这意味着右键点击 `<Link>` 等操作会按预期工作。你可以使用 `<Link reloadDocument>` 跳过客户端路由，让浏览器正常处理过渡（就像 `<a href>` 一样）。

```tsx
import * as React from "react";
import { Link } from "react-router-dom";

function UsersIndexPage({ users }) {
  return (
    <div>
      <h1>Users</h1>
      <ul>
        {users.map((user) => (
          <li key={user.id}>
            <Link to={user.id}>{user.name}</Link>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

相对的 `<Link to>` 值（不以 `/` 开头）相对于父路由解析，这意味着它基于渲染该 `<Link>` 的路由所匹配的 URL 路径来构建。它可以包含 `..` 来链接到层级结构中更上层的路由。在这些情况下，`..` 的工作方式与命令行的 `cd` 功能完全相同；每个 `..` 移除父路径的一个片段。

<docs-info>当当前 URL 以 `/` 结尾时，`<Link to>` 中的 `..` 与普通 `<a href>` 的行为不同。`<Link to>` 忽略尾部斜杠，每个 `..` 移除一个 URL 片段。但 `<a href>` 值在当前 URL 以 `/` 结尾和不以 `/` 结尾时对 `..` 的处理方式不同。</docs-info>

<docs-info>请参阅 `useResolvedPath` 文档中的[通配路径][relativesplatpath]部分，了解 `future.v7_relativeSplatPath` future flag 对通配路由中相对 `<Link to>` 行为的影响</docs-info>

## `relative`

默认情况下，链接相对于路由层级（`relative="route"`），因此 `..` 会从当前上下文路由上升一个 `Route` 层级。有时你可能会发现某些匹配的 URL 模式嵌套在一起没有意义，此时你希望使用相对于当前上下文路由路径的*路径*路由。你可以使用 `relative="path"` 来启用此行为：

```jsx
// Contact 和 EditContact 不共享额外的 UI 布局
<Route path="/" element={<Layout />}>
  <Route path="contacts/:id" element={<Contact />} />
  <Route
    path="contacts/:id/edit"
    element={<EditContact />}
  />
</Route>;

function EditContact() {
  // 由于 Contact 不是 EditContact 的父级，我们需要在
  // 当前上下文路由路径中上升一级，而不是在 Route
  // 层级结构中上升一级
  return (
    <Link to=".." relative="path">
      Cancel
    </Link>
  );
}
```

请注意，`relative: "path"` 仅影响相对路径的解析方式。它不会改变该相对路径解析的"起始"位置。解析始终相对于 Route 层级中的当前位置（即 `Link` 所渲染的路由）。

如果你希望针对当前 URL 而非路由层级使用路径相对路由，可以使用当前 [`location`][use-location] 和 `URL` 构造函数（注意尾部斜杠的行为）：

```js
// 假设当前 URL 为 https://remix.run/docs/en/main/start/quickstart
let location = useLocation();

// 没有尾部斜杠
new URL(".", window.origin + location.pathname);
// 'https://remix.run/docs/en/main/start/'
new URL("..", window.origin + location.pathname);
// 'https://remix.run/docs/en/main/'

// 有尾部斜杠：
new URL(".", window.origin + location.pathname + "/");
// 'https://remix.run/docs/en/main/start/quickstart/'
new URL("..", window.origin + location.pathname + "/");
// 'https://remix.run/docs/en/main/start/'
```

## `preventScrollReset`

如果你使用了 [`<ScrollRestoration>`][scrollrestoration]，此属性可以阻止点击链接时将滚动位置重置到窗口顶部。

```tsx
<Link to="?tab=one" preventScrollReset={true} />
```

这不会阻止用户使用前进/后退按钮回到该位置时恢复滚动位置，它只阻止用户点击链接时的重置。

你可能希望使用此行为的一个场景是：一组操作 url 搜索参数的标签页不在页面顶部。你不希望滚动位置跳到顶部，因为那可能会将切换的内容滚出视口！

```
      ┌─────────────────────────┐
      │                         ├──┐
      │                         │  │
      │                         │  │ 已滚出
      │                         │  │ 视口
      │                         │  │
      │                         │ ◄┘
    ┌─┴─────────────────────────┴─┐
    │                             ├─┐
    │                             │ │ 视口
    │   ┌─────────────────────┐   │ │
    │   │  tab   tab   tab    │   │ │
    │   ├─────────────────────┤   │ │
    │   │                     │   │ │
    │   │                     │   │ │
    │   │ content             │   │ │
    │   │                     │   │ │
    │   │                     │   │ │
    │   └─────────────────────┘   │ │
    │                             │◄┘
    └─────────────────────────────┘

```

## `replace`

如果你想通过 [`history.replaceState`][history-replace-state] 替换历史栈中的当前条目，而不是使用默认的 [`history.pushState`][history-push-state]，可以使用 `replace` 属性。

## `state`

`state` 属性可用于为新位置设置一个存储在 [history state][history-state] 中的有状态值。该值随后可以通过 `useLocation()` 访问。

```tsx
<Link to="new-path" state={{ some: "value" }} />
```

你可以在 "new-path" 路由上访问此状态值：

```ts
let { state } = useLocation();
```

## `reloadDocument`

`reloadDocument` 属性可用于跳过客户端路由，让浏览器正常处理过渡（就像 `<a href>` 一样）。

## `viewTransition`

`viewTransition` 属性通过将最终状态更新包装在 `document.startViewTransition()` 中，为此次导航启用[视图过渡][view-transitions]：

```jsx
<Link to={to} viewTransition>
  Click me
</Link>
```

如果你需要为此视图过渡应用特定样式，还需要使用 [`useViewTransitionState()`][use-view-transition-state] hook（或者查看 [NavLink][navlink] 中的 `transitioning` 类名和 `isTransitioning` 渲染属性）：

```jsx
function ImageLink(to) {
  const isTransitioning = useViewTransitionState(to);
  return (
    <Link to={to} viewTransition>
      <p
        style={{
          viewTransitionName: isTransitioning
            ? "image-title"
            : "",
        }}
      >
        Image Number {idx}
      </p>
      <img
        src={src}
        alt={`Img ${idx}`}
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

<docs-warning>`viewTransition` 仅在使用数据路由器时有效，参见[选择路由器][picking-a-router]</docs-warning>

[link-native]: ./link-native
[scrollrestoration]: ./scroll-restoration
[history-replace-state]: https://developer.mozilla.org/en-US/docs/Web/API/History/replaceState
[history-push-state]: https://developer.mozilla.org/en-US/docs/Web/API/History/pushState
[history-state]: https://developer.mozilla.org/en-US/docs/Web/API/History/state
[use-view-transition-state]: ../hooks//use-view-transition-state
[view-transitions]: https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API
[picking-a-router]: ../routers/picking-a-router
[navlink]: ./nav-link
[relativesplatpath]: ../hooks/use-resolved-path#splat-paths
[use-location]: ../hooks/use-location
