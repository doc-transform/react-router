---
title: useBeforeUnload
new: true
---

# `useBeforeUnload`

此 hook 只是 `window.onbeforeunload` 的一个辅助封装。它可以用于在用户从你的页面导航离开之前保存重要的应用状态（到浏览器的本地存储等），这样如果他们返回，你就可以恢复任何有状态的信息（恢复表单输入值等）。

```tsx lines=[1,7-11]
import { useBeforeUnload } from "react-router-dom";

function SomeForm() {
  const [state, setState] = React.useState(null);

  // save it off before users navigate away
  useBeforeUnload(
    React.useCallback(() => {
      localStorage.stuff = state;
    }, [state]),
  );

  // read it in when they return
  React.useEffect(() => {
    if (state === null && localStorage.stuff != null) {
      setState(localStorage.stuff);
    }
  }, [state]);

  return <>{/*... */}</>;
}
```
