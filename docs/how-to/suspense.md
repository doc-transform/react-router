---
title: 使用 Suspense 进行流式传输
---

# 使用 Suspense 进行流式传输

[MODES: framework, data]

<br/>
<br/>

使用 React Suspense 进行流式传输可以让应用通过延迟非关键数据、解除对 UI 渲染的阻塞来加速初始渲染。

React Router 通过从 loader 和 action 返回 promise 来支持 React Suspense。

## 1. 从 loader 返回 promise

React Router 会在渲染路由组件之前等待路由 loader 完成。要为非关键数据解除阻塞，在 loader 中返回 promise 而不是 await 它。

```tsx
import type { Route } from "./+types/my-route";

export async function loader({}: Route.LoaderArgs) {
  // 注意这里没有 await
  let nonCriticalData = new Promise((res) =>
    setTimeout(() => res("non-critical"), 5000),
  );

  let criticalData = await new Promise((res) =>
    setTimeout(() => res("critical"), 300),
  );

  return { nonCriticalData, criticalData };
}
```

注意你不能返回单个 promise，必须是带有键的对象。

## 2. 渲染回退和解析后的 UI

promise 将在 `loaderData` 上可用，`<Await>` 会等待 promise 并触发 `<Suspense>` 渲染回退 UI。

```tsx
import * as React from "react";
import { Await } from "react-router";

// [之前的代码]

export default function MyComponent({
  loaderData,
}: Route.ComponentProps) {
  let { criticalData, nonCriticalData } = loaderData;

  return (
    <div>
      <h1>流式传输示例</h1>
      <h2>关键数据值：{criticalData}</h2>

      <React.Suspense fallback={<div>加载中...</div>}>
        <Await resolve={nonCriticalData}>
          {(value) => <h3>非关键值：{value}</h3>}
        </Await>
      </React.Suspense>
    </div>
  );
}
```

## 使用 React 19

如果你正在使用 React 19，可以用 `React.use` 代替 `Await`，但你需要创建一个新组件并将 promise 传递下去以触发 suspense 回退。

```tsx
<React.Suspense fallback={<div>加载中...</div>}>
  <NonCriticalUI p={nonCriticalData} />
</React.Suspense>
```

```tsx
function NonCriticalUI({ p }: { p: Promise<string> }) {
  let value = React.use(p);
  return <h3>非关键值 {value}</h3>;
}
```

## 超时

默认情况下，loader 和 action 会在 4950 毫秒后拒绝所有未完成的 promise。你可以通过在 `entry.server.tsx` 中导出一个 `streamTimeout` 数值来控制这个时间。

```ts filename=entry.server.tsx
// 在 10 秒后拒绝处理函数中所有待处理的 promise
export const streamTimeout = 10_000;
```
