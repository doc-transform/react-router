---
title: Markdown 元素
hidden: true
---

# Markdown 元素

这是用于测试所有不同类型 Markdown 的页面。每当我发现一个样式边界情况时，就会将其添加到此文档中。这是我对所有需要在不同上下文中设置样式的不同类型元素进行视觉回归测试的方式。

## 标题

4、5、6 级标题都被同等对待。如果我们开始写需要这些标题的内容，我们应该重新审视我们的人生。

# 标题 1

## 标题 2

### 标题 3

#### 标题 4

##### 标题 5

###### 标题 6

## 表格

| 语法  | 描述  |
| ----- | ----- |
| 第1行 | 第2列 |
| 第2行 | 第2列 |
| 第3行 | 第2列 |

## 提示框

提示框可以使用 `<docs-*>` 元素。它们专门用于在文档正常流程之外特别引起对某些信息的注意。

这些元素支持三种变体：

1. `<docs-info>` - 用于一般性信息提示。
2. `<docs-warning>` - 用于警告读者应该知道的事情。
3. `<docs-error>` - 用于告诉用户不应该做某事。

示例：

<docs-info>`<Link to>` 使用 `..` 时的行为与普通 `<a href>` 不同（当前 URL 以 `/` 结尾时）。`<Link to>` 会忽略末尾斜杠，并为每个 `..` 移除一个 URL 段。但 `<a href>` 值在当前 URL 以 `/` 结尾和不以 `/` 结尾时处理 `..` 的方式不同。</docs-info>

<docs-warning>`useMatches` 仅适用于数据路由器（如 [`createBrowserRouter`][createbrowserrouter]），因为它们预先知道完整的路由树并可以提供所有当前匹配项。此外，`useMatches` 不会向下匹配到任何后代路由树，因为路由器不知道后代路由。</docs-warning>

<docs-error>不要这样做</docs-error>

<docs-info>这里的标记有点丑，因为（目前）这些内容都必须在 `<docs-*>` 元素内且没有任何换行，_但是_ 这些内容中可能包含图片。<img src="https://picsum.photos/480/270" width="480" height="270" /></docs-info>

注意：也许这些语义并不完全正确。在文档的场景下可能有其他更合适的名词，比如：

- `<docs-info>` 可以改为 `<docs-tip>`
- `<docs-warning>` 可以改为 `<docs-important>`
- `<docs-error>` 可以改为 `<docs-warning>` 或 `<docs-danger>`

## 引用块

这是一个包含多行和多种样式的 `<blockquote>`：

> 这是我的引用。
>
> 它可以包含 [链接]($link)、**粗体文本**、_斜体文本_，甚至 `<code>`，所有这些都应该被考虑到。哦，还有别忘了列表：
>
> - 列表项 1
> - 列表项 2
> - 列表项 3
>
> 无序列表，或有序列表：
>
> 1. 列表项
> 2. 另一个列表项
> 3. 又一个列表项

## 列表

这是一个链接列表，其中一些是代码：

- 这是我的第一个列表项
- [这是我的第二个列表项，是个链接][$link]
- 这是我的第三项，包含 `<code>` 和 [`<LinkedCode>` 混合文本][$link]

还有别忘了没有 `href` 的 `<a>` 标签的正确样式：<a>比如这里的这个链接</a>。

还有 `<dl>` 列表：

<dl>
  <dt>React</dt>
  <dd>对某事做出特定方式的响应或表现</dd>
  <dt>Router</dt>
  <dd>将数据包转发到计算机网络适当部分的设备。</dd>
  <dt>Library</dt>
  <dd>包含书籍、期刊，有时还有电影和录制音乐的建筑或房间，供人们阅读、借阅或参考。</dd>
  <dd>一组通常可以广泛使用的程序和软件包集合，通常加载并存储在磁盘上以供立即使用。</dd>
</dl>

## 代码

普通代码：

```tsx
<WhateverRouter initialEntries={["/events/123"]}>
  <Route path="/" element={<Root />} loader={rootLoader}>
    <Route
      path="events/:id"
      element={<Event />}
      loader={eventLoader}
    />
  </Route>
</WhateverRouter>
```

带多行高亮：

```tsx lines=[1-2,5]
<WhateverRouter initialEntries={["/events/123"]}>
  <Route path="/" element={<Root />} loader={rootLoader}>
    <Route
      path="events/:id"
      element={<Event />}
      loader={eventLoader}
    />
  </Route>
</WhateverRouter>
```

带文件名：

```tsx filename=src/main.jsx
<WhateverRouter initialEntries={["/events/123"]}>
  <Route path="/" element={<Root />} loader={rootLoader}>
    <Route
      path="events/:id"
      element={<Event />}
      loader={eventLoader}
    />
  </Route>
</WhateverRouter>
```

错误代码：

```tsx bad
<WhateverRouter initialEntries={["/events/123"]}>
  <Route path="/" element={<Root />} loader={rootLoader}>
    <Route
      path="events/:id"
      element={<Event />}
      loader={eventLoader}
    />
  </Route>
</WhateverRouter>
```

带高亮行和文件名的错误代码：

```tsx filename=src/main.jsx bad lines=[2-5]
<WhateverRouter initialEntries={["/events/123"]}>
  <Routes>
    <Route path="/" element={<Root />} loader={rootLoader}>
      <Route
        path="events/:id"
        element={<Event />}
        loader={eventLoader}
      />
    </Route>
  </Routes>
</WhateverRouter>
```

溢出的行：

```html
<!-- Other HTML for your app goes here -->
<!-- prettier-ignore -->
<script src="https://unpkg.com/react@>=16.8/umd/react.development.js" crossorigin></script>
```

---

[$link]: https://www.youtube.com/watch?v=dQw4w9WgXcQ
[createbrowserrouter]: ./routers/create-browser-router
