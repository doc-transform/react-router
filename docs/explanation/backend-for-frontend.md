---
title: 前端的后端（BFF）
---

# 前端的后端（BFF）

[MODES: framework]

<br/>
<br/>

虽然 React Router 可以作为你的全栈应用，但它也完美适配"前端的后端"（Backend for Frontend）架构。

BFF 策略采用一个 Web 服务器，其职责范围限于为前端 Web 应用提供服务，并将其连接到所需的服务：数据库、邮件服务、任务队列、现有的后端 API（REST、GraphQL）等。你的 UI 不是从浏览器直接与这些服务集成，而是连接到 BFF，由 BFF 连接到你的服务。

成熟的应用已经在 Ruby、Elixir、PHP 等语言中有大量后端应用代码，没有理由仅仅为了获得 React Router 的好处就将所有代码迁移到服务端 JavaScript 运行时。相反，你可以将 React Router 应用用作前端的后端。

你可以直接在 loader 和 action 中使用 `fetch` 来调用你的后端。

```tsx lines=[7,13,17]
import escapeHtml from "escape-html";

export async function loader() {
  const apiUrl = "https://api.example.com/some-data.json";
  const res = await fetch(apiUrl, {
    headers: {
      Authorization: `Bearer ${process.env.API_TOKEN}`,
    },
  });

  const data = await res.json();

  const prunedData = data.map((record) => {
    return {
      id: record.id,
      title: record.title,
      formattedBody: escapeHtml(record.content),
    };
  });
  return { prunedData };
}
```

与直接从浏览器获取数据相比，这种方法有几个好处。上面高亮的行展示了你可以如何：

1. 简化第三方集成，将令牌和密钥保留在客户端包之外
2. 精简数据，通过网络发送更少的 kB，显著加速你的应用
3. 将大量代码从浏览器包移到服务器，如 `escapeHtml`，从而加速你的应用。此外，将代码移到服务器通常会使代码更易于维护，因为服务端代码不需要担心异步操作的 UI 状态

再次说明，React Router 可以通过使用服务端 JavaScript API 直接与数据库和其他服务通信来作为你唯一的服务器，但它也完美地作为前端的后端工作。继续保留你现有的 API 服务器处理应用逻辑，让 React Router 将 UI 与之连接起来。
