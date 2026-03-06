---
title: defer
new: true
---

# `defer`

此工具函数允许你通过传递 promise 而非已解析的值，从 loader 中延迟返回数据。

```jsx
async function loader() {
  let product = await getProduct();
  let reviews = getProductReviews();
  return defer({ product, reviews });
}
```

更多信息请参见[延迟数据指南][deferred guide]。

[deferred guide]: ../guides/deferred
