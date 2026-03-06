---
title: 导航
order: 3
---

# 导航

[MODES: declarative]

## 简介

用户通过 `<Link>`、`<NavLink>` 和 `useNavigate` 在你的应用中进行导航。

## NavLink

此组件用于需要渲染激活状态的导航链接。

```tsx
import { NavLink } from "react-router";

export function MyAppNav() {
  return (
    <nav>
      <NavLink to="/" end>
        Home
      </NavLink>
      <NavLink to="/trending" end>
        Trending Concerts
      </NavLink>
      <NavLink to="/concerts">All Concerts</NavLink>
      <NavLink to="/account">Account</NavLink>
    </nav>
  );
}
```

当 `NavLink` 处于激活状态时，它会自动添加 `.active` 类名，方便通过 CSS 设置样式：

```css
a.active {
  color: red;
}
```

它还提供了 `className`、`style` 和 `children` 上的回调属性，可以获取激活状态，用于内联样式或条件渲染：

```tsx
// className
<NavLink
  to="/messages"
  className={({ isActive }) =>
    isActive ? "text-red-500" : "text-black"
  }
>
  Messages
</NavLink>
```

```tsx
// style
<NavLink
  to="/messages"
  style={({ isActive }) => ({
    color: isActive ? "red" : "black",
  })}
>
  Messages
</NavLink>
```

```tsx
// children
<NavLink to="/message">
  {({ isActive }) => (
    <span className={isActive ? "active" : ""}>
      {isActive ? "👉" : ""} Tasks
    </span>
  )}
</NavLink>
```

## Link

当链接不需要激活样式时，使用 `<Link>`：

```tsx
import { Link } from "react-router";

export function LoggedOutMessage() {
  return (
    <p>
      You've been logged out.{" "}
      <Link to="/login">Login again</Link>
    </p>
  );
}
```

## useNavigate

此 Hook 允许开发者在无需用户交互的情况下，将用户导航到新页面。

对于常规导航，最好使用 `Link` 或 `NavLink`。它们提供了更好的默认用户体验，如键盘事件、无障碍标签、"在新窗口中打开"、右键上下文菜单等。

仅在用户*没有*交互但你需要导航的场景下使用 `useNavigate`，例如：

- 表单提交完成后
- 因不活跃而将用户登出
- 有时间限制的 UI（如测验等）

```tsx
import { useNavigate } from "react-router";

export function LoginPage() {
  let navigate = useNavigate();

  return (
    <>
      <MyHeader />
      <MyLoginForm
        onSuccess={() => {
          navigate("/dashboard");
        }}
      />
      <MyFooter />
    </>
  );
}
```

---

下一节：[URL 值](./url-values)
