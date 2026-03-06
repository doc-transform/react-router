---
title: 导航拦截
---

# 导航拦截

[MODES: framework, data]

<br/>
<br/>

当用户正在执行某个工作流程（如填写重要表单）时，你可能希望阻止他们离开页面。

本示例将展示：

- 设置一个带有表单和通过 fetcher 调用的 action 的路由
- 当表单有修改时阻止导航
- 当用户尝试离开页面时显示确认提示

## 1. 设置带有表单的路由

添加一个带有表单的路由，我们将使用"联系"路由作为示例：

```ts filename=routes.ts
import {
  type RouteConfig,
  index,
  route,
} from "@react-router/dev/routes";

export default [
  index("routes/home.tsx"),
  route("contact", "routes/contact.tsx"),
] satisfies RouteConfig;
```

将表单添加到联系路由模块：

```tsx filename=routes/contact.tsx
import { useFetcher } from "react-router";
import type { Route } from "./+types/contact";

export async function action({
  request,
}: Route.ActionArgs) {
  let formData = await request.formData();
  let email = formData.get("email");
  let message = formData.get("message");
  console.log(email, message);
  return { ok: true };
}

export default function Contact() {
  let fetcher = useFetcher();

  return (
    <fetcher.Form method="post">
      <p>
        <label>
          Email: <input name="email" type="email" />
        </label>
      </p>
      <p>
        <textarea name="message" />
      </p>
      <p>
        <button type="submit">
          {fetcher.state === "idle" ? "发送" : "发送中..."}
        </button>
      </p>
    </fetcher.Form>
  );
}
```

## 2. 添加脏状态和 onChange 处理器

为了跟踪表单的脏状态，我们使用一个布尔值和一个简单的表单 onChange 处理器。你可能需要用不同方式跟踪脏状态，但这对本指南来说足够了。

```tsx filename=routes/contact.tsx lines=[2,8-12]
export default function Contact() {
  let [isDirty, setIsDirty] = useState(false);
  let fetcher = useFetcher();

  return (
    <fetcher.Form
      method="post"
      onChange={(event) => {
        let email = event.currentTarget.email.value;
        let message = event.currentTarget.message.value;
        setIsDirty(Boolean(email || message));
      }}
    >
      {/* 已有代码 */}
    </fetcher.Form>
  );
}
```

## 3. 当表单有修改时阻止导航

```tsx filename=routes/contact.tsx lines=[1,6-8]
import { useBlocker } from "react-router";

export default function Contact() {
  let [isDirty, setIsDirty] = useState(false);
  let fetcher = useFetcher();
  let blocker = useBlocker(
    useCallback(() => isDirty, [isDirty]),
  );

  // ... 已有代码
}
```

虽然现在可以阻止导航了，但用户没有办法确认。

## 4. 显示确认 UI

这里使用简单的 div，但你可能想用模态对话框。

```tsx filename=routes/contact.tsx lines=[19-41]
export default function Contact() {
  let [isDirty, setIsDirty] = useState(false);
  let fetcher = useFetcher();
  let blocker = useBlocker(
    useCallback(() => isDirty, [isDirty]),
  );

  return (
    <fetcher.Form
      method="post"
      onChange={(event) => {
        let email = event.currentTarget.email.value;
        let message = event.currentTarget.message.value;
        setIsDirty(Boolean(email || message));
      }}
    >
      {/* 已有代码 */}

      {blocker.state === "blocked" && (
        <div>
          <p>等等！你还没有发送消息：</p>
          <p>
            <button
              type="button"
              onClick={() => blocker.proceed()}
            >
              离开
            </button>{" "}
            <button
              type="button"
              onClick={() => blocker.reset()}
            >
              留在这里
            </button>
          </p>
        </div>
      )}
    </fetcher.Form>
  );
}
```

如果用户点击"离开"，`blocker.proceed()` 将继续导航。如果点击"留在这里"，`blocker.reset()` 将清除阻止器并让他们留在当前页面。

## 5. 当 action 完成时重置阻止器

如果用户没有点击"离开"或"留在这里"，而是提交了表单，阻止器仍然会处于活动状态。让我们在 action 完成时通过 effect 重置阻止器。

```tsx filename=routes/contact.tsx
useEffect(() => {
  if (fetcher.data?.ok) {
    if (blocker.state === "blocked") {
      blocker.reset();
    }
  }
}, [fetcher.data]);
```

## 6. 当 action 完成时清除表单

虽然与导航拦截无关，让我们在 action 完成时通过 ref 清除表单。

```tsx
let formRef = useRef<HTMLFormElement>(null);

// 将它放在表单上
<fetcher.Form
  ref={formRef}
  method="post"
  onChange={(event) => {
    // ... 已有代码
  }}
>
  {/* 已有代码 */}
</fetcher.Form>;
```

```tsx
useEffect(() => {
  if (fetcher.data?.ok) {
    // 在 effect 中清除表单
    formRef.current?.reset();
    if (blocker.state === "blocked") {
      blocker.reset();
    }
  }
}, [fetcher.data]);
```

另外，如果当前导航被阻止，你可以选择继续被阻止的导航，而不是重置阻止器。

```tsx
useEffect(() => {
  if (fetcher.data?.ok) {
    if (blocker.state === "blocked") {
      // 继续被阻止的导航
      blocker.proceed();
    } else {
      formRef.current?.reset();
    }
  }
}, [fetcher.data]);
```

在这种情况下，用户流程是：

- 用户填写表单
- 用户忘记点击"发送"，转而点击了一个链接
- 导航被阻止，确认消息显示
- 用户没有点击"离开"或"留在这里"，而是提交了表单
- 用户被带到请求的页面
