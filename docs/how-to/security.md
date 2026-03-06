---
title: 安全
---

# 安全

[MODES: framework]

<br/>
<br/>

这绝不是一份全面的指南，但 React Router 提供了一些功能来帮助解决 _安全_ 这个 _非常大_ 的话题下的几个方面。

## `Content-Security-Policy`

如果你在应用中实现了[内容安全策略 (CSP)][csp]，特别是使用了 `unsafe-inline` 指令的策略，你需要在 HTML 中渲染的内联 `<script>` 元素上指定 [`nonce`][nonce] 属性。这必须在所有生成内联脚本的 API 上指定，包括：

- [`<Scripts nonce>`][scripts]（`root.tsx`）
- [`<ScrollRestoration nonce>`][scrollrestoration]（`root.tsx`）
- [`<ServerRouter nonce>`][serverrouter]（`entry.server.tsx`）
- [`renderToPipeableStream(..., { nonce })`][renderToPipeableStream]（`entry.server.tsx`）
- [`renderToReadableStream(..., { nonce })`][renderToReadableStream]（`entry.server.tsx`）

[csp]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CSP
[nonce]: https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/nonce
[renderToPipeableStream]: https://react.dev/reference/react-dom/server/renderToPipeableStream
[renderToReadableStream]: https://react.dev/reference/react-dom/server/renderToReadableStream
[scripts]: ../api/components/Scripts
[scrollrestoration]: ../api/components/ScrollRestoration
[serverrouter]: ../api/components/ServerRouter
