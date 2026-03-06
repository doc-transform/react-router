---
title: Await
---

# Await

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.Await.html)

用于渲染 Promise 值并自动处理错误。

**注意：** `<Await>` 需要在 [`<React.Suspense>`](https://react.dev/reference/react/Suspense) 内部渲染。

```tsx
import { Await, useLoaderData } from "react-router";

export async function loader() {
  // 未 await
  const reviews = getReviews();
  // 已 await（阻塞过渡）
  const book = await fetch("/api/book").then((res) =>
    res.json(),
  );
  return { book, reviews };
}

function Book() {
  const { book, reviews } = useLoaderData();
  return (
    <div>
      <h1>{book.title}</h1>
      <p>{book.description}</p>
      <React.Suspense fallback={<ReviewsSkeleton />}>
        <Await
          resolve={reviews}
          errorElement={
            <div>Could not load reviews 😬</div>
          }
          children={(resolvedReviews) => (
            <Reviews items={resolvedReviews} />
          )}
        />
      </React.Suspense>
    </div>
  );
}
```

## 函数签名

```tsx
function Await<Resolve>({
  children,
  errorElement,
  resolve,
}: AwaitProps<Resolve>);
```

## Props

### children

使用函数时，已解析的值会作为参数传入。

```tsx [2]
<Await resolve={reviewsPromise}>
  {(resolvedReviews) => <Reviews items={resolvedReviews} />}
</Await>
```

使用 React 元素时，[`useAsyncValue`](../hooks/useAsyncValue) 会提供已解析的值：

```tsx [2]
<Await resolve={reviewsPromise}>
  <Reviews />
</Await>;

function Reviews() {
  const resolvedReviews = useAsyncValue();
  return <div>...</div>;
}
```

### errorElement

当 [`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 被拒绝时，渲染此错误元素替代 `children`。

```tsx
<Await
  errorElement={<div>Oops</div>}
  resolve={reviewsPromise}
>
  <Reviews />
</Await>
```

要提供更具上下文的错误信息，可以在子组件中使用 [`useAsyncError`](../hooks/useAsyncError)：

```tsx
<Await
  errorElement={<ReviewsError />}
  resolve={reviewsPromise}
>
  <Reviews />
</Await>;

function ReviewsError() {
  const error = useAsyncError();
  return <div>Error loading reviews: {error.message}</div>;
}
```

如果未提供 `errorElement`，被拒绝的值将冒泡到最近的路由级 [`ErrorBoundary`](../../start/framework/route-module#errorboundary)，可通过 [`useRouteError`](../hooks/useRouteError) Hook 访问。

### resolve

接收从 [`loader`](../../start/framework/route-module#loader) 返回的 [`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)，用于解析和渲染。

```tsx
import { Await, useLoaderData } from "react-router";

export async function loader() {
  let reviews = getReviews(); // 未 await
  let book = await getBook();
  return {
    book,
    reviews, // 这是一个 Promise
  };
}

export default function Book() {
  const {
    book,
    reviews, // 这是同一个 Promise
  } = useLoaderData();

  return (
    <div>
      <h1>{book.title}</h1>
      <p>{book.description}</p>
      <React.Suspense fallback={<ReviewsSkeleton />}>
        <Await
          // 传递给 Await 的 Promise
          resolve={reviews}
        >
          <Reviews />
        </Await>
      </React.Suspense>
    </div>
  );
}
```
