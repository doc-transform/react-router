---
title: href
---

# href

[MODES: framework]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.href.html)

返回指定路由的已解析 URL 路径。

```tsx
const h = href("/:lang?/about", { lang: "en" })
// -> `/en/about`

<Link to={href("/products/:id", { id: "abc123" })} />
```
