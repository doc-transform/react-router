---
title: Links
---

# Links

[MODES: framework]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.Links.html)

渲染所有由路由模块 [`links`](../../start/framework/route-module#links) 导出创建的 [`<link>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link) 标签。你应该在文档的 [`<head>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/head) 中渲染它。

```tsx
import { Links } from "react-router";

export default function Root() {
  return (
    <html>
      <head>
        <Links />
      </head>
      <body></body>
    </html>
  );
}
```

## 函数签名

```tsx
function Links({
  nonce,
  crossOrigin,
}: LinksProps): React.JSX.Element;
```

## Props

### nonce

渲染在 [`<link>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link) 元素上的 [`nonce`](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Global_attributes/nonce) 属性。

### crossOrigin

渲染在 [`<link>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link) 元素上的 [`crossOrigin`](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) 属性。
