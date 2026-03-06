---
title: routeRSCServerRequest
unstable: true
---

# unstable_routeRSCServerRequest

[MODES: data]

<br />
<br />

<docs-warning>此 API 是实验性的，可能在次要/补丁版本中发生破坏性变更。请谨慎使用，并**密切**关注发布说明中的相关变更。</docs-warning>

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.unstable_routeRSCServerRequest.html)

将传入的 [`Request`](https://developer.mozilla.org/en-US/docs/Web/API/Request) 路由到 [RSC](https://react.dev/reference/rsc/server-components) 服务器，并针对数据/资源请求代理服务器响应，或为文档请求渲染 HTML。

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
async function routeRSCServerRequest({
  request,
  serverResponse,
  createFromReadableStream,
  renderHTML,
  hydrate = true,
}: {
  request: Request;
  serverResponse: Response;
  createFromReadableStream: SSRCreateFromReadableStreamFunction;
  renderHTML: (
    getPayload: () => DecodedPayload,
    options: {
      onError(error: unknown): string | undefined;
      onHeaders(headers: Headers): void;
    },
  ) =>
    | ReadableStream<Uint8Array>
    | Promise<ReadableStream<Uint8Array>>;
  hydrate?: boolean;
}): Promise<Response>;
```

## 参数

### opts.createFromReadableStream

你的 `react-server-dom-xyz/client` 的 `createFromReadableStream` 函数，用于解码来自服务器的载荷。

### opts.serverResponse

由 [RSC](https://react.dev/reference/rsc/server-components) 处理器生成的 Response 或部分响应，包含序列化的 [`unstable_RSCPayload`](https://api.reactrouter.com/v7/types/react-router.unstable_RSCPayload.html)。

### opts.hydrate

是否使用 RSC 载荷注水服务器响应。默认为 `true`。

### opts.renderHTML

将 [`unstable_RSCPayload`](https://api.reactrouter.com/v7/types/react-router.unstable_RSCPayload.html) 渲染为 HTML 的函数，通常使用 [`<RSCStaticRouter>`](../rsc/RSCStaticRouter)。

### opts.request

要路由的请求。

## 返回值

包含数据请求的 [RSC](https://react.dev/reference/rsc/server-components) 载荷，或文档请求渲染 HTML 的 [`Response`](https://developer.mozilla.org/en-US/docs/Web/API/Response)。
