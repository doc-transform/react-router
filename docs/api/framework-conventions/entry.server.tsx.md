---
title: entry.server.tsx
order: 5
---

# entry.server.tsx

[MODES: framework]

## 概述

此文件是服务端入口，控制 React Router 应用在服务端如何生成 HTTP 响应。

此模块应使用 [`<ServerRouter>`][serverrouter] 元素及当前请求的 `context` 和 `url` 来渲染当前页面的标记。JavaScript 在浏览器中加载后，此标记将（可选地）通过[客户端入口模块][client-entry]重新注水。

<docs-info>如果你在 Node 上运行，此文件是可选的。如果不存在，将使用[默认实现][node-streaming-entry-server]。
<br/>
<br/>
如果你使用其他运行时（如 Cloudflare），则需要包含此文件。你可以在[模板仓库][templates-repo]中找到示例实现。</docs-info>

## 生成 `entry.server.tsx`

在 Node 环境运行时，React Router 会为你处理 HTTP 响应的生成。你可以通过以下命令显示默认的服务端入口文件：

```shellscript nonumber
npx react-router reveal
```

## 导出

### `default`

此模块的 `default` 导出是一个函数，允许你创建响应，包括 HTTP 状态码、响应头和 HTML，让你完全控制标记的生成和发送方式。

```tsx filename=app/entry.server.tsx
import { PassThrough } from "node:stream";
import type { EntryContext } from "react-router";
import { createReadableStreamFromReadable } from "@react-router/node";
import { ServerRouter } from "react-router";
import { renderToPipeableStream } from "react-dom/server";

export default function handleRequest(
  request: Request,
  responseStatusCode: number,
  responseHeaders: Headers,
  routerContext: EntryContext,
) {
  return new Promise((resolve, reject) => {
    const { pipe, abort } = renderToPipeableStream(
      <ServerRouter
        context={routerContext}
        url={request.url}
      />,
      {
        onShellReady() {
          responseHeaders.set("Content-Type", "text/html");

          const body = new PassThrough();
          const stream =
            createReadableStreamFromReadable(body);

          resolve(
            new Response(stream, {
              headers: responseHeaders,
              status: responseStatusCode,
            }),
          );

          pipe(body);
        },
        onShellError(error: unknown) {
          reject(error);
        },
      },
    );
  });
}
```

### `streamTimeout`

如果你使用[流式传输][streaming]响应，可以导出一个可选的 `streamTimeout` 值（单位毫秒），用于控制服务器在拒绝未完成的 Promise 并关闭流之前等待流式 Promise 结算的时间。

建议将此值与中止 React 渲染器的超时时间解耦。你应该始终将 React 渲染超时设为更高的值，以便有时间将 `streamTimeout` 产生的拒绝流式传输下去。

```tsx lines=[1-2,13-15]
// 10 秒后拒绝处理函数中所有待处理的 Promise
export const streamTimeout = 10000;

export default function handleRequest(...) {
  return new Promise((resolve, reject) => {
    // ...

    const { pipe, abort } = renderToPipeableStream(
      <ServerRouter context={routerContext} url={request.url} />,
      { /* ... */ }
    );

    // 11 秒后中止流式渲染，以允许被拒绝的边界被刷新
    setTimeout(abort, streamTimeout + 1000);
  });
}
```

### `handleDataRequest`

你可以导出一个可选的 `handleDataRequest` 函数，允许你修改数据请求的响应。这些请求不渲染 HTML，而是在客户端注水完成后将 `loader` 和 `action` 数据返回给浏览器。

```tsx
export function handleDataRequest(
  response: Response,
  {
    request,
    params,
    context,
  }: LoaderFunctionArgs | ActionFunctionArgs,
) {
  response.headers.set("X-Custom-Header", "value");
  return response;
}
```

### `handleError`

默认情况下，React Router 会将遇到的服务端错误记录到控制台。如果你想更精细地控制日志记录，或者还想将这些错误上报到外部服务，可以导出一个可选的 `handleError` 函数（这将禁用内置的错误日志记录）。

```tsx
export function handleError(
  error: unknown,
  {
    request,
    params,
    context,
  }: LoaderFunctionArgs | ActionFunctionArgs,
) {
  if (!request.signal.aborted) {
    sendErrorToErrorReportingService(error);
    console.error(formatErrorForJsonLogging(error));
  }
}
```

_注意：通常你需要避免在请求被中止时记录日志，因为 React Router 的取消和竞态条件处理可能会导致大量请求被中止。_

**流式渲染错误**

当你通过 [`renderToPipeableStream`][rendertopipeablestream] 或 [`renderToReadableStream`][rendertoreadablestream] 流式传输 HTML 响应时，你自定义的 `handleError` 实现只会处理初始 shell 渲染期间遇到的错误。如果在后续的流式渲染中遇到渲染错误，你需要手动处理这些错误，因为此时 React Router 服务器已经发送了响应。

对于 `renderToPipeableStream`，你可以在 `onError` 回调函数中处理这些错误。你需要在 `onShellReady` 中切换一个布尔值，以便知道错误是 shell 渲染错误（可以忽略）还是异步错误。

具体示例请参阅 Node 的默认 [`entry.server.tsx`][node-streaming-entry-server]。

**抛出的 Response**

注意：这不会处理从 `loader`/`action` 函数中抛出的 `Response` 实例。此处理器的目的是发现代码中导致意外抛出错误的 bug。如果你在 `loader`/`action` 中检测到某种情况并抛出 401/404 等 `Response`，这是由你的代码处理的预期流程。如果你也想记录日志或将其发送到外部服务，应在抛出响应时完成。

[client-entry]: ./entry.client.tsx
[serverrouter]: ../framework-routers/ServerRouter
[streaming]: ../../how-to/suspense
[rendertopipeablestream]: https://react.dev/reference/react-dom/server/renderToPipeableStream
[rendertoreadablestream]: https://react.dev/reference/react-dom/server/renderToReadableStream
[node-streaming-entry-server]: https://github.com/remix-run/react-router/blob/dev/packages/react-router-dev/config/defaults/entry.server.node.tsx
[templates-repo]: https://github.com/remix-run/react-router-templates
