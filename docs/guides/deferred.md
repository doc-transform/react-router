---
title: 延迟数据
description: 何时、为何以及如何使用 React 18 和 React Router 的 defer API 延迟加载非关键数据。
new: true
---

# 延迟数据指南

## 问题

设想一个场景，你的某个路由的 loader 需要获取一些由于某种原因而相当慢的数据。例如，假设你要向用户显示一个正在派送到他们家的包裹的位置：

```jsx
import { json, useLoaderData } from "react-router-dom";
import { getPackageLocation } from "./api/packages";

async function loader({ params }) {
  const packageLocation = await getPackageLocation(
    params.packageId,
  );

  return json({ packageLocation });
}

function PackageRoute() {
  const data = useLoaderData();
  const { packageLocation } = data;

  return (
    <main>
      <h1>Let's locate your package</h1>
      <p>
        Your package is at {packageLocation.latitude} lat
        and {packageLocation.longitude} long.
      </p>
    </main>
  );
}
```

我们假设 `getPackageLocation` 很慢。这将导致初始页面加载和导航到该路由的时间和最慢的数据一样长。你可以做一些事情来优化并改善用户体验：

- 加速慢的东西（😅）。
- 使用 `Promise.all` 并行加载数据（在我们的例子中没有可并行的内容，但在其他情况下可能会有帮助）。
- 添加全局过渡 spinner（对 UX 有一定帮助）。
- 添加局部骨架屏 UI（对 UX 有一定帮助）。

如果这些方法都不太好用，你可能会被迫将慢数据从 `loader` 移到组件内获取（并在加载时显示骨架屏 UI）。在这种情况下，你会在组件挂载时渲染骨架屏 UI 并触发数据获取。得益于 [`useFetcher`][usefetcher]，从 DX 的角度来看这实际上还不错。而从 UX 的角度来看，这改善了客户端过渡和初始页面加载的体验。所以它确实解决了问题。

但在大多数情况下它仍然不是最优的（特别是如果你对路由组件进行了代码分割），原因有两个：

1. 客户端获取使你的数据请求处于瀑布流中：文档 -> JavaScript -> 延迟加载的路由 -> 数据获取
2. 你的代码不能轻松地在组件获取和路由获取之间切换（稍后详述）。

## 解决方案

React Router 利用 React 18 的 Suspense 进行数据获取，使用 [`defer` Response][defer response] 工具和 [`<Await />`][await] 组件 / [`useAsyncValue`][useasyncvalue] hook。通过使用这些 API，你可以解决这两个问题：

1. 你的数据不再处于瀑布流中：文档 -> JavaScript -> 延迟加载的路由 & 数据（并行）
2. 你的代码可以轻松地在渲染 fallback 和等待数据之间切换

让我们深入了解如何实现。

### 使用 `defer`

对于你希望渲染 fallback UI 的慢数据请求，开始添加 `<Await />`。让我们对上面的示例进行这样的操作：

```jsx lines=[3,9,13-15,24-40]
import {
  Await,
  defer,
  useLoaderData,
} from "react-router-dom";
import { getPackageLocation } from "./api/packages";

async function loader({ params }) {
  const packageLocationPromise = getPackageLocation(
    params.packageId,
  );

  return defer({
    packageLocation: packageLocationPromise,
  });
}

export default function PackageRoute() {
  const data = useLoaderData();

  return (
    <main>
      <h1>Let's locate your package</h1>
      <React.Suspense
        fallback={<p>Loading package location...</p>}
      >
        <Await
          resolve={data.packageLocation}
          errorElement={
            <p>Error loading package location!</p>
          }
        >
          {(packageLocation) => (
            <p>
              Your package is at {packageLocation.latitude}{" "}
              lat and {packageLocation.longitude} long.
            </p>
          )}
        </Await>
      </React.Suspense>
    </main>
  );
}
```

<details>
  <summary>或者，你可以使用 `useAsyncValue` hook：</summary>

如果你不喜欢回到 render props，你可以使用 hook，但你需要把内容拆分到另一个组件中：

```jsx lines=[11, 16, 23-31]
export default function PackageRoute() {
  const data = useLoaderData();

  return (
    <main>
      <h1>Let's locate your package</h1>
      <React.Suspense
        fallback={<p>Loading package location...</p>}
      >
        <Await
          resolve={data.packageLocation}
          errorElement={
            <p>Error loading package location!</p>
          }
        >
          <PackageLocation />
        </Await>
      </React.Suspense>
    </main>
  );
}

function PackageLocation() {
  const packageLocation = useAsyncValue();
  return (
    <p>
      Your package is at {packageLocation.latitude} lat and{" "}
      {packageLocation.longitude} long.
    </p>
  );
}
```

