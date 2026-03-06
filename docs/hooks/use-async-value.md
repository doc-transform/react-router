---
title: useAsyncValue
new: true
---

# `useAsyncValue`

返回最近的 `<Await>` 祖先组件中解析的数据。

```tsx
function ProductVariants() {
  const variants = useAsyncValue();
  return <div>{/* ... */}</div>;
}

// Await 为值创建上下文
<Await resolve={somePromiseForProductVariants}>
  <ProductVariants />
</Await>;
```

更多信息请参阅[延迟数据指南][deferred]和 [`<Await>` 文档][await docs]。

[await docs]: ../components/await
[deferred]: ../guides/deferred
