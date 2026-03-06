---
title: 使用 FormData
new: true
---

# 使用 FormData

<docs-info>TODO: 本文档是占位文档</docs-info>

一个常见的技巧是使用 [`Object.fromEntries`][object-fromentries] 将整个 formData 转换为对象：

```tsx
const data = Object.fromEntries(await request.formData());
data.songTitle;
data.lyrics;
```

[object-fromentries]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/fromEntries
