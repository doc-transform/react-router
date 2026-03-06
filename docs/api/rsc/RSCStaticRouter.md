---
title: RSCStaticRouter
unstable: true
---

# unstable_RSCStaticRouter

[MODES: data]

<br />
<br />

<docs-warning>此 API 是实验性的，可能在次要/补丁版本中发生破坏性变更。请谨慎使用，并**密切**关注发布说明中的相关变更。</docs-warning>

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.unstable_RSCStaticRouter.html)

将 [`unstable_RSCPayload`](https://api.reactrouter.com/v7/types/react-router.unstable_RSCPayload.html) 预渲染为 HTML。通常在 [`unstable_routeRSCServerRequest`](../rsc/routeRSCServerRequest) 的 `renderHTML` 回调中使用。

```tsx
import { createFromReadableStream } from "@vitejs/plugin-rsc/ssr";
import * as ReactDomServer from "react-dom/server.edge";
import {
  unstable_RSCStaticRouter as RSCStaticRouter,
  unstable_routeRSCServerRequest as routeRSCServerRequest,
} from "react-router";

routeRSCServerRequest({
  request,
  serverResponse,
  createFromReadableStream,
  async renderHTML(getPayload) {
    const payload = getPayload();

    return await renderHTMLToReadableStream(
      <RSCStaticRouter getPayload={getPayload} />,
      {
        bootstrapScriptContent,
        formState: await payload.formState,
      },
    );
  },
});
```

## 函数签名

```tsx
function RSCStaticRouter({
  getPayload,
}: RSCStaticRouterProps);
```

## Props

### getPayload

开始解码 [`unstable_RSCPayload`](https://api.reactrouter.com/v7/types/react-router.unstable_RSCPayload.html) 的函数。通常从 [`unstable_routeRSCServerRequest`](../rsc/routeRSCServerRequest) 的 `renderHTML` 中传入。
