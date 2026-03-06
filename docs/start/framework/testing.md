---
title: 测试
order: 9
---

# 测试

[MODES: framework, data]

## 简介

当组件使用 `useLoaderData`、`<Link>` 等时，它们需要在 React Router 应用的上下文中渲染。`createRoutesStub` 函数可以创建该上下文，以便在隔离环境中测试组件。

考虑一个依赖 `useActionData` 的登录表单组件：

```tsx
import { useActionData } from "react-router";

export function LoginForm() {
  const actionData = useActionData();
  const errors = actionData?.errors;
  return (
    <Form method="post">
      <label>
        <input type="text" name="username" />
        {errors?.username && <div>{errors.username}</div>}
      </label>

      <label>
        <input type="password" name="password" />
        {errors?.password && <div>{errors.password}</div>}
      </label>

      <button type="submit">Login</button>
    </Form>
  );
}
```

我们可以使用 `createRoutesStub` 来测试此组件。它接受一个类似路由模块的对象数组，包含 loader、action 和组件。

```tsx
import { createRoutesStub } from "react-router";
import {
  render,
  screen,
  waitFor,
} from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { LoginForm } from "./LoginForm";

test("LoginForm renders error messages", async () => {
  const USER_MESSAGE = "Username is required";
  const PASSWORD_MESSAGE = "Password is required";

  const Stub = createRoutesStub([
    {
      path: "/login",
      Component: LoginForm,
      action() {
        return {
          errors: {
            username: USER_MESSAGE,
            password: PASSWORD_MESSAGE,
          },
        };
      },
    },
  ]);

  // 在 "/login" 处渲染应用 stub
  render(<Stub initialEntries={["/login"]} />);

  // 模拟交互
  userEvent.click(screen.getByText("Login"));
  await waitFor(() => screen.findByText(USER_MESSAGE));
  await waitFor(() => screen.findByText(PASSWORD_MESSAGE));
});
```

## 在框架模式类型中使用

需要注意的是，`createRoutesStub` 是为应用中依赖路由上下文信息（如 `loaderData`、`actionData`、`matches`）的可复用组件的*单元*测试而设计的。这些组件通常通过 Hook（`useLoaderData`、`useActionData`、`useMatches`）或通过从祖先路由组件传递的 props 来获取这些信息。我们**强烈**建议将 `createRoutesStub` 的使用限制在这类可复用组件的单元测试中。

`createRoutesStub` 并*非*为直接测试使用框架模式中 [`Route.*`](../../explanation/type-safety) 类型的路由组件而设计（且可以说是不兼容的）。这是因为 `Route.*` 类型源自你的实际应用——包括真实的 `loader`/`action` 函数以及路由树结构（定义了 `matches` 类型）。当你使用 `createRoutesStub` 时，你提供的是基于传递给 `createRoutesStub` 的路由树的 `loaderData`、`actionData` 甚至 `matches` 的桩值。因此，类型不会与 `Route.*` 类型对齐，你在路由 stub 中使用路由组件时会遇到类型问题。

```tsx filename=routes/login.tsx
export default function Login({
  actionData,
}: Route.ComponentProps) {
  return <Form method="post">...</Form>;
}
```

```tsx filename=routes/login.test.tsx
import LoginRoute from "./login";

test("LoginRoute renders error messages", async () => {
  const Stub = createRoutesStub([
    {
      path: "/login",
      Component: LoginRoute,
      // ^ ❌ 'matches' 属性的类型不兼容。
      action() {
        /*...*/
      },
    },
  ]);

  // ...
});
```

如果你像这样设置测试，这些类型错误通常是准确的。只要你的桩 `loader`/`action` 函数与真实实现匹配，`loaderData`/`actionData` 的类型就是正确的，但如果不匹配，类型就会误导你。

`matches` 更复杂，因为你通常不会桩化所有祖先路由。在这个例子中，没有 `root` 路由，所以 `matches` 只包含你的测试路由，而在运行时它会包含根路由和其他所有祖先路由。目前没有很好的方法自动对齐测试中的类型生成类型与运行时类型。

因此，如果你需要测试路由级组件，我们建议通过集成/E2E 测试（Playwright、Cypress 等）在运行的应用上进行测试，因为当你整体测试路由时，已经超出了单元测试的范畴。

如果你*必须*编写路由的单元测试，可以在测试中添加 `@ts-expect-error` 注释来消除 TypeScript 错误：

```tsx
const Stub = createRoutesStub([
  {
    path: "/login",
    // @ts-expect-error: `matches` 在测试代码和应用代码之间不会对齐
    Component: LoginRoute,
    action() {
      /*...*/
    },
  },
]);
```
