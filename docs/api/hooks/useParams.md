---
title: useParams
---

# useParams

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useParams.html)

返回当前 URL 中由路由匹配的动态参数的键/值对对象。子路由会继承其父路由的所有参数。

假设路由模式为 `/posts/:postId`，匹配 URL `/posts/123`，则 `params.postId` 的值为 `"123"`。

```tsx
import { useParams } from "react-router";

function SomeComponent() {
  let params = useParams();
  params.postId;
}
```

## 函数签名

```tsx
function useParams<
  ParamsOrKey extends
    | string
    | Record<string, string | undefined> = string,
>(): Readonly<
  [ParamsOrKey] extends [string]
    ? Params<ParamsOrKey>
    : Partial<ParamsOrKey>
>;
```

## 返回值

包含动态路由参数的对象。

## 示例

### 基本用法

```tsx
import { useParams } from "react-router";

// 声明式路由：
<Route path="/posts/:postId" element={<Post />} />;

// 或数据路由：
createBrowserRouter([
  {
    path: "/posts/:postId",
    component: Post,
  },
]);

// 或在 routes.ts 中：
route("/posts/:postId", "routes/post.tsx");
```

在组件中访问参数：

```tsx
import { useParams } from "react-router";

export default function Post() {
  let params = useParams();
  return <h1>Post: {params.postId}</h1>;
}
```

### 多个参数

路径模式可以包含多个参数：

```tsx
"/posts/:postId/comments/:commentId";
```

所有参数都可以在 params 对象中获取：

```tsx
import { useParams } from "react-router";

export default function Post() {
  let params = useParams();
  return (
    <h1>
      Post: {params.postId}, Comment: {params.commentId}
    </h1>
  );
}
```

### 通配符参数

通配符参数使用 `*` 定义：

```tsx
"/files/*";
```

匹配的值可以在 params 对象中以如下方式获取：

```tsx
import { useParams } from "react-router";

export default function File() {
  let params = useParams();
  let catchall = params["*"];
  // ...
}
```

你可以解构通配符参数：

```tsx
export default function File() {
  let { "*": catchall } = useParams();
  console.log(catchall);
}
```
