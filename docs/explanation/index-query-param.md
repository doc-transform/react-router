---
title: Index 查询参数
---

# Index 查询参数

[MODES: framework, data]

## 概述

在提交表单时，你可能会发现应用的 URL 中出现了一个奇怪的 `?index`。

由于嵌套路由的存在，路由层级中的多个路由可以匹配同一个 URL。与导航时调用所有匹配路由的 [`loader`][loader] 来构建 UI 不同，当提交 [`form`][form_element] 时，_只会调用一个 action_。

因为索引路由与其父路由共享相同的 URL，`?index` 参数让你可以消除两者之间的歧义。

## 理解索引路由

例如，考虑以下路由结构：

```ts filename=app/routes.ts
import {
  type RouteConfig,
  route,
  index,
} from "@react-router/dev/routes";

export default [
  route("projects", "./pages/projects.tsx", [
    index("./pages/projects/index.tsx"),
    route(":id", "./pages/projects/project.tsx"),
  ]),
] satisfies RouteConfig;
```

这创建了两个匹配 `/projects` 的路由：

- 父路由（`./pages/projects.tsx`）
- 索引路由（`./pages/projects/index.tsx`）

## 表单提交目标

例如，考虑以下表单：

```tsx
<Form method="post" action="/projects" />
<Form method="post" action="/projects?index" />
```

`?index` 参数会将表单提交到索引路由；不带 index 参数的 action 会将表单提交到父路由。

当 [`<Form>`][form_component] 在索引路由中渲染且没有指定 [`action`][action] 时，`?index` 参数会自动附加，使表单提交到索引路由。以下表单在提交时会提交到 `/projects?index`，因为它是在 `projects` 索引路由的上下文中渲染的：

```tsx filename=app/pages/projects/index.tsx
function ProjectsIndex() {
  return <Form method="post" />;
}
```

如果你将代码移到项目布局中（本例中的 `./pages/projects.tsx`），它将提交到 `/projects`。

这适用于 `<Form>` 及其所有相关 API：

```tsx
function Component() {
  const submit = useSubmit();
  submit({}, { action: "/projects" });
  submit({}, { action: "/projects?index" });
}
```

```tsx
function Component() {
  const fetcher = useFetcher();
  fetcher.submit({}, { action: "/projects" });
  fetcher.submit({}, { action: "/projects?index" });
  <fetcher.Form action="/projects" />;
  <fetcher.Form action="/projects?index" />;
  <fetcher.Form />; // 默认使用当前上下文中的路由
}
```

[loader]: ../api/data-routers/loader
[form_element]: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form
[form_component]: ../api/components/Form
[action]: ../api/data-routers/action
