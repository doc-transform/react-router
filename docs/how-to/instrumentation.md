---
title: 可观测性
unstable: true
---

# 可观测性

[MODES: framework, data]

<br/>
<br/>

<docs-warning>可观测性 API 是实验性的，可能在次要/补丁版本中发生破坏性变更。请谨慎使用，并**密切**关注发布说明中的相关变更。</docs-warning>

可观测性（Instrumentation）允许你在不修改实际路由处理器的情况下，为 React Router 应用添加日志记录、错误上报和性能追踪。这使得你可以在服务端和客户端为生产应用实现全面的可观测性方案。

## 概述

通过 React Router Instrumentation API，你提供在请求处理器、路由器操作、路由中间件和/或路由处理器周围执行的"包装"函数。这允许你：

- 监控应用性能
- 添加日志记录
- 集成可观测性平台（Sentry、DataDog、New Relic 等）
- 实现 OpenTelemetry 追踪
- 追踪用户行为和导航模式

一个关键的设计原则是 instrumentation 是**只读的** —— 你可以观察正在发生的事情，但不能通过修改传递给路由处理器的参数或从中返回的数据来修改运行时应用行为。

<docs-info>
与任何 instrumentation 方法一样，在运行时添加额外的代码执行可能会改变与未添加 instrumentation 的应用相比的性能特征。请牢记这一点，并进行适当的测试和/或利用条件 instrumentation 来避免对生产环境的用户体验产生负面影响。
</docs-info>

## 快速开始（框架模式）

[modes: framework]

### 1. 服务端 Instrumentation

在 `entry.server.tsx` 中添加 instrumentation：

```tsx filename=app/entry.server.tsx
export const unstable_instrumentations = [
  {
    // 对服务端处理器进行 instrument
    handler(handler) {
      handler.instrument({
        async request(handleRequest, { request }) {
          let url = `${request.method} ${request.url}`;
          console.log(`请求开始: ${url}`);
          await handleRequest();
          console.log(`请求结束: ${url}`);
        },
      });
    },

    // 对各个路由进行 instrument
    route(route) {
      // 如果需要，跳过特定路由的 instrumentation
      if (route.id === "root") return;

      route.instrument({
        async loader(callLoader, { request }) {
          let url = `${request.method} ${request.url}`;
          console.log(`Loader 开始: ${url} - ${route.id}`);
          await callLoader();
          console.log(`Loader 结束: ${url} - ${route.id}`);
        },
        // 其他可用的 instrumentation：
        // async action() { /* ... */ },
        // async middleware() { /* ... */ },
        // async lazy() { /* ... */ },
      });
    },
  },
];

export default function handleRequest(/* ... */) {
  // 你现有的 handleRequest 实现
}
```

### 2. 客户端 Instrumentation

在 `entry.client.tsx` 中添加 instrumentation：

```tsx filename=app/entry.client.tsx
import { startTransition, StrictMode } from "react";
import { hydrateRoot } from "react-dom/client";
import { HydratedRouter } from "react-router/dom";

const unstable_instrumentations = [
  {
    // 对路由器操作进行 instrument
    router(router) {
      router.instrument({
        // 对导航进行 instrument
        async navigate(callNavigate, { currentUrl, to }) {
          let nav = `${currentUrl} → ${to}`;
          console.log(`导航开始: ${nav}`);
          await callNavigate();
          console.log(`导航结束: ${nav}`);
        },
        // 对 fetcher 调用进行 instrument
        async fetch(
          callFetch,
          { href, currentUrl, fetcherKey },
        ) {
          let fetch = `${fetcherKey} → ${href}`;
          console.log(`Fetcher 开始: ${fetch}`);
          await callFetch();
          console.log(`Fetcher 结束: ${fetch}`);
        },
      });
    },

    // 对各个路由进行 instrument（与服务端相同）
    route(route) {
      // 如果需要，跳过特定路由的 instrumentation
      if (route.id === "root") return;

      route.instrument({
        async loader(callLoader, { request }) {
          let url = `${request.method} ${request.url}`;
          console.log(`Loader 开始: ${url} - ${route.id}`);
          await callLoader();
          console.log(`Loader 结束: ${url} - ${route.id}`);
        },
        // 其他可用的 instrumentation：
        // async action() { /* ... */ },
        // async middleware() { /* ... */ },
        // async lazy() { /* ... */ },
      });
    },
  },
];

startTransition(() => {
  hydrateRoot(
    document,
    <StrictMode>
      <HydratedRouter
        unstable_instrumentations={
          unstable_instrumentations
        }
      />
    </StrictMode>,
  );
});
```

## 快速开始（数据模式）

[modes: data]

在数据模式中，创建路由器时添加 instrumentation：