</details>

## 评估解决方案

因此，我们不是等待组件加载后才能触发数据请求，而是在用户开始过渡到新路由时就启动慢数据的请求。这可以显著加速较慢网络下的用户体验。

此外，React Router 为此提供的 API 非常符合人体工学。你可以字面意义上通过是否包含 `await` 关键字来切换某个东西是否被延迟：

```tsx
return defer({
  // not deferred:
  packageLocation: await packageLocationPromise,
  // deferred:
  packageLocation: packageLocationPromise,
});
```

因此，你可以进行 A/B 测试延迟，甚至根据用户或请求的数据来决定是否延迟：

```tsx
async function loader({ request, params }) {
  const packageLocationPromise = getPackageLocation(
    params.packageId,
  );
  const shouldDefer = shouldDeferPackageLocation(
    request,
    params.packageId,
  );

  return defer({
    packageLocation: shouldDefer
      ? packageLocationPromise
      : await packageLocationPromise,
  });
}
```

那个 `shouldDeferPackageLocation` 可以被实现为检查发起请求的用户、包裹位置数据是否在缓存中、A/B 测试的状态或其他任何内容。这真的很棒 🍭

## 常见问题

### 为什么不默认延迟所有内容？

React Router 的 defer API 是 React Router 提供的另一个杠杆，让你可以在权衡之间做出选择。你希望页面渲染得更快？延迟一些内容。你希望更低的 CLS（内容布局偏移）？不要延迟。你希望更快的渲染，也希望更低的 CLS？只延迟慢的和不重要的内容。

一切都是权衡，而 API 设计的巧妙之处在于，它很适合你进行简单的实验，看看哪种权衡能为你的实际关键指标带来更好的结果。

### `<Suspense/>` 的 fallback 何时渲染？

`<Await />` 组件只会在带有未完成 promise 的 `<Await />` 初次渲染时将 promise 抛出到 `<Suspense>` 边界。当 props 变化时不会重新渲染 fallback。这意味着，当用户提交表单且 loader 数据被重新验证时，你*不会*得到 fallback 渲染。当用户使用不同参数导航到相同路由时（在我们上面的示例中，如果用户从左侧的列表中选择包裹以在右侧查找其位置），你*会*得到 fallback 渲染。

这乍一看可能觉得反直觉，但请跟我们一起理解，我们真的仔细考虑过了，这样工作很重要。设想一个没有 deferred API 的世界。对于这些场景，你可能会想为表单提交/重新验证实现乐观 UI。

当你决定尝试 `defer` 的权衡时，我们不希望你不得不更改或删除这些优化，因为我们希望你能够轻松地在延迟某些数据和不延迟之间切换。因此，我们确保你现有的乐观状态以相同的方式工作。如果我们不这样做，你可能会经历我们所说的“爆米花 UI”，即数据提交触发 fallback 加载状态而不是你努力实现的乐观 UI。

所以请记住：**延迟 100% 仅关乎路由及其参数的初始加载。**

### 为什么 loader 返回的 Response 对象不再工作了？

当你使用 `defer` 时，你是在告诉 React Router 立即加载页面，不等待延迟数据。页面在 `Response` 对象返回之前就已经加载了，因此 response 不会像 `return fetch(url)` 那样自动处理。

因此，你需要自己处理 `Response` 并使用数据而不是 `Response` 实例来解析你的延迟 Promise。

```jsx
async function loader({ request, params }) {
  return defer({
    // Broken! Resolves with a Response
    // broken: fetch(url),

    // Fixed! Resolves with the response data
    data: fetch(url).then((res) => res.json()),
  });
}
```

或者考虑我们的延迟数据可能返回重定向 `Response` 的场景。你可以检测重定向并将状态码和位置作为数据返回，然后在组件中通过 `useEffect` 和 `useNavigate` 执行客户端重定向。

```jsx
async function loader({ request, params }) {
  let data = fetch(url).then((res) => {
    if (res.status == 301) {
      return {
        isRedirect: true,
        status: res.status,
        location: res.headers.get("Location"),
      };
    }
    return res.json();
  });

  return defer({ data });
}
```

[link]: ../components/link
[usefetcher]: ../hooks/use-fetcher
[defer response]: ../utils/defer
[await]: ../components/await
[useasyncvalue]: ../hooks/use-async-value
