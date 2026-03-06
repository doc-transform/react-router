---
title: Markdown 元素
hidden: true
---

# Markdown 元素

本页面用于测试所有可能出现的 Markdown 类型。每当我发现一个样式边界情况时，就会将其添加到本文档中。这是我对所有需要在不同上下文中进行样式处理的元素类型的一种可视化回归测试。

## 标题

第 4、5、6 级标题的样式完全相同。如果我们开始需要使用这些级别的标题来编写正文，那应该重新审视一下我们的方式了。

# 一级标题

## 二级标题

### 三级标题

#### 四级标题

##### 五级标题

###### 六级标题

## 表格

| 语法  | 描述  |
| ----- | ----- |
| 第1行 | 第2列 |
| 第2行 | 第2列 |
| 第3行 | 第2列 |

## 提示框

提示框可以使用 `<docs-*>` 元素来实现。它们专门用于在文档正常内容流之外，引起读者对特定信息的注意。

支持以下三种类型：

1. `<docs-info>` - 用于一般性的信息提示。
2. `<docs-warning>` - 用于提醒读者需要注意的事项。
3. `<docs-error>` - 用于告知用户不应该做的事情。

示例：

<docs-info>带有 `..` 的 `<Link to>` 与普通 `<a href>` 在当前 URL 以 `/` 结尾时的行为不同。`<Link to>` 会忽略末尾斜杠，并为每个 `..` 移除一个 URL 路径段。但 `<a href>` 在当前 URL 以 `/` 结尾与不以 `/` 结尾时，对 `..` 的处理方式不同。</docs-info>

<docs-warning>`useMatches` 仅适用于数据路由（如 [`createBrowserRouter`][createbrowserrouter]），因为它们预先知道完整的路由树，可以提供所有当前匹配项。此外，`useMatches` 不会匹配到任何后代路由树中，因为路由器无法感知后代路由。</docs-warning>

<docs-error>不要这样做</docs-error>

<docs-info>这里的标记有点不太美观，因为（目前）这些内容都必须在 `<docs-*>` 元素内部且不能有换行。_但是_ 其中可以包含图片。<img src="https://picsum.photos/480/270" width="480" height="270" /></docs-info>

注意：这些元素的语义可能还不够准确。文档场景下可能有其他更合适的命名，例如：

- `<docs-info>` 可以改为 `<docs-tip>`
- `<docs-warning>` 可以改为 `<docs-important>`
- `<docs-error>` 可以改为 `<docs-warning>` 或 `<docs-danger>`

## 引用块

这是一个包含多行和多种样式的 `<blockquote>`：

> 这是我的引用内容。
>
> 其中可以包含[链接]($link)、**粗体文本**、_斜体文本_，甚至 `<code>` 代码，所有这些都需要被正确处理。哦，别忘了列表：
>
> - 列表项 1
> - 列表项 2
> - 列表项 3
>
> 无序列表或有序列表：
>
> 1. 列表项
> 2. 另一个列表项
> 3. 又一个列表项

## 列表

这是一个包含链接的列表，其中一些是代码：

- 这是第一个列表项
- [这是第二个列表项，它是一个链接][$link]
- 这是第三个列表项，包含 `<code>` 和 [`<LinkedCode>` 混合文本][$link]

还有，别忘了没有 `href` 的 `<a>` 标签也需要正确的样式：<a>比如这个链接</a>。

然后是 `<dl>` 定义列表：

<dl>
  <dt>React</dt>
  <dd>对某事作出反应或以特定方式回应</dd>
  <dt>Router</dt>
  <dd>一种将数据包转发到计算机网络适当部分的设备。</dd>
  <dt>Library</dt>
  <dd>一个包含书籍、期刊，有时还有电影和音乐收藏的建筑或房间，供人们阅读、借阅或参考。</dd>
  <dd>一组通常可公开获取的程序和软件包集合，通常加载并存储在磁盘上以便立即使用。</dd>
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

带有多行高亮的代码：

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

带有文件名的代码：

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

错误示例代码：

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

带有高亮行和文件名的错误示例代码：

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

溢出的代码行：

```html
<!-- 你的应用的其他 HTML 内容放在这里 -->
<!-- prettier-ignore -->
<script src="https://unpkg.com/react@>=16.8/umd/react.development.js" crossorigin></script>
```

---

[$link]: https://www.youtube.com/watch?v=dQw4w9WgXcQ
[createbrowserrouter]: ./routers/create-browser-router
