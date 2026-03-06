---
title: 客户端数据
---

# 客户端数据

[MODES: framework]

<br/>
<br/>

你可以使用 `clientLoader` 和 `clientAction` 函数直接在浏览器中获取和修改数据。

这些函数是使用 [SPA 模式][spa]时处理数据的主要机制。本指南演示了在服务端渲染 (SSR) 中利用客户端数据的常见用例。

## 跳过服务端中转

当使用 React Router 的 Backend-For-Frontend (BFF) 架构时，你可能希望绕过 React Router 服务器，直接与后端 API 通信。这种方式需要正确处理认证，并假设没有 CORS 限制。以下是实现方式：

1. 在文档加载时从服务端 `loader` 加载数据
2. 在后续所有加载中从 `clientLoader` 加载数据

在这种情况下，React Router 在注水时 _不会_ 调用 `clientLoader`——只在后续导航时调用。

```tsx lines=[4,11]
export async function loader({
  request,
}: Route.LoaderArgs) {
  const data = await fetchApiFromServer({ request }); // (1)
  return data;
}

export async function clientLoader({
  request,
}: Route.ClientLoaderArgs) {
  const data = await fetchApiFromClient({ request }); // (2)
  return data;
}
```

## 全栈状态

有时你需要在渲染组件之前将服务端和浏览器端（如 IndexedDB 或浏览器 SDK）的数据组合在一起。以下是实现此模式的方式：

1. 在文档加载时从服务端 `loader` 加载部分数据
2. 导出 [`HydrateFallback`][hydratefallback] 组件在 SSR 期间渲染，因为此时还没有完整的数据集
3. 设置 `clientLoader.hydrate = true`，这指示 React Router 在初始文档注水时调用 clientLoader
4. 在 `clientLoader` 中将服务端数据与客户端数据合并

```tsx lines=[4-6,19-20,23,26]
export async function loader({
  request,
}: Route.LoaderArgs) {
  const partialData = await getPartialDataFromDb({
    request,
  }); // (1)
  return partialData;
}

export async function clientLoader({
  request,
  serverLoader,
}: Route.ClientLoaderArgs) {
  const [serverData, clientData] = await Promise.all([
    serverLoader(),
    getClientData(request),
  ]);
  return {
    ...serverData, // (4)
    ...clientData, // (4)
  };
}
clientLoader.hydrate = true as const; // (3)

export function HydrateFallback() {
  return <p>Skeleton rendered during SSR</p>; // (2)
}

export default function Component({
  // 这将始终是服务端 + 客户端的组合数据集
  loaderData,
}: Route.ComponentProps) {
  return <>...</>;
}
```

## 选择服务端或客户端数据加载

你可以在应用中混合使用数据加载策略，为每个路由选择仅服务端或仅客户端的数据加载。以下是两种方式的实现：

1. 当你想使用服务端数据时导出 `loader`
2. 当你想使用客户端数据时导出 `clientLoader` 和 `HydrateFallback`

仅依赖服务端 loader 的路由如下所示：

```tsx filename=app/routes/server-data-route.tsx
export async function loader({
  request,
}: Route.LoaderArgs) {
  const data = await getServerData(request);
  return data;
}

export default function Component({
  loaderData, // (1) - 服务端数据
}: Route.ComponentProps) {
  return <>...</>;
}
```

仅依赖客户端 loader 的路由如下所示：

```tsx filename=app/routes/client-data-route.tsx
export async function clientLoader({
  request,
}: Route.ClientLoaderArgs) {
  const clientData = await getClientData(request);
  return clientData;
}
// 注意：不需要显式设置 - 如果没有 `loader`，这是隐含的
clientLoader.hydrate = true;

// (2)
export function HydrateFallback() {
  return <p>Skeleton rendered during SSR</p>;
}

export default function Component({
  loaderData, // (2) - 客户端数据
}: Route.ComponentProps) {
  return <>...</>;
}
```

## 客户端缓存

你可以实现客户端缓存（使用内存、localStorage 等）来优化服务器请求。以下是一个演示缓存管理的模式：

1. 在文档加载时从服务端 `loader` 加载数据
2. 设置 `clientLoader.hydrate = true` 来预填缓存
3. 通过 `clientLoader` 从缓存加载后续导航的数据
4. 在 `clientAction` 中使缓存失效

注意，由于我们没有导出 `HydrateFallback` 组件，我们将 SSR 渲染路由组件然后在注水时运行 `clientLoader`，所以你的 `loader` 和 `clientLoader` 在初始加载时返回相同的数据很重要，以避免注水错误。

```tsx lines=[4,26,32,39,46]
export async function loader({
  request,
}: Route.LoaderArgs) {
  const data = await getDataFromDb({ request }); // (1)
  return data;
}

export async function action({
  request,
}: Route.ActionArgs) {
  await saveDataToDb({ request });
  return { ok: true };
}

let isInitialRequest = true;

export async function clientLoader({
  request,
  serverLoader,
}: Route.ClientLoaderArgs) {
  const cacheKey = generateKey(request);

  if (isInitialRequest) {
    isInitialRequest = false;
    const serverData = await serverLoader();
    cache.set(cacheKey, serverData); // (2)
    return serverData;
  }

  const cachedData = await cache.get(cacheKey);
  if (cachedData) {
    return cachedData; // (3)
  }

  const serverData = await serverLoader();
  cache.set(cacheKey, serverData);
  return serverData;
}
clientLoader.hydrate = true; // (2)

export async function clientAction({
  request,
  serverAction,
}: Route.ClientActionArgs) {
  const cacheKey = generateKey(request);
  cache.delete(cacheKey); // (4)
  const serverData = await serverAction();
  return serverData;
}
```

[spa]: ../how-to/spa
[hydratefallback]: ../start/framework/route-module#hydratefallback
