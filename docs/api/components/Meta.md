---
title: Meta
---

# Meta

[MODES: framework]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.Meta.html)

渲染所有由路由模块 [`meta`](../../start/framework/route-module#meta) 导出创建的 [`<meta>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta) 标签。你应该在文档的 [`<head>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/head) 中渲染它。

```tsx
import { Meta } from "react-router";

export default function Root() {
  return (
    <html>
      <head>
        <Meta />
      </head>
    </html>
  );
}
```

## 函数签名

```tsx
function Meta(): React.JSX.Element;
```
