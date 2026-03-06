---
title: createCallServer
unstable: true
---

# unstable_createCallServer

[MODES: data]

<br />
<br />

<docs-warning>此 API 是实验性的，可能在次要/补丁版本中发生破坏性变更。请谨慎使用，并**密切**关注发布说明中的相关变更。</docs-warning>

## 概述

为 React Router 创建 React `callServer` 实现。

```tsx
import {
  createFromReadableStream,
  createTemporaryReferenceSet,
  encodeReply,
  setServerCallback,
} from "@vitejs/plugin-rsc/browser";
import { unstable_createCallServer as createCallServer } from "react-router";

setServerCallback(
  createCallServer({
    createFromReadableStream,
    createTemporaryReferenceSet,
    encodeReply,
  }),
);
```

## 函数签名

```tsx
function createCallServer({
  createFromReadableStream,
  createTemporaryReferenceSet,
  encodeReply,
  fetch: fetchImplementation = fetch,
}: {
  createFromReadableStream: BrowserCreateFromReadableStreamFunction;
  createTemporaryReferenceSet: () => unknown;
  encodeReply: EncodeReplyFunction;
  fetch?: (request: Request) => Promise<Response>;
});
```

## 参数

### opts.createFromReadableStream

你的 `react-server-dom-xyz/client` 的 `createFromReadableStream`。用于解码来自服务器的载荷。

### opts.createTemporaryReferenceSet

创建 [RSC](https://react.dev/reference/rsc/server-components) 载荷临时引用集的函数。

### opts.encodeReply

你的 `react-server-dom-xyz/client` 的 `encodeReply`。在向服务器发送载荷时使用。

### opts.fetch

可选的 [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 实现。默认为全局 [`fetch`](https://developer.mozilla.org/en-US/docs/Web/API/fetch)。

## 返回值

可用于调用 server action 的函数。
