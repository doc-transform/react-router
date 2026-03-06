---
title: NavLink
---

# `<NavLink>`

`<NavLink>` 是一种特殊的 `<Link>`，它能够知道自身是否处于"活跃"、"待定"或"过渡中"状态。这在以下几种场景中非常有用：

- 构建导航菜单时，如面包屑或标签页组，你希望展示当前选中的项目
- 它为辅助技术（如屏幕阅读器）提供有用的上下文信息
- 它提供一个"过渡中"值，让你对[视图过渡][view-transitions]有更精细的控制

```tsx
import { NavLink } from "react-router-dom";

<NavLink
  to="/messages"
  className={({ isActive, isPending }) =>
    isPending ? "pending" : isActive ? "active" : ""
  }
>
  Messages
</NavLink>;
```

## 默认 `active` 类名

默认情况下，当 `<NavLink>` 组件处于活跃状态时会自动添加一个 `active` 类名，你可以使用 CSS 来设置样式。

```tsx
<nav id="sidebar">
  <NavLink to="/messages" />
</nav>
```

```css
#sidebar a.active {
  color: red;
}
```

## `className`

`className` 属性的用法与普通 className 相同，但你也可以传递一个函数，根据链接的活跃和待定状态自定义应用的类名。

```tsx
<NavLink
  to="/messages"
  className={({ isActive, isPending, isTransitioning }) =>
    [
      isPending ? "pending" : "",
      isActive ? "active" : "",
      isTransitioning ? "transitioning" : "",
    ].join(" ")
  }
>
  Messages
</NavLink>
```

## `style`

`style` 属性的用法与普通 style 属性相同，但你也可以传递一个函数，根据链接的活跃和待定状态自定义应用的样式。

```tsx
<NavLink
  to="/messages"
  style={({ isActive, isPending, isTransitioning }) => {
    return {
      fontWeight: isActive ? "bold" : "",
      color: isPending ? "red" : "black",
      viewTransitionName: isTransitioning ? "slide" : "",
    };
  }}
>
  Messages
</NavLink>
```

## `children`

你可以传递一个渲染函数作为 children，根据活跃和待定状态自定义 `<NavLink>` 的内容，这对于更改内部元素的样式很有用。

```tsx
<NavLink to="/tasks">
  {({ isActive, isPending, isTransitioning }) => (
    <span className={isActive ? "active" : ""}>Tasks</span>
  )}
</NavLink>
```

## `end`

`end` 属性改变了 `active` 和 `pending` 状态的匹配逻辑，使其仅匹配 NavLink 的 `to` 路径的"末端"。如果 URL 长于 `to`，它将不再被视为活跃。

| Link                           | 当前 URL     | isActive |
| ------------------------------ | ------------ | -------- |
| `<NavLink to="/tasks" />`      | `/tasks`     | true     |
| `<NavLink to="/tasks" />`      | `/tasks/123` | true     |
| `<NavLink to="/tasks" end />`  | `/tasks`     | true     |
| `<NavLink to="/tasks" end />`  | `/tasks/123` | false    |
| `<NavLink to="/tasks/" end />` | `/tasks`     | false    |
| `<NavLink to="/tasks/" end />` | `/tasks/`    | true     |

**关于根路由链接的说明**

`<NavLink to="/">` 是一个特殊情况，因为*每个* URL 都匹配 `/`。为了避免默认情况下匹配所有路由，它实际上忽略了 `end` 属性，只在你位于根路由时才匹配。

## `caseSensitive`

添加 `caseSensitive` 属性可以使匹配逻辑区分大小写。

| Link                                         | URL           | isActive |
| -------------------------------------------- | ------------- | -------- |
| `<NavLink to="/SpOnGe-bOB" />`               | `/sponge-bob` | true     |
| `<NavLink to="/SpOnGe-bOB" caseSensitive />` | `/sponge-bob` | false    |

## `aria-current`

当 `NavLink` 处于活跃状态时，它会自动在底层锚标签上应用 `<a aria-current="page">`。参见 MDN 上的 [aria-current][aria-current]。

## `reloadDocument`

`reloadDocument` 属性可用于跳过客户端路由，让浏览器正常处理过渡（就像 `<a href>` 一样）。

## `viewTransition`

`viewTransition` 属性通过将最终状态更新包装在 `document.startViewTransition()` 中，为此次导航启用[视图过渡][view-transitions]。默认情况下，在过渡期间会向 `<a>` 元素添加一个 `transitioning` 类名，你可以用它来自定义视图过渡。

```css
a.transitioning p {
  view-transition-name: "image-title";
}

a.transitioning img {
  view-transition-name: "image-expand";
}
```

```jsx
<NavLink to={to} viewTransition>
  <p>Image Number {idx}</p>
  <img src={src} alt={`Img ${idx}`} />
</NavLink>
```

你也可以使用 `className`/`style` 属性或传递给 `children` 的渲染函数，根据 `isTransitioning` 值进一步自定义。

```jsx
<NavLink to={to} viewTransition>
  {({ isTransitioning }) => (
    <>
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
    </>
  )}
</NavLink>
```

[aria-current]: https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-current
[view-transitions]: https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API
