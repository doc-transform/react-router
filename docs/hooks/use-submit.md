---
title: useSubmit
new: true
---

# `useSubmit`

`<Form>` 的命令式版本，允许你（程序员）代替用户提交表单。

<docs-warning>此功能仅在使用数据路由器时有效，参见[选择路由器][pickingarouter]</docs-warning>

例如，每当表单内的值发生变化时提交表单：

```tsx [8]
import { useSubmit, Form } from "react-router-dom";

function SearchField() {
  let submit = useSubmit();
  return (
    <Form
      onChange={(event) => {
        submit(event.currentTarget);
      }}
    >
      <input type="text" name="search" />
      <button type="submit">Search</button>
    </Form>
  );
}
```

如果你希望在一段时间不活动后自动将用户从网站注销，这也很有用。在这种情况下，我们将不活动定义为用户在 5 分钟后没有导航到任何其他页面。

```tsx lines=[1,10,15]
import { useSubmit, useLocation } from "react-router-dom";
import { useEffect } from "react";

function AdminPage() {
  useSessionTimeout();
  return <div>{/* ... */}</div>;
}

function useSessionTimeout() {
  const submit = useSubmit();
  const location = useLocation();

  useEffect(() => {
    const timer = setTimeout(() => {
      submit(null, { method: "post", action: "/logout" });
    }, 5 * 60_000);

    return () => clearTimeout(timer);
  }, [submit, location]);
}
```

## 提交目标

submit 的第一个参数接受许多不同的值。

你可以提交任何表单或表单输入元素：

```tsx
// input element events
<input onChange={(event) => submit(event.currentTarget)} />;

// React refs
let ref = useRef();
<button ref={ref} />;
submit(ref.current);
```

你可以提交 `FormData`：

```tsx
let formData = new FormData();
formData.append("cheese", "gouda");
submit(formData);
```

或者你可以提交 `URLSearchParams`：

```tsx
let searchParams = new URLSearchParams();
searchParams.append("cheese", "gouda");
submit(searchParams);
```

或者 `URLSearchParams` 构造函数接受的任何内容：

```tsx
submit("cheese=gouda&toasted=yes");
submit([
  ["cheese", "gouda"],
  ["toasted", "yes"],
]);
```

对于 POST 提交，如果提交一个 JSON 对象，默认行为是将数据编码为 `FormData`：

```tsx
submit(
  { key: "value" },
  {
    method: "post",
    encType: "application/x-www-form-urlencoded",
  },
);
// will serialize into request.formData() in your action
// and will show up on useNavigation().formData during the navigation
```

或者你可以选择 JSON 编码：

```tsx
submit(
  { key: "value" },
  { method: "post", encType: "application/json" },
);
// will serialize into request.json() in your action
// and will show up on useNavigation().json during the navigation

submit('{"key":"value"}', {
  method: "post",
  encType: "application/json",
});
// will encode into request.json() in your action
// and will show up on useNavigation().json during the navigation
```

或纯文本：

```tsx
submit("value", { method: "post", encType: "text/plain" });
// will serialize into request.text() in your action
// and will show up on useNavigation().text during the navigation
```

## 提交选项

第二个参数是一组选项，（大部分）直接映射到表单提交属性：

```tsx
submit(null, {
  method: "post",
  action: "/logout",
});

// same as
<Form action="/logout" method="post" />;
```

<docs-info>请参阅 `useResolvedPath` 文档中的[通配符路径][relativesplatpath]部分，了解 `future.v7_relativeSplatPath` future flag 对通配符路由内相对 `useSubmit()` `action` 行为的说明</docs-info>

由于提交是导航，选项还可以包含来自 [`<Form>`][form] 的其他导航相关 props，如：

- `fetcherKey`
- `navigate`
- `preventScrollReset`
- `relative`
- `replace`
- `state`
- `viewTransition`

### `options.flushSync`

`flushSync` 选项告诉 React Router DOM 将此提交的初始状态更新包装在 [`ReactDOM.flushSync`][flush-sync] 调用中，而不是默认的 [`React.startTransition`][start-transition] 中。这允许你在更新刷新到 DOM 后立即执行同步 DOM 操作。

[pickingarouter]: ../routers/picking-a-router
[form]: ../components/form
[flush-sync]: https://react.dev/reference/react-dom/flushSync
[start-transition]: https://react.dev/reference/react/startTransition
[relativesplatpath]: ../hooks/use-resolved-path#splat-paths
