---
title: 选择路由器
order: 1
new: true
---

# 选择路由器

虽然你的应用只会使用一个路由器，但根据应用运行的环境，有多种路由器可供选择。本文档应该能帮助你确定使用哪一个。

## 使用 v6.4 数据 API

在 v6.4 中，引入了支持新[数据 API][data-apis] 的新路由器：

- [`createBrowserRouter`][createbrowserrouter]
- [`createMemoryRouter`][creatememoryrouter]
- [`createHashRouter`][createhashrouter]
- [`createStaticRouter`][createstaticrouter]

以下路由器不支持数据 API：

- [`<BrowserRouter>`][browserrouter]
- [`<MemoryRouter>`][memoryrouter]
- [`<HashRouter>`][hashrouter]
- [`<NativeRouter>`][nativerouter]
- [`<StaticRouter>`][staticrouter]

我们建议将你的应用更新为使用 6.4 的新路由器之一。数据 API 目前在 React Native 中不受支持，但最终应该会支持。

快速更新到 v6.4 的最简单方式是借助 [`createRoutesFromElements`][createroutesfromelements]，这样你就不需要将 `<Route>` 元素转换为路由对象。

```jsx
import {
  createBrowserRouter,
  createRoutesFromElements,
  Route,
  RouterProvider,
} from "react-router-dom";

const router = createBrowserRouter(
  createRoutesFromElements(
    <Route path="/" element={<Root />}>
      <Route path="dashboard" element={<Dashboard />} />
      {/* ... 等等 */}
    </Route>,
  ),
);

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>,
);
```

## Web 项目

我们推荐所有 Web 项目使用 [`createBrowserRouter`][createbrowserrouter]。

它使用完整 URL 而不是 Web 应用中 `history.pushState` 标准化之前常见的哈希 URL（`#this/stuff`）。完整 URL 对 SEO 更好，对服务端渲染更好，并且与 Web 平台的其余部分更兼容。

如果你将应用托管在静态文件服务器上，你需要将其配置为将所有请求发送到 `index.html`，以避免出现 404 错误。

如果出于某种原因你无法使用完整 URL，[`createHashRouter`][createhashrouter] 是次佳选择。

如果你对数据 API 不感兴趣，可以继续使用 [`<BrowserRouter>`][browserrouter]，或者如果你无法使用完整 URL，使用 [`<HashRouter>`][hashrouter]。

## 测试

使用 React Router API 的组件测试用 [`createMemoryRouter`][creatememoryrouter] 或 [`<MemoryRouter>`][memoryrouter] 是最简单的，而不是使用你在应用中需要 DOM history API 的路由器。

React Router 的一些 API 内部使用 `fetch`，仅从 Node.js v18 开始支持。如果你的项目使用 v17 或更低版本，你应该手动添加 `fetch` polyfill。一种方法是安装 [`whatwg-fetch`](https://www.npmjs.com/package/whatwg-fetch) 并将其添加到 `jest.config.js` 文件中：

```js
module.exports = {
  setupFiles: ["whatwg-fetch"],
  // ...其余配置
};
```

## React Native

你将在 React Native 项目中使用 [`<NativeRouter>`][nativerouter]。

v6.4 的数据 API 目前在 React Native 中不受支持，但最终应该会支持。

## 数据 API

以下 API 在 React Router 6.4 中引入，仅在使用数据路由器时有效：

- [`route.action`][action]
- [`route.errorElement`][errorelement]
- [`route.lazy`][lazy]
- [`route.loader`][loader]
- [`route.shouldRevalidate`][shouldrevalidate]
- [`route.handle`][handle]
- [`<Await>`][await]
- [`<Form>`][form]
- [`<ScrollRestoration>`][scrollrestoration]
- [`useActionData`][useactiondata]
- [`useAsyncError`][useasyncerror]
- [`useAsyncValue`][useasyncvalue]
- [`useFetcher`][usefetcher]
- [`useFetchers`][usefetchers]
- [`useLoaderData`][useloaderdata]
- [`useMatches`][usematches]
- [`useNavigation`][usenavigation]
- [`useRevalidator`][userevalidator]
- [`useRouteError`][userouteerror]
- [`useRouteLoaderData`][userouteloaderdata]
- [`useSubmit`][usesubmit]
- [`useBlocker`][useblocker]
- 在 [Link][viewtransition-link] 和 [useNavigate][viewtransition-navigate] 上支持 `startViewTransition`

[createbrowserrouter]: ./create-browser-router
[createhashrouter]: ./create-hash-router
[creatememoryrouter]: ./create-memory-router
[createstaticrouter]: ./create-static-router
[createroutesfromelements]: ../utils/create-routes-from-elements
[browserrouter]: ../router-components/browser-router
[memoryrouter]: ../router-components/memory-router
[hashrouter]: ../router-components/hash-router
[nativerouter]: ../router-components/native-router
[staticrouter]: ../router-components/static-router
[data-apis]: #data-apis
[action]: ../route/action
[errorelement]: ../route/error-element
[lazy]: ../route/lazy
[loader]: ../route/loader
[shouldrevalidate]: ../route/should-revalidate
[handle]: ../route/route#handle
[await]: ../components/await
[form]: ../components/form
[scrollrestoration]: ../components/scroll-restoration
[useactiondata]: ../hooks/use-action-data
[useasyncerror]: ../hooks/use-async-error
[useasyncvalue]: ../hooks/use-async-value
[useblocker]: ../hooks/use-blocker
[usefetcher]: ../hooks/use-fetcher
[usefetchers]: ../hooks/use-fetchers
[useloaderdata]: ../hooks/use-loader-data
[usematches]: ../hooks/use-matches
[usenavigation]: ../hooks/use-navigation
[userevalidator]: ../hooks/use-revalidator
[userouteerror]: ../hooks/use-route-error
[userouteloaderdata]: ../hooks/use-route-loader-data
[usesubmit]: ../hooks/use-submit
[useblocker]: ../hooks/use-blocker
[viewtransition-link]: ../components/link#viewtransition
[viewtransition-navigate]: ../hooks/use-navigate#optionsviewtransition
