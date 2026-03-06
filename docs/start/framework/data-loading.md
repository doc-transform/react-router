---
title: 数据加载
order: 5
---

# 数据加载

[MODES: framework]

## 简介

数据通过 `loader` 和 `clientLoader` 提供给路由组件。

Loader 数据会自动从 loader 中序列化，并在组件中反序列化。除了字符串和数字等原始值外，loader 还可以返回 Promise、Map、Set、Date 等。

`loaderData` 属性的类型是[自动生成的][type-safety]。

<docs-info>我们尽量支持与 React 允许服务端组件传递给客户端组件的 [可序列化类型][serializable-types] 相同的类型集合。这为你的应用未来迁移到 [RSC][rsc] 做好了准备。</docs-info>

## 客户端数据加载

`clientLoader` 用于在客户端获取数据。这对于你希望仅从浏览器获取数据的页面或完整项目非常有用。

```tsx filename=app/product.tsx
// route("products/:pid", "./product.tsx");
import type { Route } from "./+types/product";

export async function clientLoader({
  params,
}: Route.ClientLoaderArgs) {
  const res = await fetch(`/api/products/${params.pid}`);
  const product = await res.json();
  return product;
}

// 在客户端 loader 运行时渲染 HydrateFallback
export function HydrateFallback() {
  return <div>Loading...</div>;
}

export default function Product({
  loaderData,
}: Route.ComponentProps) {
  const { name, description } = loaderData;
  return (
    <div>
      <h1>{name}</h1>
      <p>{description}</p>
    </div>
  );
}
```

## 服务端数据加载

在服务端渲染时，`loader` 同时用于初始页面加载和客户端导航。客户端导航时，React Router 会自动从浏览器向服务器发起 `fetch` 请求来调用 loader。

```tsx filename=app/product.tsx
// route("products/:pid", "./product.tsx");
import type { Route } from "./+types/product";
import { fakeDb } from "../db";

export async function loader({ params }: Route.LoaderArgs) {
  const product = await fakeDb.getProduct(params.pid);
  return product;
}

export default function Product({
  loaderData,
}: Route.ComponentProps) {
  const { name, description } = loaderData;
  return (
    <div>
      <h1>{name}</h1>
      <p>{description}</p>
    </div>
  );
}
```

注意：`loader` 函数会从客户端包中移除，因此你可以使用仅限服务端的 API，无需担心它们被包含在浏览器中。

## 静态数据加载

在预渲染时，loader 用于在生产构建期间获取数据。

```tsx filename=app/product.tsx
// route("products/:pid", "./product.tsx");
import type { Route } from "./+types/product";

export async function loader({ params }: Route.LoaderArgs) {
  let product = await getProductFromCSVFile(params.pid);
  return product;
}

export default function Product({
  loaderData,
}: Route.ComponentProps) {
  const { name, description } = loaderData;
  return (
    <div>
      <h1>{name}</h1>
      <p>{description}</p>
    </div>
  );
}
```

预渲染的 URL 在 `react-router.config.ts` 中指定：

```ts filename=react-router.config.ts
import type { Config } from "@react-router/dev/config";

export default {
  async prerender() {
    let products = await readProductsFromCSVFile();
    return products.map(
      (product) => `/products/${product.id}`,
    );
  },
} satisfies Config;
```

注意：在服务端渲染时，未被预渲染的 URL 仍会照常进行服务端渲染，允许你在单个路由上预渲染部分数据，同时对其余部分进行服务端渲染。

## 同时使用两种 Loader

`loader` 和 `clientLoader` 可以一起使用。`loader` 用于服务端的初始 SSR（或预渲染），`clientLoader` 用于后续的客户端导航。

```tsx filename=app/product.tsx
// route("products/:pid", "./product.tsx");
import type { Route } from "./+types/product";
import { fakeDb } from "../db";

export async function loader({ params }: Route.LoaderArgs) {
  return fakeDb.getProduct(params.pid);
}

export async function clientLoader({
  serverLoader,
  params,
}: Route.ClientLoaderArgs) {
  const res = await fetch(`/api/products/${params.pid}`);
  const serverData = await serverLoader();
  return { ...serverData, ...res.json() };
}

export default function Product({
  loaderData,
}: Route.ComponentProps) {
  const { name, description } = loaderData;

  return (
    <div>
      <h1>{name}</h1>
      <p>{description}</p>
    </div>
  );
}
```

你还可以通过在函数上设置 `hydrate` 属性来强制客户端 loader 在注水期间和页面渲染之前运行。在这种情况下，你需要渲染一个 `HydrateFallback` 组件来显示备用 UI。

```tsx filename=app/product.tsx
export async function loader() {
  /* ... */
}

export async function clientLoader() {
  /* ... */
}

// 强制客户端 loader 在注水期间运行
clientLoader.hydrate = true as const; // `as const` 用于类型推断

export function HydrateFallback() {
  return <div>Loading...</div>;
}

export default function Product() {
  /* ... */
}
```

---

下一节：[操作（Action）][actions]

另请参阅：

- [使用 Suspense 进行流式传输][streaming]
- [客户端数据][client-data]
- [使用 Fetcher][fetchers]

[type-safety]: ../../explanation/type-safety
[serializable-types]: https://react.dev/reference/rsc/use-client#serializable-types
[rsc]: ../../how-to/react-server-components
[actions]: ./actions
[streaming]: ../../how-to/suspense
[client-data]: ../../how-to/client-data
[fetchers]: ../../how-to/fetchers#loading-data
