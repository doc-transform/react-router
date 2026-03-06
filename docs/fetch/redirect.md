---
title: redirect
new: true
---

# `redirect`

由于你可以在 loader 和 action 中返回或抛出响应，因此可以使用 `redirect` 重定向到另一个路由。

```jsx
import { redirect } from "react-router-dom";

const loader = async () => {
  const user = await getUser();
  if (!user) {
    return redirect("/login");
  }
  return null;
};
```

它实际上只是以下写法的快捷方式：

```jsx
new Response("", {
  status: 302,
  headers: {
    Location: someUrl,
  },
});
```

当重定向是对数据的响应时，建议在 loader 和 action 中使用 `redirect` 而不是在组件中使用 `useNavigate`。

另请参阅：

- [从 Loader 返回 Response][responses]

## 类型声明

```ts
type RedirectFunction = (
  url: string,
  init?: number | ResponseInit,
) => Response;
```

## `url`

要重定向到的 URL。

```js
redirect("/login");
```

## `init`

响应中要使用的 [Response][response] 选项。

[responses]: ../route/loader#returning-responses
[response]: https://developer.mozilla.org/en-US/docs/Web/API/Response/Response
