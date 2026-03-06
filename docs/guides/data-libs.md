---
title: 数据库集成
description: "自 v6.4 发布以来，一些人开始疑惑 React Router 是否在试图替代像 React Query 这样的库。答案是“不！”"
---

# 数据库集成

自 v6.4 发布以来，一些人开始疑惑 React Router 是否在试图替代像 [React Query][react-query]、[useSwr][useswr] 等库。

答案是“不！”

React Router 的数据 API 关注的是*何时*加载、变更和重新验证数据，而不是*如何*做到。它关注的是数据生命周期，而不是数据获取、变更、存储和缓存的实际实现。

考虑到 `<a href>` 和 `<form action>` 都是导航事件，且两者都与数据耦合（显示什么数据或更改什么数据），客户端路由器帮助你处理两个元素的*导航状态*是很有意义的。但实际的数据实现取决于你。

本文的示例改编自 [TkDodo 的博客][tkdodo]，感谢这篇优秀的文章！

## 加载数据

不是在组件中加载数据，而是在 loader 内部使用你的数据抽象。注意，这个加载发生在 React 渲染生命周期之外，所以你不能使用像 React Query 的 `useQuery` 这样的 hook，你需要直接使用 query client 的方法。

```jsx lines=[4]
import { queryClient } from "./query-client";

export const loader = ({ params }) => {
  return queryClient.fetchQuery(queryKey, queryFn, {
    staleTime: 10000,
  });
};
```

如果 query client 能正确抛出错误，那么 React Router 的 [`errorElement`][errorelement] 将以相同的方式工作。

当然，你可以使用数据库的所有功能，比如缓存。缓存你的数据可以确保当用户点击后退按钮回到已经看过的页面时，数据会立即从缓存加载。有时缓存是正确的选择，有时你希望它始终是最新的，但这不在 React Router 数据 API 的范围内。

React Router 只保留*当前页面的 loaderData*。如果用户点击“后退”，所有 loader 都会再次被调用。没有像 React Query 这样的数据缓存库（或你的 JSON API 上的 HTTP 缓存头来使用浏览器自己的 HTTP 缓存），你的应用会再次获取所有数据。

从这个意义上说，React Router 关注的是*时机*，而 React Query 关注的是*缓存*。

## 在组件中访问数据

虽然 React Router 的 `useLoaderData` 返回你从 loader 返回的任何内容，但你可以使用数据抽象库的 hook 来获取该包的完整功能集。

```diff
export default function SomeRouteComponent() {
- const data = useLoaderData();
+ const { data } = useQuery(someQueryKey);
}
```

## 在数据变更中失效数据

因为大多数这些库都有某种缓存机制，你需要在某个时候失效这些缓存。

失效这些缓存的完美位置是在 React Router 的 [action][action] 中。

```jsx lines=[7]
import { queryClient } from "./query-client";

export const action = async ({ request, params }) => {
  const formData = await request.formData();
  const updates = Object.fromEntries(formData);
  await updateContact(params.contactId, updates);
  await queryClient.invalidateQueries(["contacts"]);
  return redirect(`/contacts/${params.contactId}`);
};
```

## 与 `defer` 一起使用

你同样可以利用 deferred API：

```jsx lines=[2,10,14,28]
function loader() {
  return defer({
    // no await!
    someData: queryClient.fetchQuery("someKey", fn),
  });
}

function Comp() {
  // *do* useLoaderData for promise
  const { someData } = useLoaderData();
  return (
    <div>
      <h1>Something</h1>
      <Await
        resolve={someData}
        errorElement={<div>Oops!</div>}
      >
        <SomeView />
      </Await>
    </div>
  );
}

function SomeView() {
  // instead of accessing with useAsyncValue
  // const someData = useAsyncValue();
  // `useQuery` as usual
  const { data } = useQuery("someKey");
  // ...
}
```

## 功能重叠

像 `useQuery` 这样的 hook 通常返回加载状态和错误状态，你可以用它们来分支 UI。使用 React Router，你可以将所有分支逻辑保留在幸福路径组件之外，而依赖 [`errorElement`][errorelement]、[`useNavigation`][usenavigation] 和 [`Await`][await] 来代替。

## 总结

通过所有这些 API 的协作，你现在可以使用 React Router 的 [`useNavigation`][usenavigation] 构建加载状态、乐观 UI 等更多功能。使用 React Router 处理数据加载、变更和导航状态的时机，然后使用 React Query 等库处理实际的加载、失效、存储和缓存。

[react-query]: https://tanstack.com/query/v4/
[useswr]: https://swr.vercel.app/
[errorelement]: ../route/error-element
[action]: ../route/action
[tkdodo]: https://tkdodo.eu/blog/react-query-meets-react-router
[usenavigation]: ../hooks/use-navigation
[await]: ../components/await
