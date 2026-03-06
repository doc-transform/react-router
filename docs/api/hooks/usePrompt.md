---
title: usePrompt
unstable: true
---

# unstable_usePrompt

[MODES: framework, data]

<br />
<br />

<docs-warning>此 API 是实验性的，可能在次要/补丁版本中发生破坏性变更。请谨慎使用，并**密切**关注发布说明中的相关变更。</docs-warning>

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.unstable_usePrompt.html)

[`useBlocker`](../hooks/useBlocker) 的封装，向用户显示 [`window.confirm`](https://developer.mozilla.org/en-US/docs/Web/API/Window/confirm) 提示，而无需使用 [`useBlocker`](../hooks/useBlocker) 构建自定义 UI。

`unstable_` 前缀不会被移除，因为此技术存在很多粗糙的边界情况，如果用户在确认对话框打开时点击额外的后退/前进导航，在不同浏览器中的表现差异很大（有时不正确）。使用时风险自负。

```tsx
function ImportantForm() {
  let [value, setValue] = React.useState("");

  // 当输入框中有数据时，阻止导航到其他页面
  unstable_usePrompt({
    message: "Are you sure?",
    when: ({ currentLocation, nextLocation }) =>
      value !== "" &&
      currentLocation.pathname !== nextLocation.pathname,
  });

  return (
    <Form method="post">
      <label>
        Enter some important data:
        <input
          name="data"
          value={value}
          onChange={(e) => setValue(e.target.value)}
        />
      </label>
      <button type="submit">Save</button>
    </Form>
  );
}
```

## 函数签名

```tsx
function usePrompt({
  when,
  message,
}: {
  when: boolean | BlockerFunction;
  message: string;
}): void;
```

## 参数

### options.message

确认对话框中显示的消息。

### options.when

布尔值或返回布尔值的函数，指示是否阻止导航。如果提供函数，它会接收一个包含 `currentLocation` 和 `nextLocation` 属性的对象。

## 返回值

无返回值。
