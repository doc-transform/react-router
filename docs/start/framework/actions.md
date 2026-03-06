---
title: 操作（Action）
order: 6
---

# 操作（Action）

[MODES: framework]

## 简介

数据变更通过路由 action 完成。当 action 完成后，页面上所有的 loader 数据都会自动重新验证，使你的 UI 与数据保持同步，无需编写任何额外代码。

使用 `action` 定义的路由 action 只在服务端调用，而使用 `clientAction` 定义的 action 在浏览器中运行。

## 客户端 Action

客户端 action 仅在浏览器中运行，当同时定义了客户端 action 和服务端 action 时，客户端 action 优先执行。

```tsx filename=app/project.tsx
// route('/projects/:projectId', './project.tsx')
import type { Route } from "./+types/project";
import { Form } from "react-router";
import { someApi } from "./api";

export async function clientAction({
  request,
}: Route.ClientActionArgs) {
  let formData = await request.formData();
  let title = formData.get("title");
  let project = await someApi.updateProject({ title });
  return project;
}

export default function Project({
  actionData,
}: Route.ComponentProps) {
  return (
    <div>
      <h1>Project</h1>
      <Form method="post">
        <input type="text" name="title" />
        <button type="submit">Submit</button>
      </Form>
      {actionData ? (
        <p>{actionData.title} updated</p>
      ) : null}
    </div>
  );
}
```

## 服务端 Action

服务端 action 仅在服务端运行，会从客户端包中移除。

```tsx filename=app/project.tsx
// route('/projects/:projectId', './project.tsx')
import type { Route } from "./+types/project";
import { Form } from "react-router";
import { fakeDb } from "../db";

export async function action({
  request,
}: Route.ActionArgs) {
  let formData = await request.formData();
  let title = formData.get("title");
  let project = await fakeDb.updateProject({ title });
  return project;
}

export default function Project({
  actionData,
}: Route.ComponentProps) {
  return (
    <div>
      <h1>Project</h1>
      <Form method="post">
        <input type="text" name="title" />
        <button type="submit">Submit</button>
      </Form>
      {actionData ? (
        <p>{actionData.title} updated</p>
      ) : null}
    </div>
  );
}
```

## 调用 Action

Action 可以通过 `<Form>` 声明式调用，也可以通过 `useSubmit`（或 `<fetcher.Form>` 和 `fetcher.submit`）命令式调用，需要引用路由路径并使用 "post" 方法。

### 使用 Form 调用 action

```tsx
import { Form } from "react-router";

function SomeComponent() {
  return (
    <Form action="/projects/123" method="post">
      <input type="text" name="title" />
      <button type="submit">Submit</button>
    </Form>
  );
}
```

这将触发导航，并在浏览器历史记录中添加新条目。

### 使用 useSubmit 调用 action

你可以使用 `useSubmit` 以命令式方式向 action 提交表单数据。

```tsx
import { useCallback } from "react";
import { useSubmit } from "react-router";
import { useFakeTimer } from "fake-lib";

function useQuizTimer() {
  let submit = useSubmit();

  let cb = useCallback(() => {
    submit(
      { quizTimedOut: true },
      { action: "/end-quiz", method: "post" },
    );
  }, []);

  let tenMinutes = 10 * 60 * 1000;
  useFakeTimer(tenMinutes, cb);
}
```

这将触发导航，并在浏览器历史记录中添加新条目。

### 使用 fetcher 调用 action

Fetcher 允许你向 action（和 loader）提交数据，而不触发导航（浏览器历史记录中不会添加新条目）。

```tsx
import { useFetcher } from "react-router";

function Task() {
  let fetcher = useFetcher();
  let busy = fetcher.state !== "idle";

  return (
    <fetcher.Form method="post" action="/update-task/123">
      <input type="text" name="title" />
      <button type="submit">
        {busy ? "Saving..." : "Save"}
      </button>
    </fetcher.Form>
  );
}
```

它们也有命令式的 `submit` 方法。

```tsx
fetcher.submit(
  { title: "New Title" },
  { action: "/update-task/123", method: "post" },
);
```

更多信息请参阅[使用 Fetcher][fetchers] 指南。

---

下一节：[导航](./navigating)

[fetchers]: ../../how-to/fetchers
[data]: ../../api/react-router/data
