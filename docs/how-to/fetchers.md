---
title: 使用 Fetcher
---

# 使用 Fetcher

[MODES: framework, data]

<br/>
<br/>

Fetcher 适用于创建需要多个并发数据交互但不触发导航的复杂、动态用户界面。

Fetcher 跟踪自己独立的状态，可以用来加载数据、修改数据、提交表单，以及与 loader 和 action 交互。

## 调用 Action

Fetcher 最常见的用例是向 action 提交数据，触发路由数据的重新验证。考虑以下路由模块：

```tsx
import { useLoaderData } from "react-router";

export async function clientLoader({ request }) {
  let title = localStorage.getItem("title") || "No Title";
  return { title };
}

export default function Component() {
  let data = useLoaderData();
  return (
    <div>
      <h1>{data.title}</h1>
    </div>
  );
}
```

### 1. 添加 action

首先为路由添加一个供 fetcher 调用的 action：

```tsx lines=[7-11]
import { useLoaderData } from "react-router";

export async function clientLoader({ request }) {
  // ...
}

export async function clientAction({ request }) {
  await new Promise((res) => setTimeout(res, 1000));
  let data = await request.formData();
  localStorage.setItem("title", data.get("title"));
  return { ok: true };
}

export default function Component() {
  let data = useLoaderData();
  // ...
}
```

### 2. 创建 fetcher

接下来创建一个 fetcher 并用它渲染一个表单：

```tsx lines=[7,12-14]
import { useLoaderData, useFetcher } from "react-router";

// ...

export default function Component() {
  let data = useLoaderData();
  let fetcher = useFetcher();
  return (
    <div>
      <h1>{data.title}</h1>

      <fetcher.Form method="post">
        <input type="text" name="title" />
      </fetcher.Form>
    </div>
  );
}
```

### 3. 提交表单

如果你现在提交表单，fetcher 会调用 action 并自动重新验证路由数据。

### 4. 渲染待处理状态

Fetcher 在异步工作期间会暴露它们的状态，这样你可以在用户交互时立即渲染待处理 UI：

```tsx lines=[10]
export default function Component() {
  let data = useLoaderData();
  let fetcher = useFetcher();
  return (
    <div>
      <h1>{data.title}</h1>

      <fetcher.Form method="post">
        <input type="text" name="title" />
        {fetcher.state !== "idle" && <p>保存中...</p>}
      </fetcher.Form>
    </div>
  );
}
```

### 5. 乐观 UI

有时表单中有足够的信息可以立即渲染下一个状态。你可以通过 `fetcher.formData` 访问表单数据：

```tsx lines=[3-4,8]
export default function Component() {
  let data = useLoaderData();
  let fetcher = useFetcher();
  let title = fetcher.formData?.get("title") || data.title;

  return (
    <div>
      <h1>{title}</h1>

      <fetcher.Form method="post">
        <input type="text" name="title" />
        {fetcher.state !== "idle" && <p>保存中...</p>}
      </fetcher.Form>
    </div>
  );
}
```

### 6. Fetcher 数据和验证

从 action 返回的数据可以通过 fetcher 的 `data` 属性获取。这主要用于在变更失败时向用户返回错误消息：

```tsx lines=[7-10,28-32]
// ...

export async function clientAction({ request }) {
  await new Promise((res) => setTimeout(res, 1000));
  let data = await request.formData();

  let title = data.get("title") as string;
  if (title.trim() === "") {
    return { ok: false, error: "标题不能为空" };
  }

  localStorage.setItem("title", title);
  return { ok: true, error: null };
}

export default function Component() {
  let data = useLoaderData();
  let fetcher = useFetcher();
  let title = fetcher.formData?.get("title") || data.title;

  return (
    <div>
      <h1>{title}</h1>

      <fetcher.Form method="post">
        <input type="text" name="title" />
        {fetcher.state !== "idle" && <p>保存中...</p>}
        {fetcher.data?.error && (
          <p style={{ color: "red" }}>
            {fetcher.data.error}
          </p>
        )}
      </fetcher.Form>
    </div>
  );
}
```

## 加载数据

Fetcher 的另一个常见用例是从路由加载数据，例如用于组合框。

### 1. 创建搜索路由

考虑以下带有基本搜索功能的路由：

```tsx filename=./search-users.tsx
// { path: '/search-users', filename: './search-users.tsx' }
const users = [
  { id: 1, name: "Ryan" },
  { id: 2, name: "Michael" },
  // ...
];

export async function loader({ request }) {
  await new Promise((res) => setTimeout(res, 300));
  let url = new URL(request.url);
  let query = url.searchParams.get("q");
  return users.filter((user) =>
    user.name.toLowerCase().includes(query.toLowerCase()),
  );
}
```

### 2. 在组合框组件中渲染 fetcher

```tsx
import { useFetcher } from "react-router";

export function UserSearchCombobox() {
  let fetcher = useFetcher();
  return (
    <div>
      <fetcher.Form method="get" action="/search-users">
        <input type="text" name="q" />
      </fetcher.Form>
    </div>
  );
}
```

- action 指向我们上面创建的路由："/search-users"
- 输入框的 name 是 "q"，与查询参数匹配

### 3. 添加类型推断

```tsx lines=[2,5]
import { useFetcher } from "react-router";
import type { loader } from "./search-users";

export function UserSearchCombobox() {
  let fetcher = useFetcher<typeof loader>();
  // ...
}
```

确保使用 `import type`，这样你只导入类型。

### 4. 渲染数据

```tsx lines=[10-16]
import { useFetcher } from "react-router";

export function UserSearchCombobox() {
  let fetcher = useFetcher<typeof loader>();
  return (
    <div>
      <fetcher.Form method="get" action="/search-users">
        <input type="text" name="q" />
      </fetcher.Form>
      {fetcher.data && (
        <ul>
          {fetcher.data.map((user) => (
            <li key={user.id}>{user.name}</li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

注意你需要按"回车"来提交表单并查看结果。

### 5. 渲染待处理状态

```tsx lines=[12-14]
import { useFetcher } from "react-router";

export function UserSearchCombobox() {
  let fetcher = useFetcher<typeof loader>();
  return (
    <div>
      <fetcher.Form method="get" action="/search-users">
        <input type="text" name="q" />
      </fetcher.Form>
      {fetcher.data && (
        <ul
          style={{
            opacity: fetcher.state === "idle" ? 1 : 0.25,
          }}
        >
          {fetcher.data.map((user) => (
            <li key={user.id}>{user.name}</li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

### 6. 在用户输入时搜索

Fetcher 可以通过 `fetcher.submit` 以编程方式提交：

```tsx lines=[5-7]
<fetcher.Form method="get" action="/search-users">
  <input
    type="text"
    name="q"
    onChange={(event) => {
      fetcher.submit(event.currentTarget.form);
    }}
  />
</fetcher.Form>
```

注意输入事件的 form 作为第一个参数传递给 `fetcher.submit`。Fetcher 将使用该表单来提交请求，读取其属性并从其元素中序列化数据。
