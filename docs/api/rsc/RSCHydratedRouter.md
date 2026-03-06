---
title: RSCHydratedRouter
unstable: true
---

# unstable_RSCHydratedRouter

[MODES: data]

<br />
<br />

<docs-warning>此 API 是实验性的，可能在次要/补丁版本中发生破坏性变更。请谨慎使用，并**密切**关注发布说明中的相关变更。</docs-warning>

## 概述

在浏览器中注水服务端渲染的 [`unstable_RSCPayload`](https://api.reactrouter.com/v7/types/react-router.unstable_RSCPayload.html)。

```tsx
import { startTransition, StrictMode } from "react";
import { hydrateRoot } from "react-dom/client";
import {
  unstable_getRSCStream as getRSCStream,
  unstable_RSCHydratedRouter as RSCHydratedRouter,
} from "react-router";
import type { unstable_RSCPayload as RSCPayload } from "react-router";

createFromReadableStream(getRSCStream()).then((payload) =>
  startTransition(async () => {
    hydrateRoot(
      document,
      <StrictMode>
        <RSCHydratedRouter
          createFromReadableStream={
            createFromReadableStream
          }
          payload={payload}
        />
      </StrictMode>,
      { formState: await getFormState(payload) },
    );
  }),
);
```

## 函数签名

```tsx
function RSCHydratedRouter({
  createFromReadableStream,
  fetch: fetchImplementation = fetch,
  payload,
  routeDiscovery = "eager",
  getContext,
}: RSCHydratedRouterProps);
```

## Props

### createFromReadableStream

你的 `react-server-dom-xyz/client` 的 `createFromReadableStream` 函数，用于解码来自服务器的载荷。

### fetch

可选的 fetch 实现。默认为全局 [`fetch`](https://developer.mozilla.org/en-US/docs/Web/API/fetch)。

### getContext

返回 [`RouterContextProvider`](../utils/RouterContextProvider) 实例的函数，作为客户端 [`action`](../../start/data/route-object#action)、[`loader`](../../start/data/route-object#loader) 和[中间件](../../how-to/middleware)的 `context` 参数提供。每次导航或 fetcher 调用都会调用此函数生成新的 `context` 实例。

### payload

要注水的已解码 [`unstable_RSCPayload`](https://api.reactrouter.com/v7/types/react-router.unstable_RSCPayload.html)。

### routeDiscovery

`"eager"` 或 `"lazy"` - 决定链接是立即发现还是延迟到点击时发现。
