---
title: 渐进增强
---

# 渐进增强

[MODES: framework]

<br/>
<br/>

> 渐进增强是一种 Web 设计策略，优先关注 Web 内容本身，让每个人都能访问网页的基本内容和功能，同时拥有更多浏览器特性或更快网速的用户则能获得增强版本。
>
> <cite>- [维基百科][wikipedia]</cite>

当使用 React Router 配合服务端渲染（框架模式中的默认方式）时，你可以自动利用渐进增强的好处。

## 渐进增强为何重要

"渐进增强"一词由 Steven Champeon 和 Nick Finck 在 2003 年提出，当时不同浏览器对 CSS 和 JavaScript 的支持参差不齐，许多用户实际上在禁用 JavaScript 的状态下浏览网页。

今天，我们很幸运地在一个更加一致的 Web 上开发，大多数用户都启用了 JavaScript。

然而，我们仍然坚信 React Router 中渐进增强的核心原则。它能带来快速且健壮的应用，以及简单的开发工作流。

**性能**：虽然很容易认为只有 5% 的用户有慢速连接，但现实是 100% 的用户在 5% 的时间里都有慢速连接。

**健壮性**：在 JavaScript 加载完成之前，每个人都处于 JavaScript 禁用状态。

**简洁性**：使用 React Router 以渐进增强方式构建应用，实际上比构建传统 SPA 更简单。

## 性能

服务端渲染允许你的应用比典型的[单页应用 (SPA)][spa] 并行处理更多事情，使初始加载体验和后续导航更快。

典型的 SPA 发送一个空白文档，只有在 JavaScript 加载完成后才开始工作：

```
HTML        |---|
JavaScript      |---------|
Data                      |---------------|
                            页面渲染完成 👆
```

React Router 应用可以在请求到达服务器的那一刻就开始工作，并流式传输响应，这样浏览器可以并行开始下载 JavaScript、其他资源和数据：

```
               👇 首字节
HTML        |---|-----------|
JavaScript      |---------|
Data        |---------------|
              页面渲染完成 👆
```

## 健壮性和可访问性

虽然你的用户可能不会在禁用 JavaScript 的状态下浏览网页，但在 JavaScript 加载完成之前，每个人都在不使用 JavaScript 的情况下使用网站。React Router 通过在 HTML 之上构建来拥抱渐进增强，允许你以无需 JavaScript 即可工作的方式构建应用，然后在其上叠加 JavaScript 来增强体验。

最简单的例子是 `<Link to="/account">`。这些渲染为 `<a href="/account">` 标签，无需 JavaScript 即可工作。当 JavaScript 加载后，React Router 会拦截点击并使用客户端路由处理导航。这让你对 UX 有更多控制，而不是只看浏览器标签页中的旋转图标——但两种方式都能工作。

现在考虑一个简单的添加到购物车按钮：

```tsx
export function AddToCart({ id }) {
  return (
    <Form method="post" action="/add-to-cart">
      <input type="hidden" name="id" value={id} />
      <button type="submit">添加到购物车</button>
    </Form>
  );
}
```

无论 JavaScript 是否已加载都没关系，这个按钮都会将产品添加到购物车。

当 JavaScript 加载后，React Router 会拦截表单提交并在客户端处理。这允许你添加自己的加载中 UI 或其他客户端行为。

## 简洁性

当你开始依赖 Web 的基本特性如 HTML 和 URL 时，你会发现你对客户端状态和状态管理的需求大大减少。

考虑之前的按钮，对代码没有根本性改变，我们可以添加一些客户端行为：

```tsx lines=[1,4,7,10-12,14]
import { useFetcher } from "react-router";

export function AddToCart({ id }) {
  const fetcher = useFetcher();

  return (
    <fetcher.Form method="post" action="/add-to-cart">
      <input name="id" value={id} />
      <button type="submit">
        {fetcher.state === "submitting"
          ? "添加中..."
          : "添加到购物车"}
      </button>
    </fetcher.Form>
  );
}
```

当 JavaScript 正在加载时，此功能与之前的工作方式完全相同，但一旦 JavaScript 加载完成：

- `useFetcher` 不再像 `<Form>` 那样导致页面导航，因此用户可以停留在同一页面继续购物
- 应用代码决定加载中的 UI，而不是浏览器标签页中的旋转图标

这不是以两种不同方式构建——一种用于 JavaScript，一种不用——而是迭代式构建。从功能的最简版本开始并发布；然后迭代到增强的用户体验。

用户不仅获得了渐进增强的体验，应用开发者也可以在不改变功能基本设计的情况下"渐进增强"UI。

另一个渐进增强带来简洁性的例子是 URL。当你从 URL 开始时，你不需要担心客户端状态管理。你可以直接使用 URL 作为 UI 的真实来源。

```tsx
export function SearchBox() {
  return (
    <Form method="get" action="/search">
      <input type="search" name="query" />
      <SearchIcon />
    </Form>
  );
}
```

这个组件不需要任何状态管理。它只是渲染一个提交到 `/search` 的表单。当 JavaScript 加载后，React Router 会拦截表单提交并在客户端处理。以下是下一个迭代：

```tsx lines=[1,4-6,11]
import { useNavigation } from "react-router";

export function SearchBox() {
  const navigation = useNavigation();
  const isSearching =
    navigation.location.pathname === "/search";

  return (
    <Form method="get" action="/search">
      <input type="search" name="query" />
      {isSearching ? <Spinner /> : <SearchIcon />}
    </Form>
  );
}
```

架构没有根本性改变，只是对用户和代码的渐进增强。

另请参阅：[状态管理][state_management]

[wikipedia]: https://en.wikipedia.org/wiki/Progressive_enhancement
[spa]: ../how-to/spa
[state_management]: ./state-management
