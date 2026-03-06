---
title: Await
new: true
---

## `<Await>`

用于渲染[延迟][defer]值并自动处理错误。请务必查阅[延迟数据指南][deferred guide]，因为有几个 API 需要与此组件配合使用。

```jsx lines=[1,10-18]
import { Await, useLoaderData } from "react-router-dom";

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

**注意：** `<Await>` 需要在 `<React.Suspense>` 或 `<React.SuspenseList>` 父组件内部渲染，以启用后备 UI。

## 类型声明

```tsx
declare function Await(
  props: AwaitProps,
): React.ReactElement;

interface AwaitProps {
  children: React.ReactNode | AwaitResolveRenderFunction;
  errorElement?: React.ReactNode;
  resolve: TrackedPromise | any;
}

interface AwaitResolveRenderFunction {
  (data: Awaited<any>): React.ReactElement;
}
```

## `children`

可以是 React 元素或函数。

使用函数时，解析后的值作为唯一参数传入。

```tsx [2]
<Await resolve={reviewsPromise}>
  {(resolvedReviews) => <Reviews items={resolvedReviews} />}
</Await>
```

使用 React 元素时，[`useAsyncValue`][useasyncvalue] 将提供数据：

```tsx [2]
<Await resolve={reviewsPromise}>
  <Reviews />
</Await>;

function Reviews() {
  const resolvedReviews = useAsyncValue();
  return <div>{/* ... */}</div>;
}
```

## `errorElement`

当 Promise 被拒绝时，错误元素会替代 children 渲染。你可以使用 [`useAsyncError`][useasyncerror] 访问错误信息。

如果 Promise 被拒绝，你可以提供一个可选的 `errorElement`，通过 `useAsyncError` hook 在上下文化的 UI 中处理该错误。

```tsx [3,9]
<Await
  resolve={reviewsPromise}
  errorElement={<ReviewsError />}
>
  <Reviews />
</Await>;

function ReviewsError() {
  const error = useAsyncError();
  return <div>{error.message}</div>;
}
```

如果你不提供 errorElement，被拒绝的值将冒泡到最近的路由级别 [`errorElement`][routeerrorelement]，可以通过 [`useRouteError`][userouteerror] hook 访问。

## `resolve`

接收从[延迟][defer] [loader][loader] 值返回的 Promise，用于解析和渲染。

```jsx [12,15,24,32-33]
import {
  defer,
  Route,
  useLoaderData,
  Await,
} from "react-router-dom";

// 给定以下路由
<Route
  loader={async () => {
    let book = await getBook();
    let reviews = getReviews(); // 未 await
    return defer({
      book,
      reviews, // 这是一个 Promise
    });
  }}
  element={<Book />}
/>;

function Book() {
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
          // 将 Promise 传递给 Await
          resolve={reviews}
        >
          <Reviews />
        </Await>
      </React.Suspense>
    </div>
  );
}
```

[useloaderdata]: ../hooks/use-loader-data
[userouteerror]: ../hooks/use-route-error
[defer]: ../utils/defer
[deferred guide]: ../guides/deferred
[useasyncvalue]: ../hooks/use-async-value
[useasyncerror]: ../hooks/use-async-error
[routeerrorelement]: ../route/error-element
[loader]: ../route/loader
