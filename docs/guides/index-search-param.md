---
title: Index 查询参数
new: true
---

# Index 查询参数

你可能会发现在提交表单时，应用的 URL 中出现了一个奇怪的 `?index`。

由于嵌套路由的存在，路由层级中的多个路由可以匹配 URL。与导航不同（导航时所有匹配路由的 loader 都会被调用以构建 UI），当表单提交时 _只会调用一个 action_。

因为 index 路由与其父路由共享相同的 URL，`?index` 参数让你可以在两者之间消除歧义。

例如，考虑以下路由器和表单：

```jsx
createBrowserRouter([
  {
    path: "/projects",
    element: <ProjectsLayout />,
    action: ProjectsLayout.action,
    children: [
      {
        index: true,
        element: <ProjectsIndex />,
        action: ProjectsPage.action,
      },
    ],
  },
]);

<Form method="post" action="/projects" />;
<Form method="post" action="/projects?index" />;
```

`?index` 参数会提交到 index 路由，不带 index 参数的 action 会提交到父路由。

当 `<Form>` 在没有 `action` 的 index 路由中渲染时，`?index` 参数会自动追加，以便表单提交到 index 路由。以下表单在提交时会发送到 `/projects?index`，因为它是在 projects index 路由的上下文中渲染的：

```tsx
function ProjectsIndex() {
  return <Form method="post" />;
}
```

如果你将代码移到 `ProjectsLayout` 路由，它会改为提交到 `/projects`。

这适用于 `<Form>` 及其所有相关 API：

```tsx
let submit = useSubmit();
submit({}, { action: "/projects" });
submit({}, { action: "/projects?index" });

let fetcher = useFetcher();
fetcher.submit({}, { action: "/projects" });
fetcher.submit({}, { action: "/projects?index" });
<fetcher.Form action="/projects" />;
<fetcher.Form action="/projects?index" />;
<fetcher.Form />; // defaults to the route in context
```
