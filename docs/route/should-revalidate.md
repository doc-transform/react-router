---
title: shouldRevalidate
new: true
---

# `shouldRevalidate`

<details>
  <summary>类型声明</summary>

```ts
interface ShouldRevalidateFunction {
  (args: ShouldRevalidateFunctionArgs): boolean;
}

interface ShouldRevalidateFunctionArgs {
  currentUrl: URL;
  currentParams: AgnosticDataRouteMatch["params"];
  nextUrl: URL;
  nextParams: AgnosticDataRouteMatch["params"];
  formMethod?: Submission["formMethod"];
  formAction?: Submission["formAction"];
  formEncType?: Submission["formEncType"];
  text?: Submission["text"];
  formData?: Submission["formData"];
  json?: Submission["json"];
  actionResult?: any;
  actionStatus?: number;
  defaultShouldRevalidate: boolean;
}
```

</details>

此函数允许你选择退出路由 [loader][loader] 的重新验证，作为一种优化手段。

<docs-warning>此功能仅在使用数据路由器时有效，参见[选择路由器][pickingarouter]</docs-warning>

有多种情况会触发数据重新验证，自动保持 UI 与数据同步：

- 通过以下方式调用 [`action`][action] 后：
  - [`<Form>`][form]、[`<fetcher.Form>`][fetcher]、[`useSubmit`][usesubmit] 或 [`fetcher.submit`][fetcher]
  - 当启用 `future.v7_skipActionErrorRevalidation` 标志时，如果 `action` 返回或抛出 4xx/5xx `Response`，`loader` 默认不会重新验证
  - 你可以通过 `shouldRevalidate` 和 `actionStatus` 参数选择在这些场景中进行重新验证
- 通过 [`useRevalidator`][userevalidator] 触发显式重新验证时
- 当已渲染路由的 [URL 参数][params]发生变化时
- 当 URL 搜索参数发生变化时
- 当导航到与当前 URL 相同的 URL 时

如果你在路由上定义了 `shouldRevalidate`，在为新数据调用路由 loader 之前，它会先检查该函数。如果函数返回 `false`，则 loader *不会*被调用，该 loader 的现有数据将保留在页面上。

<docs-info>
Fetcher 的加载也会触发重新验证，但由于它们加载特定的 URL，不需要担心上述 URL 驱动的重新验证场景。Fetcher 的加载默认仅在 action 提交和显式重新验证请求之后才重新验证。
</docs-info>

```jsx lines=[5-9,14-15,21-22]
<Route
  path="meals-plans"
  element={<MealPlans />}
  loader={loadMealPlans}
  shouldRevalidate={({ currentUrl }) => {
    // 仅在提交来源为 `/meal-plans/new` 路由时才重新验证
    return currentUrl.pathname === "/meal-plans/new";
  }}
>
  <Route
    path="new"
    element={<NewMealPlanForm />}
    // 此 action 之后 `loadMealPlans` 将被重新验证...
    action={createMealPlan}
  />
  <Route
    path=":planId/meal"
    element={<Meal />}
    // ...但此 action 不会，因为来源 URL
    // 不是 "/meal-plans/new"
    action={updateMeal}
  />
</Route>
```

注意这仅适用于已经加载、当前正在渲染且将继续在新 URL 处渲染的数据。新 URL 处的新路由和 fetcher 的数据始终会被首次获取。

<docs-warning>使用此 API 有使 UI 与数据不同步的风险，请谨慎使用！</docs-warning>

[action]: ./action
[form]: ../components/form
[fetcher]: ../hooks/use-fetcher
[usesubmit]: ../hooks/use-submit
[loader]: ./loader
[params]: ./route#dynamic-segments
[pickingarouter]: ../routers/picking-a-router
[userevalidator]: ../hooks/use-revalidator
