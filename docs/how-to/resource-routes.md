---
title: 资源路由
---

# 资源路由

[MODES: framework, data]

<br/>
<br/>

在服务端渲染时，路由可以提供"资源"而不是渲染组件，例如图片、PDF、JSON 数据、webhook 等。

## 定义资源路由

按照约定，当路由模块导出了 loader 或 action 但没有导出默认组件时，该路由就成为资源路由。

考虑一个提供 PDF 而非 UI 的路由：

```ts
route("/reports/pdf/:id", "pdf-report.ts");
```

```tsx filename=pdf-report.ts
import type { Route } from "./+types/pdf-report";

export async function loader({ params }: Route.LoaderArgs) {
  const report = await getReport(params.id);
  const pdf = await generateReportPDF(report);
  return new Response(pdf, {
    status: 200,
    headers: {
      "Content-Type": "application/pdf",
    },
  });
}
```

注意这里没有默认导出。这使得该路由成为资源路由。

## 链接到资源路由

链接到资源路由时，请使用 `<a>` 或 `<Link reloadDocument>`，否则 React Router 将尝试使用客户端路由并获取载荷（如果你犯了这个错误，会收到一个有用的错误消息）。

```tsx
<Link reloadDocument to="/reports/pdf/123">
  查看 PDF
</Link>
```

## 处理不同的请求方法

GET 请求由 `loader` 处理，而 POST、PUT、PATCH 和 DELETE 由 `action` 处理：

```tsx
import type { Route } from "./+types/resource";

export function loader(_: Route.LoaderArgs) {
  return Response.json({ message: "我处理 GET 请求" });
}

export function action(_: Route.ActionArgs) {
  return Response.json({
    message: "我处理其他所有请求",
  });
}
```

## 返回类型

资源路由在返回类型方面很灵活——你可以返回 [`Response`][Response] 实例或 [`data()`][data] 对象。决定使用哪种类型时，一个好的经验法则是：

- 如果你的资源路由用于外部消费，返回 `Response` 实例
  - 保持结果响应编码在代码中是显式的，而不必猜测 React Router 内部如何将 `data() -> Response` 转换
- 如果你通过 [fetcher][fetcher] 或 [`<Form>`][form] 提交来访问资源路由，返回 `data()`
  - 与 UI 路由中的 loader/action 保持一致
  - 允许你通过 `data()`/[`Await`][await] 将 promise 流式传输到 UI

## 错误处理

从资源路由抛出 `Error`（或除 `Response`/`data()` 之外的任何内容）将触发 [`handleError`][handleError] 并返回 500 HTTP 响应：

```tsx
export function action() {
  let db = await getDb();
  if (!db) {
    // 致命错误 - 返回 500 响应并触发 `handleError`
    throw new Error("无法连接到数据库");
  }
  // ...
}
```

如果资源路由生成了 `Response`（通过 `new Response()` 或 `data()`），则被视为成功执行，不会触发 `handleError`，因为 API 已经成功为 HTTP 请求生成了 Response。这适用于抛出的响应以及带有 4xx/5xx 状态码的返回响应。此行为与 `fetch()` 一致，后者不会对 4xx/5xx 响应返回被拒绝的 promise。

```tsx
export function action() {
  // 非致命错误 - 不触发 `handleError`：
  throw new Response({ error: "未授权" }, { status: 401 });

  // 以下 3 种写法与上面等效
  return new Response({ error: "未授权" }, { status: 401 });

  throw data({ error: "未授权" }, { status: 401 });

  return data({ error: "未授权" }, { status: 401 });
}
```

### 错误边界

[错误边界][error-boundary]仅在从 UI 访问资源路由时适用，例如从 [`fetcher`][fetcher] 调用或 [`<Form>`][form] 提交。如果你在这些情况下从资源路由 `throw`，错误将冒泡到 UI 中最近的 `ErrorBoundary`。

[handleError]: ../api/framework-conventions/entry.server.tsx#handleerror
[data]: ../api/utils/data
[Response]: https://developer.mozilla.org/en-US/docs/Web/API/Response
[fetcher]: ../api/hooks/useFetcher
[form]: ../api/components/Form
[await]: ../api/components/Await
[error-boundary]: ../start/framework/route-module#errorboundary
