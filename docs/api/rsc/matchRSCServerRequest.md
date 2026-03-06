---
title: matchRSCServerRequest
unstable: true
---

# unstable_matchRSCServerRequest

[MODES: data]

<br />
<br />

<docs-warning>此 API 是实验性的，可能在次要/补丁版本中发生破坏性变更。请谨慎使用，并**密切**关注发布说明中的相关变更。</docs-warning>

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/variables/react-router.unstable_matchRSCServerRequest.html)

将给定路由匹配到 [`Request`](https://developer.mozilla.org/en-US/docs/Web/API/Request)，并返回一个 [RSC](https://react.dev/reference/rsc/server-components) [`Response`](https://developer.mozilla.org/en-US/docs/Web/API/Response)，编码一个 [`unstable_RSCPayload`](https://api.reactrouter.com/v7/types/react-router.unstable_RSCPayload.html) 供支持 [RSC](https://react.dev/reference/rsc/server-components) 的客户端路由器使用。

```tsx
import {
  createTemporaryReferenceSet,
  decodeAction,
  decodeReply,
  loadServerAction,
  renderToReadableStream,
} from "@vitejs/plugin-rsc/rsc";
import { unstable_matchRSCServerRequest as matchRSCServerRequest } from "react-router";

matchRSCServerRequest({
  createTemporaryReferenceSet,
  decodeAction,
  decodeFormState,
  decodeReply,
  loadServerAction,
  request,
  routes: routes(),
  generateResponse(match) {
    return new Response(
      renderToReadableStream(match.payload),
      {
        status: match.statusCode,
        headers: match.headers,
      },
    );
  },
});
```

## 函数签名

```tsx
async function matchRSCServerRequest({
  allowedActionOrigins,
  createTemporaryReferenceSet,
  basename,
  decodeReply,
  requestContext,
  loadServerAction,
  decodeAction,
  decodeFormState,
  onError,
  request,
  routes,
  generateResponse,
}: {
  allowedActionOrigins?: string[];
  createTemporaryReferenceSet: () => unknown;
  basename?: string;
  decodeReply?: DecodeReplyFunction;
  decodeAction?: DecodeActionFunction;
  decodeFormState?: DecodeFormStateFunction;
  requestContext?: RouterContextProvider;
  loadServerAction?: LoadServerActionFunction;
  onError?: (error: unknown) => void;
  request: Request;
  routes: RSCRouteConfigEntry[];
  generateResponse: (
    match: RSCMatch,
    {
      onError,
      temporaryReferences,
    }: {
      onError(error: unknown): string | undefined;
      temporaryReferences: unknown;
    },
  ) => Response;
}): Promise<Response>;
```

## 参数

### opts.allowedActionOrigins

允许执行 action 的来源模式。

### opts.basename

匹配请求时使用的基础路径。

### opts.createTemporaryReferenceSet

返回请求的临时引用集的函数，用于跟踪 [RSC](https://react.dev/reference/rsc/server-components) 流中的临时引用。

### opts.decodeAction

你的 `react-server-dom-xyz/server` 的 `decodeAction` 函数，负责加载 server action。

### opts.decodeFormState

负责解码表单状态的函数，用于通过 React 的 [`useActionState`](https://react.dev/reference/react/useActionState) 使用 `react-server-dom-xyz/server` 的 `decodeFormState` 实现渐进增强的表单。

### opts.decodeReply

你的 `react-server-dom-xyz/server` 的 `decodeReply` 函数，用于解码服务器函数的参数并将其绑定到实现以供路由器调用。

### opts.generateResponse

负责使用你的 `renderToReadableStream` 生成 [`Response`](https://developer.mozilla.org/en-US/docs/Web/API/Response) 的函数，编码 [`unstable_RSCPayload`](https://api.reactrouter.com/v7/types/react-router.unstable_RSCPayload.html)。

### opts.loadServerAction

你的 `react-server-dom-xyz/server` 的 `loadServerAction` 函数，通过 ID 加载 server action。

### opts.onError

可选的错误处理器，在请求处理期间发生任何错误时调用。

### opts.request

要匹配的 [`Request`](https://developer.mozilla.org/en-US/docs/Web/API/Request)。

### opts.requestContext

每个请求应创建的 [`RouterContextProvider`](../utils/RouterContextProvider) 实例，传递给 [`action`](../../start/data/route-object#action)、[`loader`](../../start/data/route-object#loader) 和[中间件](../../how-to/middleware)。

### opts.routes

你的[路由定义](https://api.reactrouter.com/v7/types/react-router.unstable_RSCRouteConfigEntry.html)。

## 返回值

包含用于注水的 [RSC](https://react.dev/reference/rsc/server-components) 数据的 [`Response`](https://developer.mozilla.org/en-US/docs/Web/API/Response)。
