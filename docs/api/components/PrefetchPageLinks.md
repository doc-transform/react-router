---
title: PrefetchPageLinks
---

# PrefetchPageLinks

[MODES: framework]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.PrefetchPageLinks.html)

渲染 [`<link rel=prefetch|modulepreload>`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLLinkElement/rel) 标签，用于预取另一个页面的模块和数据，实现到该页面的即时导航。[`<Link prefetch>`](./Link#prefetch) 内部使用了此组件，但你也可以出于其他原因渲染它来预取页面。

例如，当用户在搜索框中输入时，你可以渲染此组件来预取搜索结果，以便在用户点击选择后即时导航。

```tsx
import { PrefetchPageLinks } from "react-router";

<PrefetchPageLinks page="/absolute/path" />;
```

## 函数签名

```tsx
function PrefetchPageLinks({
  page,
  ...linkProps
}: PageLinkDescriptor);
```

## Props

### page

要预取的页面的绝对路径，例如 `/absolute/path`。

### linkProps

扩展到 [`<link>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link) 标签上的额外 props，如 [`crossOrigin`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLLinkElement/crossOrigin)、[`integrity`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLLinkElement/integrity)、[`rel`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLLinkElement/rel) 等。
