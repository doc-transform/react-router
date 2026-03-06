---
title: useNavigation
new: true
---

# `useNavigation`

此 hook 告诉你关于页面导航所需的一切，以构建待处理导航指示器和数据变更的乐观 UI。例如：

- 全局加载指示器
- 在变更正在进行时禁用表单
- 为提交按钮添加忙碌指示器
- 在服务器上创建新记录时乐观地显示它
- 在更新记录时乐观地显示新状态

<docs-warning>此功能仅在使用数据路由器时有效，参见[选择路由器][pickingarouter]</docs-warning>

```js
import { useNavigation } from "react-router-dom";

function SomeComponent() {
  const navigation = useNavigation();
  navigation.state;
  navigation.location;
  navigation.formData;
  navigation.json;
  navigation.text;
  navigation.formAction;
  navigation.formMethod;
  navigation.formEncType;
}
```

<docs-warning>没有 `future.v7_normalizeFormMethod` [Future Flag][api-development-strategy] 时，`useNavigation().formMethod` 字段是小写的。这将在 v7 中规范化为大写以与 `fetch()` 行为保持一致，因此请升级你的 React Router v6 应用以采用大写 HTTP 方法。</docs-warning>

## `navigation.state`

- **idle** - 没有正在进行的导航。
- **submitting** - 由于使用 POST、PUT、PATCH 或 DELETE 的表单提交，正在调用路由 action。
- **loading** - 正在调用下一个路由的 loader 以渲染下一个页面。

普通导航和 GET 表单提交经历以下状态转换：

```
idle → loading → idle
```

使用 POST、PUT、PATCH 或 DELETE 的表单提交经历以下状态转换：

```
idle → submitting → loading → idle
```

以下是一个简单的提交按钮，当导航状态变化时会改变其文本：

```tsx
function SubmitButton() {
  const navigation = useNavigation();

  const text =
    navigation.state === "submitting"
      ? "Saving..."
      : navigation.state === "loading"
        ? "Saved!"
        : "Go";

  return <button type="submit">{text}</button>;
}
```

虽然 `navigation.state` 提供了活跃导航的高级状态，你可以通过将它与其他 `navigation` 属性结合来推断更细粒度的信息：

```js
// Is this just a normal load?
let isNormalLoad =
  navigation.state === "loading" &&
  navigation.formData == null;

// Are we reloading after an action?
let isReloading =
  navigation.state === "loading" &&
  navigation.formData != null &&
  navigation.formAction === navigation.location.pathname;

// Are we redirecting after an action?
let isRedirecting =
  navigation.state === "loading" &&
  navigation.formData != null &&
  navigation.formAction !== navigation.location.pathname;
```

## `navigation.formData`

任何从 `<Form>` 或 `useSubmit` 发起的 POST、PUT、PATCH 或 DELETE 导航都会附带你的表单提交数据。这主要用于通过 `submission.formData` [`FormData`](https://developer.mozilla.org/en-US/docs/Web/API/FormData) 对象构建“乐观 UI”。

对于 GET 表单提交，`formData` 将为空，数据将反映在 `navigation.location.search` 中。

## `navigation.json`

任何从 `useSubmit(payload, { encType: "application/json" })` 发起的 POST、PUT、PATCH 或 DELETE 导航都会在 `navigation.json` 中提供你的 JSON 值。

## `navigation.text`

任何从 `useSubmit(payload, { encType: "text/plain" })` 发起的 POST、PUT、PATCH 或 DELETE 导航都会在 `navigation.text` 中提供你的文本值。

## `navigation.location`

此属性告诉你下一个 [location][location] 将是什么。

请注意，如果表单正在提交到链接指向的 URL，此链接不会显示为“待处理”，因为我们只对“loading”状态执行此操作。表单将包含状态为“submitting”时的待处理 UI，一旦 action 完成，链接就会变为待处理状态。

## `navigation.formAction`

任何从 `<Form>` 或 `useSubmit` 发起的 POST、PUT、PATCH 或 DELETE 导航都会在 `navigation.formAction` 中提供表单提交 action 路由的路径值。

对于 GET 表单提交，`navigation.formAction` 将为空。

如果你在 `example.com/id` 提交表单，那么 `navigation.formAction` 将是 "/id"

## `navigation.formMethod`

任何从 `<Form>` 或 `useSubmit` 发起的 POST、PUT、PATCH 或 DELETE 导航都会在 `navigation.formMethod` 中提供表单提交方法的值。

对于 GET 表单提交，`navigation.formMethod` 将为空。

以下是一个示例。请注意 `navigation.formMethod` 是小写的：

```tsx
function SubmitButton() {
  const navigation = useNavigation();
  if (navigation.formMethod) {
    console.log(navigation.formMethod); // post
  }

  return (
    <Form method="POST">
      <button>Submit</button>
    </Form>
  );
}
```

## `navigation.formEncType`

任何从 `<Form>` 或 `useSubmit` 发起的 POST、PUT、PATCH 或 DELETE 导航都会在 `navigation.formEncType` 中提供表单提交方法的值。

此属性可以是以下四个值之一："text/plain"、"application/json"、"multipart/form-data" 或 "application/x-www-form-urlencoded"。

对于 GET 表单提交，`navigation.formEncType` 将为空。

[location]: ../utils/location
[pickingarouter]: ../routers/picking-a-router
[api-development-strategy]: ../guides/api-development-strategy
