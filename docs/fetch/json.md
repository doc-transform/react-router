---
title: json
new: true
---

# `json`

以下写法的快捷方式：

```jsx
new Response(JSON.stringify(someValue), {
  headers: {
    "Content-Type": "application/json; utf-8",
  },
});
```

通常在 loader 中使用：

```jsx
import { json } from "react-router-dom";

const loader = async () => {
  const data = getSomeData();
  return json(data);
};
```

另请参阅：

- [从 Loader 返回 Response][responses]

[responses]: ../route/loader#returning-responses
