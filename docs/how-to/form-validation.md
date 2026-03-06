---
title: 表单验证
---

# 表单验证

[MODES: framework, data]

<br/>
<br/>

本指南介绍了一个简单的注册表单实现。你可能需要将这些概念与第三方验证库和错误组件结合使用，但本指南只关注 React Router 中的相关机制。

## 1. 设置

我们先创建一个包含表单的基本注册路由。

```ts filename=app/routes.ts
import {
  type RouteConfig,
  route,
} from "@react-router/dev/routes";

export default [
  route("signup", "signup.tsx"),
] satisfies RouteConfig;
```

```tsx filename=signup.tsx
import type { Route } from "./+types/signup";
import { useFetcher } from "react-router";

export default function Signup(_: Route.ComponentProps) {
  let fetcher = useFetcher();
  return (
    <fetcher.Form method="post">
      <p>
        <input type="email" name="email" />
      </p>

      <p>
        <input type="password" name="password" />
      </p>

      <button type="submit">注册</button>
    </fetcher.Form>
  );
}
```

## 2. 定义 Action

在这一步中，我们将在 `Signup` 组件所在的同一文件中定义一个服务端 `action`。请注意，这里的目的是提供对相关机制的广泛概述，而不是深入探讨表单验证规则或错误对象结构。我们将使用简单的邮箱和密码检查来演示核心概念。

```tsx filename=signup.tsx
import type { Route } from "./+types/signup";
import { redirect, useFetcher, data } from "react-router";

export default function Signup(_: Route.ComponentProps) {
  // 为简洁起见省略
}

export async function action({
  request,
}: Route.ActionArgs) {
  const formData = await request.formData();
  const email = String(formData.get("email"));
  const password = String(formData.get("password"));

  const errors = {};

  if (!email.includes("@")) {
    errors.email = "邮箱地址无效";
  }

  if (password.length < 12) {
    errors.password = "密码应至少为 12 个字符";
  }

  if (Object.keys(errors).length > 0) {
    return data({ errors }, { status: 400 });
  }

  // 验证成功时重定向到仪表盘
  return redirect("/dashboard");
}
```

如果发现任何验证错误，它们会从 `action` 返回给 fetcher。这是我们向 UI 发出信号的方式，表明某些内容需要修正，否则用户将被重定向到仪表盘。

注意 `data({ errors }, { status: 400 })` 调用。设置 400 状态码是向客户端发出验证错误信号的 Web 标准方式（Bad Request）。在 React Router 中，只有 2xx 状态码才会触发页面数据重新验证，因此发送 400 状态码可以防止 `action` 之后正常的重新验证。

## 3. 显示验证错误

最后，我们将修改 `Signup` 组件，从 `fetcher.data` 中显示验证错误（如果有的话）。

```tsx filename=signup.tsx lines=[3,8,13-15]
export default function Signup(_: Route.ComponentProps) {
  let fetcher = useFetcher();
  let errors = fetcher.data?.errors;
  return (
    <fetcher.Form method="post">
      <p>
        <input type="email" name="email" />
        {errors?.email ? <em>{errors.email}</em> : null}
      </p>

      <p>
        <input type="password" name="password" />
        {errors?.password ? (
          <em>{errors.password}</em>
        ) : null}
      </p>

      <button type="submit">注册</button>
    </fetcher.Form>
  );
}
```
