---
title: 视图过渡
---

# 视图过渡

[MODES: framework, data]

<br/>
<br/>

使用 [View Transitions API][view-transitions-api] 在 React Router 应用中实现页面过渡的流畅动画。此功能允许你在客户端导航期间创建无缝的视觉过渡效果。

## 基本视图过渡

### 1. 在导航中启用视图过渡

启用视图过渡的最简单方法是在 `Link`、`NavLink` 或 `Form` 组件上添加 `viewTransition` prop。这会自动将导航更新包裹在 `document.startViewTransition()` 中。

```tsx
<Link to="/about" viewTransition>
  关于
</Link>
```

不需要额外的 CSS，这就能提供页面之间的基本交叉淡入淡出动画。

### 2. 使用编程式导航启用视图过渡

使用 `useNavigate` hook 进行编程式导航时，可以通过传递 `viewTransition: true` 选项来启用视图过渡：

```tsx
import { useNavigate } from "react-router";

function NavigationButton() {
  const navigate = useNavigate();

  return (
    <button
      onClick={() =>
        navigate("/about", { viewTransition: true })
      }
    >
      关于
    </button>
  );
}
```

这提供了与在 Link 组件上使用 `viewTransition` prop 相同的交叉淡入淡出动画。

有关使用 View Transitions API 的更多信息，请参阅 Google Chrome 团队的["使用 View Transition API 实现流畅过渡"指南][view-transitions-guide]。

## 图片画廊示例

让我们构建一个图片画廊，演示如何触发和使用视图过渡。我们将创建一个图片列表，点击后以流畅的动画展开为详情视图。

### 1. 创建图片画廊路由

```tsx filename=routes/image-gallery.tsx
import { NavLink } from "react-router";

export const images = [
  "https://remix.run/blog-images/headers/the-future-is-now.jpg",
  "https://remix.run/blog-images/headers/waterfall.jpg",
  "https://remix.run/blog-images/headers/webpack.png",
  // ... 更多图片 ...
];

export default function ImageGalleryRoute() {
  return (
    <div className="image-list">
      <h1>图片列表</h1>
      <div>
        {images.map((src, idx) => (
          <NavLink
            key={src}
            to={`/image/${idx}`}
            viewTransition // 为此链接启用视图过渡
          >
            <p>图片 {idx}</p>
            <img
              className="max-w-full contain-layout"
              src={src}
            />
          </NavLink>
        ))}
      </div>
    </div>
  );
}
```

### 2. 添加过渡样式

为需要在路由之间流畅过渡的元素定义视图过渡名称和动画。

```css filename=app.css
/* 图片网格布局样式 */
.image-list > div {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  column-gap: 10px;
}

.image-list h1 {
  font-size: 2rem;
  font-weight: 600;
}

.image-list img {
  max-width: 100%;
  contain: layout;
}

.image-list p {
  width: fit-content;
}

/* 在导航期间为元素分配过渡名称 */
.image-list a.transitioning img {
  view-transition-name: image-expand;
}

.image-list a.transitioning p {
  view-transition-name: image-title;
}
```

### 3. 创建图片详情路由

详情视图需要使用相同的视图过渡名称来创建无缝的动画。

```tsx filename=routes/image-details.tsx
import { Link } from "react-router";
import { images } from "./home";
import type { Route } from "./+types/image-details";

export default function ImageDetailsRoute({
  params,
}: Route.ComponentProps) {
  return (
    <div className="image-detail">
      <Link to="/" viewTransition>
        返回
      </Link>
      <h1>图片 {params.id}</h1>
      <img src={images[Number(params.id)]} />
    </div>
  );
}
```

### 4. 为详情视图添加匹配的过渡样式

```css filename=app.css
/* 匹配列表视图的过渡名称 */
.image-detail h1 {
  font-size: 2rem;
  font-weight: 600;
  width: fit-content;
  view-transition-name: image-title;
}

.image-detail img {
  max-width: 100%;
  contain: layout;
  view-transition-name: image-expand;
}
```

## 高级用法

你可以使用 render props 或 `useViewTransitionState` hook 来更精确地控制视图过渡。

### 1. 使用 render props

```tsx filename=routes/image-gallery.tsx
<NavLink to={`/image/${idx}`} viewTransition>
  {({ isTransitioning }) => (
    <>
      <p
        style={{
          viewTransitionName: isTransitioning
            ? "image-title"
            : "none",
        }}
      >
        图片 {idx}
      </p>
      <img
        src={src}
        style={{
          viewTransitionName: isTransitioning
            ? "image-expand"
            : "none",
        }}
      />
    </>
  )}
</NavLink>
```

### 2. 使用 `useViewTransitionState` hook

```tsx filename=routes/image-gallery.tsx
function NavImage(props: { src: string; idx: number }) {
  const href = `/image/${props.idx}`;
  // Hook 提供特定路由的过渡状态
  const isTransitioning = useViewTransitionState(href);

  return (
    <Link to={href} viewTransition>
      <p
        style={{
          viewTransitionName: isTransitioning
            ? "image-title"
            : "none",
        }}
      >
        图片 {props.idx}
      </p>
      <img
        src={props.src}
        style={{
          viewTransitionName: isTransitioning
            ? "image-expand"
            : "none",
        }}
      />
    </Link>
  );
}
```

[view-transitions-api]: https://developer.mozilla.org/en-US/docs/Web/API/ViewTransition
[view-transitions-guide]: https://developer.chrome.com/docs/web-platform/view-transitions