```tsx
import {
  createBrowserRouter,
  RouterProvider,
} from "react-router";

const unstable_instrumentations = [
  {
    // 对路由器操作进行 instrument
    router(router) {
      router.instrument({
        // 对导航进行 instrument
        async navigate(callNavigate, { currentUrl, to }) {
          let nav = `${currentUrl} → ${to}`;
          console.log(`导航开始: ${nav}`);
          await callNavigate();
          console.log(`导航结束: ${nav}`);
        },
        // 对 fetcher 调用进行 instrument
        async fetch(
          callFetch,
          { href, currentUrl, fetcherKey },
        ) {
          let fetch = `${fetcherKey} → ${href}`;
          console.log(`Fetcher 开始: ${fetch}`);
          await callFetch();
          console.log(`Fetcher 结束: ${fetch}`);
        },
      });
    },

    // 对各个路由进行 instrument（与服务端相同）
    route(route) {
      // 如果需要，跳过特定路由的 instrumentation
      if (route.id === "root") return;

      route.instrument({
        async loader(callLoader, { request }) {
          let url = `${request.method} ${request.url}`;
          console.log(`Loader 开始: ${url} - ${route.id}`);
          await callLoader();
          console.log(`Loader 结束: ${url} - ${route.id}`);
        },
        // 其他可用的 instrumentation：
        // async action() { /* ... */ },
        // async middleware() { /* ... */ },
        // async lazy() { /* ... */ },
      });
    },
  },
];

const router = createBrowserRouter(routes, {
  unstable_instrumentations,
});

function App() {
  return <RouterProvider router={router} />;
}
```

## 核心概念

### Instrumentation 层级

你可以在不同层级对应用进行 instrument。每个 instrumentation 函数接收第二个"info"参数，包含被 instrument 的特定方面的相关上下文信息。

#### 1. 处理器层级（服务端）

[modes: framework]

对处理所有服务器请求的顶级请求处理器进行 instrument：

```tsx filename=entry.server.tsx
export const unstable_instrumentations = [
  {
    handler(handler) {
      handler.instrument({
        async request(handleRequest, { request, context }) {
          // 在应用的所有请求周围运行
          await handleRequest();
        },
      });
    },
  },
];
```

#### 2. 路由器层级（客户端）

[modes: framework,data]

对客户端路由器操作（如导航和 fetcher 调用）进行 instrument：

```tsx
export const unstable_instrumentations = [
  {
    router(router) {
      router.instrument({
        async navigate(callNavigate, { to, currentUrl }) {
          // 在导航操作周围运行
          await callNavigate();
        },
        async fetch(
          callFetch,
          { href, currentUrl, fetcherKey },
        ) {
          // 在 fetcher 操作周围运行
          await callFetch();
        },
      });
    },
  },
];

// 框架模式 (entry.client.tsx)
<HydratedRouter
  unstable_instrumentations={unstable_instrumentations}
/>;

// 数据模式
const router = createBrowserRouter(routes, {
  unstable_instrumentations,
});
```

#### 3. 路由层级（服务端 + 客户端）

[modes: framework,data]

对各个路由处理器进行 instrument：

```tsx
const unstable_instrumentations = [
  {
    route(route) {
      route.instrument({
        async loader(
          callLoader,
          { params, request, context, unstable_pattern },
        ) {
          // 在 loader 执行周围运行
          await callLoader();
        },
        async action(
          callAction,
          { params, request, context, unstable_pattern },
        ) {
          // 在 action 执行周围运行
          await callAction();
        },
        async middleware(
          callMiddleware,
          { params, request, context, unstable_pattern },
        ) {
          // 在中间件执行周围运行
          await callMiddleware();
        },
        async lazy(callLazy) {
          // 在懒加载路由加载周围运行
          await callLazy();
        },
      });
    },
  },
];
```

### 只读设计

Instrumentation 被设计为**仅用于观察**。你不能：

- 修改传递给处理器的参数
- 更改处理器的返回值
- 改变应用行为

这确保了 instrumentation 可以安全地添加到生产应用中，不会在路由逻辑中引入 bug。

### 错误处理

为了确保 instrumentation 代码不会影响运行时应用，错误会在内部被捕获并阻止向外传播。这个设计选择体现在两个方面。

首先，如果"处理器"函数（loader、action、请求处理器、导航等）抛出错误，该错误不会从你的 instrumentation 中调用的 `callHandler` 函数冒出。相反，`callHandler` 函数返回一个类型为 `{ type: "success", error: undefined } | { type: "error", error: unknown }` 的可区分联合结果。这确保你的整个 instrumentation 函数可以在不需要任何 try/catch/finally 逻辑来处理应用错误的情况下运行。

```tsx
export const unstable_instrumentations = [
  {
    route(route) {
      route.instrument({
        async loader(callLoader) {
          let { status, error } = await callLoader();

          if (status === "error") {
            // 错误情况 - `error` 有值
          } else {
            // 成功情况 - `error` 为 undefined
          }
        },
      });
    },
  },
];
```

