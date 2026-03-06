---
title: action
new: true
---

# `action`

路由 action 是路由 [loader][loader] "读取"操作对应的"写入"操作。它们为应用提供了使用简单的 HTML 和 HTTP 语义进行数据变更的方式，而 React Router 则抽象了异步 UI 和重新验证的复杂性。这给你提供了 HTML + HTTP 的简单心智模型（浏览器处理异步和重新验证），同时拥有现代 SPA 的行为和 UX 能力。

<docs-warning>此功能仅在使用数据路由器时有效，参见[选择路由器][pickingarouter]</docs-warning>

```tsx
<Route
  path="/song/:songId/edit"
  element={<EditSong />}
  action={async ({ params, request }) => {
    let formData = await request.formData();
    return fakeUpdateSong(params.songId, formData);
  }}
  loader={({ params }) => {
    return fakeGetSong(params.songId);
  }}
/>
```

当应用向路由发送非 get 提交（"post"、"put"、"patch"、"delete"）时，action 就会被调用。这可以通过以下几种方式发生：

```tsx
// 表单
<Form method="post" action="/songs" />;
<fetcher.Form method="put" action="/songs/123/edit" />;

// 命令式提交
let submit = useSubmit();
submit(data, {
  method: "delete",
  action: "/songs/123",
});
fetcher.submit(data, {
  method: "patch",
  action: "/songs/123/edit",
});
```

## `params`

路由参数从[动态片段][dynamicsegments]解析后传递给 action。这对于确定要变更哪个资源很有用：

```tsx
<Route
  path="/projects/:projectId/delete"
  action={({ params }) => {
    return fakeDeleteProject(params.projectId);
  }}
/>
```

## `request`

这是发送到路由的 [Fetch Request][request] 实例。最常见的用例是从请求中解析 [FormData][formdata]：

```tsx
<Route
  action={async ({ request }) => {
    let formData = await request.formData();
    // ...
  }}
/>
```

> Request?!

action 接收一个 "request" 可能初看起来有些奇怪。你是否写过这样的代码？

```tsx [3]
<form
  onSubmit={(event) => {
    event.preventDefault();
    // ...
  }}
/>
```

你到底在阻止什么？

没有 JavaScript 的情况下，仅使用纯 HTML 和 HTTP Web 服务器，被阻止的那个默认事件其实相当不错。浏览器会将表单中的所有数据序列化为 [`FormData`][formdata]，并作为新请求的请求体发送到服务器。与上面的代码类似，React Router 的 [`<Form>`][form] 阻止浏览器发送该请求，而是将请求发送到路由的 action！这使得使用 HTML 和 HTTP 的简单模型就能构建高度动态的 Web 应用。

请记住，`formData` 中的值是从表单提交自动序列化的，因此你的输入框需要有 `name` 属性。

```tsx
<Form method="post">
  <input name="songTitle" />
  <textarea name="lyrics" />
  <button type="submit">Save</button>
</Form>;

// 通过相同的 name 访问
formData.get("songTitle");
formData.get("lyrics");
```

关于 `formData` 的更多信息，请参阅[使用 FormData][workingwithformdata]。

### 可选的序列化类型

注意，当使用 [`useSubmit`][usesubmit] 时，你也可以传递 `encType: "application/json"` 或 `encType: "text/plain"` 来将负载序列化为 `request.json()` 或 `request.text()`。

## 返回 Response

虽然你可以从 action 返回任何内容并通过 [`useActionData`][useactiondata] 访问它，但你也可以返回一个 Web [Response][response]。

更多信息请参阅 [loader 文档][returningresponses]。

## 在 Action 中抛出

你可以在 action 中 `throw` 来跳出当前调用栈（停止运行当前代码），React Router 将重新走"错误路径"。

```tsx [10]
<Route
  action={async ({ params, request }) => {
    const res = await fetch(
      `/api/properties/${params.id}`,
      {
        method: "put",
        body: await request.formData(),
      },
    );
    if (!res.ok) throw res;
    return { ok: true };
  }}
/>
```

更多细节和扩展用例请阅读 [errorElement][errorelement] 文档。

## 处理每个路由的多个 Action

一个相当常见的问题是*"如果我需要在 action 中处理多种不同的行为怎么办？"*有几种方法可以实现，但通常最简单的是在 `<button type="submit">` 上放置 `name`/`value` 并在 action 中使用它来决定执行哪段代码（没错——提交[按钮][button]可以有 name/value 属性！）：

```jsx lines=[3,5,10,30-32,42-44]
async function action({ request }) {
  let formData = await request.formData();
  let intent = formData.get("intent");

  if (intent === "edit") {
    await editSong(formData);
    return { ok: true };
  }

  if (intent === "add") {
    await addSong(formData);
    return { ok: true };
  }

  throw json(
    { message: "Invalid intent" },
    { status: 400 },
  );
}

function Component() {
  let song = useLoaderData();

  // 当歌曲存在时，显示编辑表单
  if (song) {
    return (
      <Form method="post">
        <p>Edit song lyrics:</p>
        {/* 编辑歌曲输入框 */}
        <button type="submit" name="intent" value="edit">
          Edit
        </button>
      </Form>
    );
  }

  // 否则显示添加新歌曲的表单
  return (
    <Form method="post">
      <p>Add new lyrics:</p>
      {/* 添加歌曲输入框 */}
      <button type="submit" name="intent" value="add">
        Add
      </button>
    </Form>
  );
}
```

如果按钮的 name/value 不适合你的用例，你也可以使用隐藏输入来发送 `intent`，或者通过 [`<Form method>`][form-method] 属性提交不同的 HTTP 方法（`POST` 用于添加，`PUT`/`PATCH` 用于编辑，`DELETE` 用于删除）。

[loader]: ./loader
[pickingarouter]: ../routers/picking-a-router
[dynamicsegments]: ./route#dynamic-segments
[formdata]: https://developer.mozilla.org/en-US/docs/Web/API/FormData
[request]: https://developer.mozilla.org/en-US/docs/Web/API/Request
[response]: https://developer.mozilla.org/en-US/docs/Web/API/Response
[url]: https://developer.mozilla.org/en-US/docs/Web/API/URL
[urlsearchparams]: https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams
[migratingtoremix]: ../guides/migrating-to-remix
[useloaderdata]: ../hooks/use-loader-data
[json]: ../fetch/json
[errorelement]: ./error-element
[form]: ../components/form
[workingwithformdata]: ../guides/form-data
[useactiondata]: ../hooks/use-action-data
[usesubmit]: ../hooks/use-submit
[returningresponses]: ./loader#returning-responses
[createbrowserrouter]: ../routers/create-browser-router
[button]: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button
[form-method]: ../components/form#method
