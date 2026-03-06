---
title: NavLink
---

# NavLink

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.NavLink.html)

在 [`<Link>`](../components/Link) 基础上封装了额外的 props，用于设置激活和待定状态的样式。

- 根据链接的 `active` 和 `pending` 状态自动添加类名，参见 [`NavLinkProps.className`](https://api.reactrouter.com/v7/interfaces/react-router.NavLinkProps.html#className)
  - 注意：`pending` 状态仅在框架模式和数据模式中可用。
- 当链接处于激活状态时，自动添加 `aria-current="page"` 属性。参见 MDN 上的 [`aria-current`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-current)。
- 状态也可通过 className、style 和 children 的渲染属性获取。参见 [`NavLinkRenderProps`](https://api.reactrouter.com/v7/types/react-router.NavLinkRenderProps.html)。

```tsx
<NavLink to="/message">Messages</NavLink>

// 使用渲染属性
<NavLink
  to="/messages"
  className={({ isActive, isPending }) =>
    isPending ? "pending" : isActive ? "active" : ""
  }
>
  Messages
</NavLink>
```

## Props

### caseSensitive

[modes: framework, data, declarative]

将匹配逻辑改为区分大小写：

| 链接                                         | URL           | isActive |
| -------------------------------------------- | ------------- | -------- |
| `<NavLink to="/SpOnGe-bOB" />`               | `/sponge-bob` | true     |
| `<NavLink to="/SpOnGe-bOB" caseSensitive />` | `/sponge-bob` | false    |

### children

[modes: framework, data, declarative]

可以是普通的 React 子元素，也可以是一个接收链接 `active` 和 `pending` 状态对象的函数。

```tsx
<NavLink to="/tasks">
  {({ isActive }) => (
    <span className={isActive ? "active" : ""}>Tasks</span>
  )}
</NavLink>
```

### className

[modes: framework, data, declarative]

`NavLink` 会根据状态自动添加对应的类名。

```css
a.active {
  color: red;
}
a.pending {
  color: blue;
}
a.transitioning {
  view-transition-name: my-transition;
}
```

或者你可以指定一个接收 [`NavLinkRenderProps`](https://api.reactrouter.com/v7/types/react-router.NavLinkRenderProps.html) 并返回 `className` 的函数：

```tsx
<NavLink
  className={({ isActive, isPending }) =>
    isActive
      ? "my-active-class"
      : isPending
        ? "my-pending-class"
        : ""
  }
/>
```

### discover

[modes: framework]

定义链接的[懒路由发现](../../explanation/lazy-route-discovery)行为。

- **render** — 默认值，在链接渲染时发现路由
- **none** — 不提前发现，仅在链接被点击时发现

```tsx
<Link /> // 默认 ("render")
<Link discover="render" />
<Link discover="none" />
```

### end

[modes: framework, data, declarative]

将 `active` 和 `pending` 状态的匹配逻辑改为只匹配 [`NavLinkProps.to`](https://api.reactrouter.com/v7/interfaces/react-router.NavLinkProps.html#to) 的"末尾"。如果 URL 更长，将不再被视为激活状态。

| 链接                          | URL          | isActive |
| ----------------------------- | ------------ | -------- |
| `<NavLink to="/tasks" />`     | `/tasks`     | true     |
| `<NavLink to="/tasks" />`     | `/tasks/123` | true     |
| `<NavLink to="/tasks" end />` | `/tasks`     | true     |
| `<NavLink to="/tasks" end />` | `/tasks/123` | false    |

`<NavLink to="/">` 是一个特殊情况，因为*每个* URL 都匹配 `/`。为了避免默认匹配所有路由，它实际上忽略了 `end` 属性，只在根路由时才匹配。

### prefetch

[modes: framework]

定义链接的数据和模块预取行为。

```tsx
<Link /> // 默认
<Link prefetch="none" />
<Link prefetch="intent" />
<Link prefetch="render" />
<Link prefetch="viewport" />
```

- **none** — 默认，不预取
- **intent** — 当用户悬停或聚焦链接时预取
- **render** — 当链接渲染时预取
- **viewport** — 当链接进入视口时预取，非常适合移动端

预取通过 HTML [`<link rel="prefetch">`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link) 标签实现，它们会插入到链接之后。

```tsx
<a href="..." />
<a href="..." />
<link rel="prefetch" /> // 可能会条件渲染
```

因此，如果你使用了 `nav :last-child` 选择器，需要改用 `nav :last-of-type`，以避免样式因条件渲染而从最后一个链接上脱落（以及其他类似的选择器）。

### preventScrollReset

[modes: framework, data]

点击链接时，阻止滚动位置重置到窗口顶部（当应用使用 [`ScrollRestoration`](../components/ScrollRestoration) 时）。这仅阻止新位置重置滚动到顶部，后退/前进按钮导航时滚动位置仍会恢复。

```tsx
<Link to="?tab=one" preventScrollReset />
```

### relative

[modes: framework, data, declarative]

定义链接的相对路径行为。

```tsx
<Link to=".." /> // 默认: "route"
<Link relative="route" />
<Link relative="path" />
```

假设路由层级中父路由模式为 `"blog"`，子路由模式为 `"blog/:slug/edit"`：

- **route** — 默认值，相对于路由模式解析链接。在上面的例子中，相对链接 `"..."` 会移除 `:slug/edit` 两个段，回到 `"/blog"`。
- **path** — 相对于路径，因此 `"..."` 只会向上移除一个 URL 段到 `"/blog/:slug"`。

注意：索引路由和布局路由没有路径，因此不参与相对路径计算。

### reloadDocument

[modes: framework, data, declarative]

点击链接时使用文档导航代替客户端路由：浏览器会像处理普通 [`<a href>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/a) 一样处理过渡。

```tsx
<Link to="/logout" reloadDocument />
```

### replace

[modes: framework, data, declarative]

替换 [`History`](https://developer.mozilla.org/en-US/docs/Web/API/History) 栈中的当前条目，而不是推入新条目。

```tsx
<Link replace />
```

```
# 假设历史栈如下
A -> B

# 普通链接点击会推入新条目
A -> B -> C

# 使用 `replace`，B 会被 C 替换
A -> C
```

### state

[modes: framework, data, declarative]

向下一个位置添加持久性的客户端路由状态。

```tsx
<Link to="/somewhere/else" state={{ some: "value" }} />
```

可以通过 `location` 访问位置状态。

```tsx
function SomeComp() {
  const location = useLocation();
  location.state; // { some: "value" }
}
```

此状态在服务端不可访问，因为它是基于 [`history.state`](https://developer.mozilla.org/en-US/docs/Web/API/History/state) 实现的。

### style

[modes: framework, data, declarative]

也可以通过接收 [`NavLinkRenderProps`](https://api.reactrouter.com/v7/types/react-router.NavLinkRenderProps.html) 的函数动态应用样式：

```tsx
<NavLink to="/tasks" style={{ color: "red" }} />
<NavLink to="/tasks" style={({ isActive, isPending }) => ({
  color:
    isActive ? "red" :
    isPending ? "blue" : "black"
})} />
```

### to

[modes: framework, data, declarative]

可以是字符串或部分 [`Path`](https://api.reactrouter.com/v7/interfaces/react-router.Path.html)：

```tsx
<Link to="/some/path" />

<Link
  to={{
    pathname: "/some/path",
    search: "?query=string",
    hash: "#hash",
  }}
/>
```

### viewTransition

[modes: framework, data]

为此导航启用[视图过渡](https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API)。

```jsx
<Link to={to} viewTransition>
  Click me
</Link>
```

要在过渡期间应用特定样式，请参阅 [`useViewTransitionState`](../hooks/useViewTransitionState)。
