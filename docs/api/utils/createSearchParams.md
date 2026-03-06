---
title: createSearchParams
---

# createSearchParams

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.createSearchParams.html)

使用给定的初始化器创建一个 URLSearchParams 对象。

这与 `new URLSearchParams(init)` 完全相同，不同之处在于它还支持在初始化器的对象形式中使用数组作为值，而不仅仅是字符串。当你需要给定键有多个值但不想使用数组初始化器时，这非常方便。

例如，替代以下写法：

```tsx
let searchParams = new URLSearchParams([
  ["sort", "name"],
  ["sort", "price"],
]);
```

你可以这样做：

```
let searchParams = createSearchParams({
  sort: ['name', 'price']
});
```

## 函数签名

```tsx
createSearchParams(init): URLSearchParams
```

## 参数

### init

[modes: framework, data, declarative]

_无文档_
