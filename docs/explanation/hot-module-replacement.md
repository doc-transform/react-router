---
title: 热模块替换
---

# 热模块替换

[MODES: framework]

<br/>
<br/>

热模块替换（Hot Module Replacement）是一种无需重新加载页面即可更新应用中模块的技术。它提供了出色的开发体验，React Router 在使用 Vite 时支持此功能。

HMR 会尽最大努力在更新时保留浏览器状态。例如，假设你在一个弹窗中有一个表单，并且你已经填写了所有字段。一旦你保存对代码的任何更改，传统的实时重载会硬刷新页面，导致所有字段被重置。每次你做出更改，你都必须*再次*打开弹窗并*再次*填写表单。

但是使用 HMR，所有这些状态都会*跨更新保留*。

## React Fast Refresh

React 已经有了通过其[虚拟 DOM][virtual-dom] 响应用户交互（如点击按钮）来更新 DOM 的机制。如果 React 也能处理响应代码变更来更新 DOM，那不是很好吗？

这正是 [React Fast Refresh][react-refresh] 的用途！当然，React 是关于组件的，而非通用 JavaScript 代码，所以 React Fast Refresh 只处理导出的 React 组件的热更新。

但 React Fast Refresh 确实有一些你应该了解的限制。

### 类组件状态

React Fast Refresh 不保留类组件的状态。包括内部返回类的高阶组件：

```tsx
export class ComponentA extends Component {} // ❌

export const ComponentB = HOC(ComponentC); // ❌ 如果 HOC 返回类组件则不工作

export function ComponentD() {} // ✅
export const ComponentE = () => {}; // ✅
export default function ComponentF() {} // ✅
```

### 命名函数组件

函数组件必须是命名的，不能是匿名的，React Fast Refresh 才能跟踪变更：

```tsx
export default () => {}; // ❌
export default function () {} // ❌

const ComponentA = () => {};
export default ComponentA; // ✅

export default function ComponentB() {} // ✅
```

### 支持的导出

React Fast Refresh 只能处理组件导出。虽然 React Router 为你管理[路由导出如 `action`、`headers`、`links`、`loader` 和 `meta`][route-module]，但任何用户自定义的导出都会导致完全重载：

```tsx
// 这些导出由 React Router Vite 插件处理
// 以兼容 HMR
export const meta = { title: "Home" }; // ✅
export const links = [
  { rel: "stylesheet", href: "style.css" },
]; // ✅

// 这些导出由 React Router Vite 插件移除
// 因此它们永远不会影响 HMR
export const headers = { "Cache-Control": "max-age=3600" }; // ✅
export const loader = async () => {}; // ✅
export const action = async () => {}; // ✅

// 这既不是路由模块导出，也不是组件导出，
// 因此它会导致该路由的完全重载
export const myValue = "some value"; // ❌

export default function Route() {} // ✅
```

👆 路由本来就不应该导出这样的随机值。如果你想在路由之间复用值，把它们放在自己的非路由模块中：

```ts filename=my-custom-value.ts
export const myValue = "some value";
```

### 变更 Hooks

当组件中添加或移除 hooks 时，React Fast Refresh 无法跟踪该组件的变更，导致仅在下一次渲染时完全重载。hooks 更新后，后续变更应该能再次热更新。例如，如果你给组件添加了 `useState`，你可能会在下一次渲染时丢失该组件的本地状态。

此外，如果你对 hook 的返回值进行解构，当解构的 key 被移除或重命名时，React Fast Refresh 将无法保留该组件的状态。例如：

```tsx
export default function Component({ loaderData }) {
  const { pet } = useMyCustomHook();
  return (
    <div>
      <input />
      <p>我的狗叫 {pet.name}！</p>
    </div>
  );
}
```

如果你将 key `pet` 改为 `dog`：

```diff
 export default function Component() {
-  const { pet } = useMyCustomHook();
+  const { dog } = useMyCustomHook();
   return (
     <div>
       <input />
-      <p>我的狗叫 {pet.name}！</p>
+      <p>我的狗叫 {dog.name}！</p>
     </div>
   );
 }
```

那么 React Fast Refresh 将无法保留 `<input />` 的状态 ❌。

### 组件 Keys

在某些情况下，React 无法区分现有组件被更改和新组件被添加。[React 需要 `key`][react-keys] 来消除这些歧义，并在兄弟元素被修改时跟踪变更。

[virtual-dom]: https://reactjs.org/docs/faq-internals.html#what-is-the-virtual-dom
[react-refresh]: https://github.com/facebook/react/tree/main/packages/react-refresh
[react-keys]: https://react.dev/learn/rendering-lists#why-does-react-need-keys
[route-module]: ../start/framework/route-module
