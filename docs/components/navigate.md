---
title: Navigate
---

# `<Navigate>`

<details>
  <summary>类型声明</summary>

```tsx
declare function Navigate(props: NavigateProps): null;

interface NavigateProps {
  to: To;
  replace?: boolean;
  state?: any;
  relative?: RelativeRoutingType;
}
```

</details>

`<Navigate>` 元素在渲染时会改变当前位置。它是 [`useNavigate`][use-navigate] 的组件包装器，接受所有相同的参数作为 props。

<docs-info>拥有基于组件的 `useNavigate` hook 版本，使得在无法使用 hook 的 [`React.Component`](https://reactjs.org/docs/react-component.html) 子类中更容易使用此功能。</docs-info>

```tsx
import * as React from "react";
import { Navigate } from "react-router-dom";

class LoginForm extends React.Component {
  state = { user: null, error: null };

  async handleSubmit(event) {
    event.preventDefault();
    try {
      let user = await login(event.target);
      this.setState({ user });
    } catch (error) {
      this.setState({ error });
    }
  }

  render() {
    let { user, error } = this.state;
    return (
      <div>
        {error && <p>{error.message}</p>}
        {user && (
          <Navigate to="/dashboard" replace={true} />
        )}
        <form
          onSubmit={(event) => this.handleSubmit(event)}
        >
          <input type="text" name="username" />
          <input type="password" name="password" />
        </form>
      </div>
    );
  }
}
```

[use-navigate]: ../hooks/use-navigate
