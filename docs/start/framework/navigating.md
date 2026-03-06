---
title: 导航
order: 6
---

# 导航

[MODES: framework]

## 简介

用户通过 `<Link>`、`<NavLink>`、`<Form>`、`redirect` 和 `useNavigate` 在你的应用中进行导航。

## NavLink

此组件用于需要渲染激活和待定状态的导航链接。

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

`NavLink` 会渲染不同状态下的默认类名，方便用 CSS 设置样式：

```css
a.active {
  color: red;
}

a.pending {
  animate: pulse 1s infinite;
}

a.transitioning {
  /* css 过渡正在运行 */
}
```

它还提供了 `className`、`style` 和 `children` 上的回调属性，可以获取状态用于内联样式或条件渲染：

```tsx
// className
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

```tsx
// style
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

```tsx
// children
<NavLink to="/tasks">
  {({ isActive, isPending, isTransitioning }) => (
    <span className={isActive ? "active" : ""}>Tasks</span>
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

## Form

Form 组件可用于通过用户提供的 `URLSearchParams` 进行导航。

```tsx
<Form action="/search">
  <input type="text" name="q" />
</Form>
```

如果用户在输入框中输入"journey"并提交，将导航到：

```
/search?q=journey
```

使用 `<Form method="post" />` 的表单也会导航到 action 属性指定的地址，但会以 `FormData` 而非 `URLSearchParams` 的形式提交数据。不过更常见的做法是使用 `useFetcher()` 来 POST 表单数据。参见[使用 Fetcher](../../how-to/fetchers)。

## redirect

在路由 loader 和 action 内部，你可以返回 `redirect` 跳转到另一个 URL。

```tsx
import { redirect } from "react-router";

export async function loader({ request }) {
  let user = await getUser(request);
  if (!user) {
    return redirect("/login");
  }
  return { userName: user.name };
}
```

在新记录创建后重定向到该记录是很常见的做法：

```tsx
import { redirect } from "react-router";

export async function action({ request }) {
  let formData = await request.formData();
  let project = await createProject(formData);
  return redirect(`/projects/${project.id}`);
}
```

## useNavigate

此 Hook 允许开发者在无需用户交互的情况下，将用户导航到新页面。此 Hook 的使用应该不常见。建议尽可能使用本指南中的其他 API。

仅在用户*没有*交互但你需要导航的场景下使用 `useNavigate`，例如：

- 因不活跃而将用户登出
- 有时间限制的 UI（如测验等）

```tsx
import { useNavigate } from "react-router";

export function useLogoutAfterInactivity() {
  let navigate = useNavigate();

  useFakeInactivityHook(() => {
    navigate("/logout");
  });
}
```

---

下一节：[待定 UI](./pending-ui)
