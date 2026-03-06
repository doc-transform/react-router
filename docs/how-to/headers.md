---
title: HTTP 响应头
---

# HTTP 响应头

[MODES: framework]

<br/>
<br/>

响应头主要通过路由模块的 `headers` 导出来定义。你也可以在 `entry.server.tsx` 中设置响应头。

## 从路由模块

```tsx filename=some-route.tsx
import { Route } from "./+types/some-route";

export function headers(_: Route.HeadersArgs) {
  return {
    "Content-Security-Policy": "default-src 'self'",
    "X-Frame-Options": "DENY",
    "X-Content-Type-Options": "nosniff",
    "Cache-Control": "max-age=3600, s-maxage=86400",
  };
}
```

你可以返回 [`Headers`](https://developer.mozilla.org/en-US/docs/Web/API/Headers) 实例或 `HeadersInit`。

## 从 loader 和 action

当响应头依赖于 loader 数据时，loader 和 action 也可以设置响应头。

### 1. 将返回值包裹在 `data` 中

```tsx lines=[1,8]
import { data } from "react-router";

export async function loader({ params }: LoaderArgs) {
  let [page, ms] = await fakeTimeCall(
    await getPage(params.id),
  );

  return data(page, {
    headers: {
      "Server-Timing": `page;dur=${ms};desc="Page query"`,
    },
  });
}
```

### 2. 从 `headers` 导出返回

来自 loader 和 action 的响应头不会自动发送。你必须在 `headers` 导出中显式返回它们。

```tsx
function hasAnyHeaders(headers: Headers): boolean {
  return [...headers].length > 0;
}

export function headers({
  actionHeaders,
  loaderHeaders,
}: HeadersArgs) {
  return hasAnyHeaders(actionHeaders)
    ? actionHeaders
    : loaderHeaders;
}
```

一个值得注意的例外是 `Set-Cookie` 响应头，它会从父路由的 `headers`、`loader` 和 `action` 中自动保留，即使子路由没有导出 `headers`。

## 与父路由响应头合并

考虑这些嵌套路由：

```ts filename=routes.ts
route("pages", "pages-layout-with-nav.tsx", [
  route(":slug", "page.tsx"),
]);
```

如果两个路由模块都想设置响应头，将发送最深层匹配路由的响应头。

当你需要同时保留父级和子级的响应头时，需要在子路由中合并它们。

### 追加

最简单的方式是直接追加到父级响应头。这样可以避免覆盖父级可能设置的重要响应头。

```tsx
export function headers({ parentHeaders }: HeadersArgs) {
  parentHeaders.append(
    "Permissions-Policy: geolocation=()",
  );
  return parentHeaders;
}
```

### 设置

有时覆盖父级响应头很重要。使用 `set` 而不是 `append`：

```tsx
export function headers({ parentHeaders }: HeadersArgs) {
  parentHeaders.set(
    "Cache-Control",
    "max-age=3600, s-maxage=86400",
  );
  return parentHeaders;
}
```

你可以通过只在"叶子路由"（索引路由和没有子路由的子路由）中定义响应头，而不在父路由中定义，来避免合并响应头的需求。

## 从 `entry.server.tsx`

`handleRequest` 导出会接收来自路由模块的响应头作为参数。你可以在这里追加全局响应头。

```tsx
export default async function handleRequest(
  request,
  responseStatusCode,
  responseHeaders,
  routerContext,
  loadContext,
) {
  // 设置、追加全局响应头
  responseHeaders.set(
    "X-App-Version",
    routerContext.manifest.version,
  );

  return new Response(await getStream(), {
    headers: responseHeaders,
    status: responseStatusCode,
  });
}
```

如果你没有 `entry.server.tsx`，运行 `reveal` 命令：

```shellscript nonumber
react-router reveal
```
