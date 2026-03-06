---
title: useFormAction
new: true
---

# `useFormAction`

<details>
  <summary>类型声明</summary>

```tsx
declare function useFormAction(
  action?: string,
  { relative }: { relative?: RelativeRoutingType } = {},
): string;
```

</details>

此 hook 在 [`<Form>`][form] 内部使用，自动将默认和相对 action 解析到上下文中的当前路由。虽然不常见，但你可以直接使用它来执行一些操作，例如计算 `<button formAction>` 的正确 action 来更改按钮所在 `<Form>` 的 action。<small>（是的，HTML 按钮可以更改其表单的 action！）</small>

```tsx
import { useFormAction } from "react-router-dom";

function DeleteButton() {
  return (
    <button
      formAction={useFormAction("destroy")}
      formMethod="post"
    >
      Delete
    </button>
  );
}
```

它也可用于自动解析 [`submit`][usesubmit] 和 [`fetcher.submit`][usefetchersubmit] 的 action。

```tsx
let submit = useSubmit();
let action = useFormAction();
submit(formData, { action });
```

[form]: ../components/form
[usesubmit]: ./use-submit
[usefetchersubmit]: ./use-fetcher#fetchersubmit