其次，如果你的 instrumentation 函数抛出错误，React Router 会优雅地吞掉该错误，使其不会向外冒泡影响其他 instrumentation 或应用行为。在以下两个示例中，处理器和所有其他 instrumentation 函数仍然会运行：

```tsx
export const unstable_instrumentations = [
  {
    route(route) {
      route.instrument({
        // 在调用处理器之前抛出 - RR 将捕获错误并仍然调用 loader
        async loader(callLoader) {
          somethingThatThrows();
          await callLoader();
        },
        // 在调用处理器之后抛出 - RR 将在内部捕获错误
        async action(callAction) {
          await callAction();
          somethingThatThrows();
        },
      });
    },
  },
];
```

### 组合

你可以通过提供数组来组合多个 instrumentation：

```tsx
export const unstable_instrumentations = [
  loggingInstrumentation,
  performanceInstrumentation,
  errorReportingInstrumentation,
];
```

每个 instrumentation 包装前一个，创建嵌套的执行链。

### 条件 Instrumentation

你可以根据环境或其他因素有条件地启用 instrumentation：

```tsx
export const unstable_instrumentations =
  process.env.NODE_ENV === "production"
    ? [productionInstrumentation]
    : [developmentInstrumentation];
```

```tsx
// 或在 instrumentation 内部条件判断
export const unstable_instrumentations = [
  {
    route(route) {
      // 只对特定路由进行 instrument
      if (!route.id?.startsWith("routes/admin")) return;

      // 或者只在存在查询参数时进行 instrument
      let sp = new URL(request.url).searchParams;
      if (!sp.has("DEBUG")) return;

      route.instrument({
        async loader() {
          /* ... */
        },
      });
    },
  },
];
```

## 常见模式

### 请求日志（服务端）

```tsx
const logging: unstable_ServerInstrumentation = {
  handler({ instrument }) {
    instrument({
      request: (fn, { request }) =>
        log(`请求 ${request.url}`, fn),
    });
  },
  route({ instrument, id }) {
    instrument({
      middleware: (fn) => log(` 中间件 (${id})`, fn),
      loader: (fn) => log(`  loader (${id})`, fn),
      action: (fn) => log(`  action (${id})`, fn),
    });
  },
};

async function log(
  label: string,
  cb: () => Promise<unstable_InstrumentationHandlerResult>,
) {
  let start = Date.now();
  console.log(`➡️ ${label}`);
  await cb();
  console.log(`⬅️ ${label} (${Date.now() - start}ms)`);
}

export const unstable_instrumentations = [logging];
```

### OpenTelemetry 集成

```tsx
import { trace, SpanStatusCode } from "@opentelemetry/api";

const tracer = trace.getTracer("my-app");

const otel: unstable_ServerInstrumentation = {
  handler({ instrument }) {
    instrument({
      request: (fn, { request }) =>
        otelSpan(`请求`, { url: request.url }, fn),
    });
  },
  route({ instrument, id }) {
    instrument({
      middleware: (fn, { unstable_pattern }) =>
        otelSpan(
          "中间件",
          { routeId: id, pattern: unstable_pattern },
          fn,
        ),
      loader: (fn, { unstable_pattern }) =>
        otelSpan(
          "loader",
          { routeId: id, pattern: unstable_pattern },
          fn,
        ),
      action: (fn, { unstable_pattern }) =>
        otelSpan(
          "action",
          { routeId: id, pattern: unstable_pattern },
          fn,
        ),
    });
  },
};

async function otelSpan(
  label: string,
  attributes: Record<string, string>,
  cb: () => Promise<unstable_InstrumentationHandlerResult>,
) {
  return tracer.startActiveSpan(
    label,
    { attributes },
    async (span) => {
      let { error } = await cb();
      if (error) {
        span.recordException(error);
        span.setStatus({
          code: SpanStatusCode.ERROR,
        });
      }
      span.end();
    },
  );
}

export const unstable_instrumentations = [otel];
```

### 客户端性能追踪

```tsx
const windowPerf: unstable_ClientInstrumentation = {
  router({ instrument }) {
    instrument({
      navigate: (fn, { to, currentUrl }) =>
        measure(`导航:${currentUrl}->${to}`, fn),
      fetch: (fn, { href }) =>
        measure(`fetcher:${href}`, fn),
    });
  },
  route({ instrument, id }) {
    instrument({
      middleware: (fn) => measure(`中间件:${id}`, fn),
      loader: (fn) => measure(`loader:${id}`, fn),
      action: (fn) => measure(`action:${id}`, fn),
    });
  },
};

async function measure(
  label: string,
  cb: () => Promise<unstable_InstrumentationHandlerResult>,
) {
  performance.mark(`start:${label}`);
  await cb();
  performance.mark(`end:${label}`);
  performance.measure(
    label,
    `start:${label}`,
    `end:${label}`,
  );
}

<HydratedRouter unstable_instrumentations={[windowPerf]} />;
```
