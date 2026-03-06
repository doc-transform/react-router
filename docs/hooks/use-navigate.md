---
title: useNavigate
---

# `useNavigate`

<details>
  <summary>类型声明</summary>

```tsx
declare function useNavigate(): NavigateFunction;

interface NavigateFunction {
  (to: To, options?: NavigateOptions): void;
  (delta: number): void;
}

interface NavigateOptions {
  replace?: boolean;
  state?: any;
  preventScrollReset?: boolean;
  relative?: RelativeRoutingType;
  flushSync?: boolean;
  viewTransition?: boolean;
}

type RelativeRoutingType = "route" | "path";
```

</details>

<docs-warning>通常在 [`loaders`][loaders] 和 [`actions`][actions] 中使用 [`redirect`][redirect] 比使用此 hook 更好</docs-warning>

`useNavigate` hook 返回一个函数，让你可以以编程方式进行导航，例如在 effect 中：

```tsx
import { useNavigate } from "react-router-dom";

function useLogoutTimer() {
  const userIsInactive = useFakeInactiveUser();
  const navigate = useNavigate();

  useEffect(() => {
    if (userIsInactive) {
      fake.logout();
      navigate("/session-timed-out");
    }
  }, [userIsInactive]);
}
```

`navigate` 函数有两种签名：

- 传递一个 `To` 值（与 `<Link to>` 类型相同）和一个可选的第二个 `options` 参数（类似于你可以传递给 [`<Link>`][link] 的 props），或者
- 传递你想要在历史栈中移动的增量。例如，`navigate(-1)` 等同于点击后退按钮

<docs-info>请参阅 `useResolvedPath` 文档中的[通配符路径][relativesplatpath]部分，了解 `future.v7_relativeSplatPath` future flag 对通配符路由内相对 `useNavigate()` 行为的说明</docs-info>

## `options.replace`

指定 `replace: true` 将导致导航替换历史栈中的当前条目，而不是添加新条目。

## `options.state`

你可以包含一个可选的 `state` 值存储在 [history state][history-state] 中，然后可以通过 [`useLocation`][use-location] 在目标路由上访问它。例如：

```tsx
navigate("/new-route", { state: { key: "value" } });
```

## `options.preventScrollReset`

使用 [`<ScrollRestoration>`][scrollrestoration] 组件时，你可以通过 `options.preventScrollReset` 禁用将滚动位置重置到页面顶部。

## `options.relative`

默认情况下，导航相对于路由层次结构（`relative: "route"`），所以 `..` 会向上一个 `Route` 层级。偶尔你可能会遇到匹配的 URL 模式不适合嵌套的情况，这时你可能更希望使用相对*路径*路由。你可以通过 `relative: "path"` 来选择此行为：

```jsx
// Contact and EditContact do not share additional UI layout
<Route path="/" element={<Layout />}>
  <Route path="contacts/:id" element={<Contact />} />
  <Route
    path="contacts/:id/edit"
    element={<EditContact />}
  />
</Route>;

function EditContact() {
  // Since Contact is not a parent of EditContact we need to go up one level
  // in the path, instead of one level in the Route hierarchy
  navigate("..", { relative: "path" });
}
```

请注意，`relative: "path"` 只影响相对路径的解析。它不会改变该相对路径解析的"起始"位置。解析始终相对于 Route 层次结构中的当前位置（即调用 `useNavigate` 的路由）。

如果你希望相对于当前 URL 而不是路由层次结构使用路径相对路由，你可以使用当前 [`location`][use-location] 和 `URL` 构造函数来实现（注意末尾斜杠的行为）：

```js
// Assume the current URL is https://remix.run/docs/en/main/start/quickstart
let location = useLocation();

// Without trailing slashes
new URL(".", window.origin + location.pathname);
// 'https://remix.run/docs/en/main/start/'
new URL("..", window.origin + location.pathname);
// 'https://remix.run/docs/en/main/'

// With trailing slashes:
new URL(".", window.origin + location.pathname + "/");
// 'https://remix.run/docs/en/main/start/quickstart/'
new URL("..", window.origin + location.pathname + "/");
// 'https://remix.run/docs/en/main/start/'
```

## `options.flushSync`

`flushSync` 选项告诉 React Router DOM 将此导航的初始状态更新包装在 [`ReactDOM.flushSync`][flush-sync] 调用中，而不是默认的 [`React.startTransition`][start-transition] 中。这允许你在更新刷新到 DOM 后立即执行同步 DOM 操作。

<docs-warning>`flushSync` 仅在使用数据路由器时有效，参见[选择路由器][picking-a-router]</docs-warning>

## `options.viewTransition`

`viewTransition` 选项通过将最终状态更新包装在 `document.startViewTransition()` 中来为此导航启用[视图过渡][view-transitions]。如果你需要为此视图过渡应用特定的样式，你还需要使用 [`useViewTransitionState()`][use-view-transition-state]。

<docs-warning>`viewTransition` 仅在使用数据路由器时有效，参见[选择路由器][picking-a-router]</docs-warning>

[link]: ../components/link
[redirect]: ../fetch/redirect
[loaders]: ../route/loader
[actions]: ../route/action
[history-state]: https://developer.mozilla.org/en-US/docs/Web/API/History/state
[scrollrestoration]: ../components/scroll-restoration
[use-location]: ../hooks/use-location
[use-view-transition-state]: ../hooks//use-view-transition-state
[view-transitions]: https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API
[picking-a-router]: ../routers/picking-a-router
[flush-sync]: https://react.dev/reference/react-dom/flushSync
[start-transition]: https://react.dev/reference/react/startTransition
[relativesplatpath]: ../hooks/use-resolved-path#splat-paths
