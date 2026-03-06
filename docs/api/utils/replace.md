---
title: replace
---

# replace

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.replace.html)

重定向 [`Response`](https://developer.mozilla.org/en-US/docs/Web/API/Response)，对于客户端导航重定向，会执行 [`history.replaceState`](https://developer.mozilla.org/en-US/docs/Web/API/History/replaceState) 而非 [`history.pushState`](https://developer.mozilla.org/en-US/docs/Web/API/History/pushState)。设置状态码和 [`Location`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Location) 响应头。默认为 [`302 Found`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/302)。

```tsx
import { replace } from "react-router";

export async function loader() {
  return replace("/new-location");
}
```

## 参数

### url

要重定向到的 URL。

### init

要包含在响应中的状态码或 `ResponseInit` 对象。

## 返回值

带有重定向状态和 [`Location`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Location) 响应头的 [`Response`](https://developer.mozilla.org/en-US/docs/Web/API/Response) 对象。
