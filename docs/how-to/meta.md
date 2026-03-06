---
title: Meta 标签与 SEO
hidden: true
---

[从路由模块文档复制]

默认情况下，meta 描述符在大多数情况下会渲染一个 [`<meta>` 标签][meta-element]。有两个例外：

- `{ title }` 渲染一个 `<title>` 标签
- `{ "script:ld+json" }` 渲染一个 `<script type="application/ld+json">` 标签，其值应该是一个可序列化的对象，会被字符串化并注入到标签中。

```tsx
export function meta() {
  return [
    {
      "script:ld+json": {
        "@context": "https://schema.org",
        "@type": "Organization",
        name: "React Router",
        url: "https://reactrouter.com",
      },
    },
  ];
}
```

meta 描述符还可以通过将 `tagName` 属性设置为 `"link"` 来渲染 [`<link>` 标签][link-element]。这对于与 SEO 相关的 `<link>` 标签（如 `canonical` URL）很有用。对于样式表和 favicon 等资源链接，你应该使用 [`links` 导出][links]。

```tsx
export function meta() {
  return [
    {
      tagName: "link",
      rel: "canonical",
      href: "https://reactrouter.com",
    },
  ];
}
```
