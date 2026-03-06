---
title: redirect
---

# redirect

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.redirect.html)

重定向 [`Response`](https://developer.mozilla.org/en-US/docs/Web/API/Response)。设置状态码和 [`Location`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Location) 响应头。默认为 [`302 Found`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/302)。

```tsx
import { redirect } from "react-router";

export async function loader({ request }: Route.LoaderArgs) {
  if (!isLoggedIn(request))
    throw redirect("/login");
  }

  // ...
}
```

## 参数

### url

要重定向到的 URL。

### init

要包含在响应中的状态码或 `ResponseInit` 对象。

## 返回值

带有重定向状态和 [`Location`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Location) 响应头的 [`Response`](https://developer.mozilla.org/en-US/docs/Web/API/Response) 对象。
