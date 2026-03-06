---
title: StaticRouter
---

# `<StaticRouter>`

<details>
  <summary>类型声明</summary>

```tsx
declare function StaticRouter(
  props: StaticRouterProps,
): React.ReactElement;

interface StaticRouterProps {
  basename?: string;
  children?: React.ReactNode;
  location?: Path | LocationPieces;
}
```

</details>

`<StaticRouter>` 用于在 [Node.js][node] 中渲染 React Router Web 应用。通过 `location` 属性提供当前位置。

- `<StaticRouter location>` 默认为 `"/"`

```tsx
import * as React from "react";
import * as ReactDOMServer from "react-dom/server";
import { StaticRouter } from "react-router-dom/server";
import http from "http";

function requestHandler(req, res) {
  let html = ReactDOMServer.renderToString(
    <StaticRouter location={req.url}>
      {/* 你的应用的其余部分放在这里 */}
    </StaticRouter>,
  );

  res.write(html);
  res.end();
}

http.createServer(requestHandler).listen(3000);
```

[node]: https://nodejs.org
