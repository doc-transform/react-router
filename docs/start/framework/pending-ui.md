---
title: 待定 UI
order: 7
---

# 待定 UI

[MODES: framework]

## 简介

当用户导航到新路由或向 action 提交数据时，UI 应该立即通过待定或乐观状态响应用户的操作。应用代码负责处理这些状态。

## 全局待定导航

当用户导航到新 URL 时，下一个页面的 loader 会在页面渲染前等待完成。你可以通过 `useNavigation` 获取待定状态。

```tsx
import { useNavigation } from "react-router";

export default function Root() {
  const navigation = useNavigation();
  const isNavigating = Boolean(navigation.location);

  return (
    <html>
      <body>
        {isNavigating && <GlobalSpinner />}
        <Outlet />
      </body>
    </html>
  );
}
```

## 局部待定导航

待定指示器也可以局部化到链接上。NavLink 的 children、className 和 style 属性可以是接收待定状态的函数。

```tsx
import { NavLink } from "react-router";

function Navbar() {
  return (
    <nav>
      <NavLink to="/home">
        {({ isPending }) => (
          <span>Home {isPending && <Spinner />}</span>
        )}
      </NavLink>
      <NavLink
        to="/about"
        style={({ isPending }) => ({
          color: isPending ? "gray" : "black",
        })}
      >
        About
      </NavLink>
    </nav>
  );
}
```

## 待定表单提交

当表单提交时，UI 应该立即以待定状态响应用户的操作。使用 [fetcher][use_fetcher] 表单最容易实现，因为它有自己独立的状态（而普通表单会触发全局导航）。

```tsx filename=app/project.tsx lines=[10-12]
import { useFetcher } from "react-router";

function NewProjectForm() {
  const fetcher = useFetcher();

  return (
    <fetcher.Form method="post">
      <input type="text" name="title" />
      <button type="submit">
        {fetcher.state !== "idle"
          ? "Submitting..."
          : "Submit"}
      </button>
    </fetcher.Form>
  );
}
```

对于非 fetcher 表单提交，待定状态可以通过 `useNavigation` 获取。

```tsx filename=app/projects/new.tsx
import { useNavigation, Form } from "react-router";

function NewProjectForm() {
  const navigation = useNavigation();

  return (
    <Form method="post" action="/projects/new">
      <input type="text" name="title" />
      <button type="submit">
        {navigation.formAction === "/projects/new"
          ? "Submitting..."
          : "Submit"}
      </button>
    </Form>
  );
}
```

## 乐观 UI

当 UI 的未来状态可以通过表单提交数据预知时，可以实现乐观 UI 以获得即时的用户体验。

```tsx filename=app/project.tsx lines=[4-7]
function Task({ task }) {
  const fetcher = useFetcher();

  let isComplete = task.status === "complete";
  if (fetcher.formData) {
    isComplete =
      fetcher.formData.get("status") === "complete";
  }

  return (
    <div>
      <div>{task.title}</div>
      <fetcher.Form method="post">
        <button
          name="status"
          value={isComplete ? "incomplete" : "complete"}
        >
          {isComplete ? "Mark Incomplete" : "Mark Complete"}
        </button>
      </fetcher.Form>
    </div>
  );
}
```

---

下一节：[测试](./testing)

[use_fetcher]: https://api.reactrouter.com/v7/functions/react-router.useFetcher.html
