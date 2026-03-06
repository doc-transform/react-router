---
title: ScrollRestoration
new: true
---

# `<ScrollRestoration />`

此组件会在 location 变化且 loader 完成后模拟浏览器的滚动恢复行为，确保滚动位置恢复到正确的位置，即使跨域也能正常工作。

<docs-warning>此功能仅在使用数据路由器时有效，参见[选择路由器][pickingarouter]</docs-warning>

你应该只渲染一个此组件，建议在应用的根路由中渲染：

```tsx [1,7]
import { ScrollRestoration } from "react-router-dom";

function RootRouteComponent() {
  return (
    <div>
      {/* ... */}
      <ScrollRestoration />
    </div>
  );
}
```

## `getKey`

可选属性，定义 React Router 用于恢复滚动位置的 key。

```tsx
<ScrollRestoration
  getKey={(location, matches) => {
    // 默认行为
    return location.key;
  }}
/>
```

默认使用 `location.key`，模拟浏览器在没有客户端路由时的默认行为。用户可以在历史栈中多次导航到同一 URL，每个条目都有自己的滚动位置需要恢复。

某些应用可能希望覆盖此行为，基于其他条件来恢复位置。考虑一个有四个主要页面的社交应用：

- "/home"
- "/messages"
- "/notifications"
- "/search"

如果用户从 "/home" 开始，向下滚动一些，点击导航菜单中的 "messages"，然后点击导航菜单中的 "home"（不是后退按钮！），历史栈中将有三个条目：

```
1. /home
2. /messages
3. /home
```

默认情况下，React Router（和浏览器）会为 `1` 和 `3` 存储两个不同的滚动位置，即使它们的 URL 相同。这意味着当用户从 `2` → `3` 导航时，滚动位置会跳到顶部，而不是恢复到 `1` 中的位置。

一个可靠的产品决策是：无论用户如何到达 home feed（后退按钮或新链接点击），都保持用户的滚动位置。为此，你可以使用 `location.pathname` 作为 key。

```tsx
<ScrollRestoration
  getKey={(location, matches) => {
    return location.pathname;
  }}
/>
```

或者你可能只想对某些路径使用 pathname，而对其他所有路径使用正常行为：

```tsx
<ScrollRestoration
  getKey={(location, matches) => {
    const paths = ["/home", "/notifications"];
    return paths.includes(location.pathname)
      ? // home 和 notifications 按 pathname 恢复
        location.pathname
      : // 其他所有页面像浏览器一样按 location 恢复
        location.key;
  }}
/>
```

## 阻止滚动重置

当导航创建新的滚动 key 时，滚动位置会重置到页面顶部。你可以阻止链接和表单的"滚动到顶部"行为：

```tsx
<Link preventScrollReset={true} />
<Form preventScrollReset={true} />
```

参见：[`<Link preventScrollReset>`][preventscrollreset]，[`<Form preventScrollReset>`][form-preventscrollreset]

## 滚动闪烁

如果没有像 [Remix][remix] 这样的服务端渲染框架，你可能会在初始页面加载时遇到一些滚动闪烁。这是因为 React Router 在 JS 包下载完成、数据加载完毕和完整页面渲染之前无法恢复滚动位置（如果你渲染了 spinner，视口大小可能与保存滚动位置时不同）。

服务端渲染框架可以防止滚动闪烁，因为它们可以在初始加载时发送完整的文档，这样滚动位置可以在页面首次渲染时恢复。

[remix]: https://remix.run
[preventscrollreset]: ../components/link#preventscrollreset
[form-preventscrollreset]: ../components/form#preventscrollreset
[pickingarouter]: ../routers/picking-a-router
