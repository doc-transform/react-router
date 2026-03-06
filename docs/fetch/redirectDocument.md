---
title: redirectDocument
new: true
---

# `redirectDocument`

这是对 [`redirect`][redirect] 的一个小型包装，它会触发文档级别的重定向到新位置，而不是客户端导航。

当你的 React Router 应用与同一域名上的另一个独立应用共存时，这最为有用。你需要从 React Router 应用通过 `window.location` 重定向到另一个应用，而不是使用 React Router 导航：

```jsx
import { redirectDocument } from "react-router-dom";

const loader = async () => {
  const user = await getUser();
  if (!user) {
    return redirectDocument("/otherapp/login");
  }
  return null;
};
```

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
redirectDocument("/otherapp/login");
```

## `init`

响应中要使用的 [Response][response] 选项。

[response]: https://developer.mozilla.org/en-US/docs/Web/API/Response/Response
[redirect]: ./redirect
