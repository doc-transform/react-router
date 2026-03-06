---
title: 错误边界
---

# 错误边界

[MODES: framework, data]

<br/>
<br/>

为了避免向用户呈现空白页面，路由模块会自动捕获代码中的错误并渲染最近的 `ErrorBoundary`。

错误边界不用于渲染表单验证错误或错误上报。请参阅[表单验证](./form-validation)和[错误上报](./error-reporting)。

## 1. 添加根错误边界

所有应用至少应导出一个根错误边界。它处理三种主要情况：

- 带有状态码和文本的抛出 `data`
- 带有堆栈跟踪的 Error 实例
- 随机抛出的值

### 框架模式

[modes: framework]

在[框架模式][picking-a-mode]中，错误作为 prop 传递给路由级别的错误边界（参见 [`Route.ErrorBoundaryProps`][type-safety]），因此你不需要使用 hook 来获取它：

```tsx filename=root.tsx lines=[1,3-5]
import { Route } from "./+types/root";

export function ErrorBoundary({
  error,
}: Route.ErrorBoundaryProps) {
  if (isRouteErrorResponse(error)) {
    return (
      <>
        <h1>
          {error.status} {error.statusText}
        </h1>
        <p>{error.data}</p>
      </>
    );
  } else if (error instanceof Error) {
    return (
      <div>
        <h1>Error</h1>
        <p>{error.message}</p>
        <p>The stack trace is:</p>
        <pre>{error.stack}</pre>
      </div>
    );
  } else {
    return <h1>Unknown Error</h1>;
  }
}
```

### 数据模式

[modes: data]

在[数据模式][picking-a-mode]中，`ErrorBoundary` 不接收 props，你可以通过 `useRouteError` 访问错误：

```tsx lines=[1,6,16]
import { useRouteError } from "react-router";

let router = createBrowserRouter([
  {
    path: "/",
    ErrorBoundary: RootErrorBoundary,
    Component: Root,
  },
]);

function Root() {
  /* ... */
}

function RootErrorBoundary() {
  let error = useRouteError();
  if (isRouteErrorResponse(error)) {
    return (
      <>
        <h1>
          {error.status} {error.statusText}
        </h1>
        <p>{error.data}</p>
      </>
    );
  } else if (error instanceof Error) {
    return (
      <div>
        <h1>Error</h1>
        <p>{error.message}</p>
        <p>The stack trace is:</p>
        <pre>{error.stack}</pre>
      </div>
    );
  } else {
    return <h1>Unknown Error</h1>;
  }
}
```

## 2. 制造一个 bug

[modes: framework,data]

不建议故意抛出错误来强制渲染错误边界作为控制流手段。错误边界主要用于捕获代码中的非预期错误。

```tsx
export async function loader() {
  return undefined();
}
```

这将渲染第 1 步中 UI 的 `instanceof Error` 分支。

这不仅适用于 loader，还适用于所有路由模块 API：loader、action、组件、headers、links 和 meta。

## 3. 在 loader/action 中抛出 data

[modes: framework,data]

第 2 条规则也有例外，特别是 404 的情况。当 loader 找不到渲染页面所需的内容时，你可以故意 `throw data()`（带有正确的状态码）到最近的错误边界。抛出 404 然后继续即可。

```tsx
import { data } from "react-router";

export async function loader({ params }) {
  let record = await fakeDb.getRecord(params.id);
  if (!record) {
    throw data("Record Not Found", { status: 404 });
  }
  return record;
}
```

这将渲染第 1 步中 UI 的 `isRouteErrorResponse` 分支。

## 4. 嵌套错误边界

当抛出错误时，将渲染"最近的错误边界"。

### 框架模式

[modes: framework]

考虑这些嵌套路由：

```tsx filename="routes.ts"
// ✅ 有错误边界
route("/app", "app.tsx", [
  // ❌ 没有错误边界
  route("invoices", "invoices.tsx", [
    // ✅ 有错误边界
    route("invoices/:id", "invoice-page.tsx", [
      // ❌ 没有错误边界
      route("payments", "payments.tsx"),
    ]),
  ]),
]);
```

下表显示了给定错误来源时将渲染哪个错误边界：

| 错误来源         | 渲染的边界       |
| ---------------- | ---------------- |
| app.tsx          | app.tsx          |
| invoices.tsx     | app.tsx          |
| invoice-page.tsx | invoice-page.tsx |
| payments.tsx     | invoice-page.tsx |

### 数据模式

[modes: data]

在数据模式中，等效的路由树可能如下所示：

```tsx
let router = createBrowserRouter([
  {
    path: "/app",
    Component: App,
    ErrorBoundary: AppErrorBoundary, // ✅ 有错误边界
    children: [
      {
        path: "invoices",
        Component: Invoices, // ❌ 没有错误边界
        children: [
          {
            path: ":id",
            Component: Invoice,
            ErrorBoundary: InvoiceErrorBoundary, // ✅ 有错误边界
            children: [
              {
                path: "payments",
                Component: Payments, // ❌ 没有错误边界
              },
            ],
          },
        ],
      },
    ],
  },
]);
```

下表显示了给定错误来源时将渲染哪个错误边界：

| 错误来源   | 渲染的边界             |
| ---------- | ---------------------- |
| `App`      | `AppErrorBoundary`     |
| `Invoices` | `AppErrorBoundary`     |
| `Invoice`  | `InvoiceErrorBoundary` |
| `Payments` | `InvoiceErrorBoundary` |

## 错误清洗

[modes: framework]

在框架模式下进行生产构建时，服务端发生的任何错误在发送到浏览器之前都会被自动清洗，以防止泄露任何敏感的服务端信息（如堆栈跟踪）。

这意味着在生产环境的浏览器中，抛出的 `Error` 将只有通用的消息且没有堆栈跟踪。原始错误在服务端保持不变。

另请注意，通过 `throw data(yourData)` 发送的数据不会被清洗，因为该数据是预期用于渲染的。

[picking-a-mode]: ../start/modes
[type-safety]: ../explanation/type-safety
