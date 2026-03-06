---
title: generatePath
---

# `generatePath`

<details>
  <summary>Type declaration</summary>

```tsx
declare function generatePath<Path extends string>(
  path: Path,
  params?: {
    [key in PathParams<Path>]: string;
  },
): string;
```

</details>

`generatePath` 将一组参数插入到包含 `:id` 和 `*` 占位符的路由路径字符串中。当你想要消除路由路径中的占位符以便静态匹配而不是使用动态参数时非常有用。

```tsx
generatePath("/users/:id", { id: "42" }); // "/users/42"
generatePath("/files/:type/*", {
  type: "img",
  "*": "cat.jpg",
}); // "/files/img/cat.jpg"
```
