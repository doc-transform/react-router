---
title: Link
---

# Link

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.Link.html)

一个渐进增强的 [`<a href>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/a) 封装组件，用于通过客户端路由进行导航。

```tsx
import { Link } from "react-router";

<Link to="/dashboard">Dashboard</Link>;

<Link
  to={{
    pathname: "/some/path",
    search: "?query=string",
    hash: "#hash",
  }}
/>;
```

## Props

### discover

[modes: framework]

定义链接的[懒路由发现](../../explanation/lazy-route-discovery)行为。

- **render** — 默认值，在链接渲染时发现路由
- **none** — 不提前发现，仅在链接被点击时发现

```tsx
<Link /> // 默认 ("render")
<Link discover="render" />
<Link discover="none" />
```

### prefetch

[modes: framework]

定义链接的数据和模块预取行为。

```tsx
<Link /> // 默认
<Link prefetch="none" />
<Link prefetch="intent" />
<Link prefetch="render" />
<Link prefetch="viewport" />
```

- **none** — 默认，不预取
- **intent** — 当用户悬停或聚焦链接时预取
- **render** — 当链接渲染时预取
- **viewport** — 当链接进入视口时预取，非常适合移动端

预取通过 HTML [`<link rel="prefetch">`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link) 标签实现，它们会插入到链接之后。

```tsx
<a href="..." />
<a href="..." />
<link rel="prefetch" /> // 可能会条件渲染
```

因此，如果你使用了 `nav :last-child` 选择器，需要改用 `nav :last-of-type`，以避免样式因条件渲染而从最后一个链接上脱落（以及其他类似的选择器）。

### preventScrollReset

[modes: framework, data]

点击链接时，阻止滚动位置重置到窗口顶部（当应用使用 [`ScrollRestoration`](../components/ScrollRestoration) 时）。这仅阻止新位置重置滚动到顶部，后退/前进按钮导航时滚动位置仍会恢复。

```tsx
<Link to="?tab=one" preventScrollReset />
```

### relative

[modes: framework, data, declarative]

定义链接的相对路径行为。

```tsx
<Link to=".." /> // 默认: "route"
<Link relative="route" />
<Link relative="path" />
```

假设路由层级中父路由模式为 `"blog"`，子路由模式为 `"blog/:slug/edit"`：

- **route** — 默认值，相对于路由模式解析链接。在上面的例子中，相对链接 `"..."` 会移除 `:slug/edit` 两个段，回到 `"/blog"`。
- **path** — 相对于路径，因此 `"..."` 只会向上移除一个 URL 段到 `"/blog/:slug"`。

注意：索引路由和布局路由没有路径，因此不参与相对路径计算。

### reloadDocument

[modes: framework, data, declarative]

点击链接时使用文档导航代替客户端路由：浏览器会像处理普通 [`<a href>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/a) 一样处理过渡。

```tsx
<Link to="/logout" reloadDocument />
```

### replace

[modes: framework, data, declarative]

替换 [`History`](https://developer.mozilla.org/en-US/docs/Web/API/History) 栈中的当前条目，而不是推入新条目。

```tsx
<Link replace />
```

```
# 假设历史栈如下
A -> B

# 普通链接点击会推入新条目
A -> B -> C

# 使用 `replace`，B 会被 C 替换
A -> C
```

### state

[modes: framework, data, declarative]

向下一个位置添加持久性的客户端路由状态。

```tsx
<Link to="/somewhere/else" state={{ some: "value" }} />
```

可以通过 `location` 访问位置状态。

```tsx
function SomeComp() {
  const location = useLocation();
  location.state; // { some: "value" }
}
```

此状态在服务端不可访问，因为它是基于 [`history.state`](https://developer.mozilla.org/en-US/docs/Web/API/History/state) 实现的。

### to

[modes: framework, data, declarative]

可以是字符串或部分 [`Path`](https://api.reactrouter.com/v7/interfaces/react-router.Path.html)：

```tsx
<Link to="/some/path" />

<Link
  to={{
    pathname: "/some/path",
    search: "?query=string",
    hash: "#hash",
  }}
/>
```

### viewTransition

[modes: framework, data]

为此导航启用[视图过渡](https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API)。

```jsx
<Link to={to} viewTransition>
  Click me
</Link>
```

要在过渡期间应用特定样式，请参阅 [`useViewTransitionState`](../hooks/useViewTransitionState)。

### unstable_defaultShouldRevalidate

[modes: framework, data, declarative]

指定此导航的默认重新验证行为。

```tsx
<Link
  to="/some/path"
  unstable_defaultShouldRevalidate={false}
/>
```

如果当前活跃的路由上没有 `shouldRevalidate` 函数，则直接使用此值。否则会将其传入 `shouldRevalidate`，由路由做最终的重新验证判断。这在更新搜索参数且不想触发重新验证时很有用。

默认情况下（未指定时），loader 会按照路由器的标准重新验证行为进行重新验证。

### unstable_mask

[modes: framework, data]

此导航的遮罩路径，当你想让路由导航到一个位置但在 URL 栏中显示另一个位置时使用。

这对于上下文导航非常有用，例如在图库上方以模态框打开图片，同时保持底层的图库处于活跃状态。如果用户分享了遮罩后的 URL，或在新标签页中打开链接，他们只会加载遮罩位置，而不会加载底层的上下文位置。

此功能依赖 `history.state`，因此仅适用于 SPA 场景，SSR 渲染不会遵循遮罩。

```tsx
// routes/gallery.tsx
export function clientLoader({
  request,
}: Route.LoaderArgs) {
  let sp = new URL(request.url).searchParams;
  return {
    images: getImages(),
    modalImage: sp.has("image")
      ? getImage(sp.get("image")!)
      : null,
  };
}

export default function Gallery({
  loaderData,
}: Route.ComponentProps) {
  return (
    <>
      <GalleryGrid>
        {loaderData.images.map((image) => (
          <Link
            key={image.id}
            to={`/gallery?image=${image.id}`}
            unstable_mask={`/images/${image.id}`}
          >
            <img src={image.url} alt={image.alt} />
          </Link>
        ))}
      </GalleryGrid>

      {data.modalImage ? (
        <dialog open>
          <img
            src={data.modalImage.url}
            alt={data.modalImage.alt}
          />
        </dialog>
      ) : null}
    </>
  );
}
```
