---
title: 竞态条件
---

# 竞态条件

[MODES: framework, data]

<br/>
<br/>

虽然不可能消除应用中所有可能的竞态条件，React Router 会自动处理 Web 用户界面中最常见的竞态条件。

## 浏览器行为

React Router 对网络并发的处理深受 Web 浏览器处理文档行为的启发。

想象一下点击一个链接导航到新文档，然后在新页面完成加载之前点击另一个链接。浏览器会：

1. 取消第一个请求
2. 立即处理新的导航

同样的行为适用于表单提交。当一个待处理的表单提交被新的提交中断时，第一个被取消，新的提交立即被处理。

## React Router 行为

与浏览器一样，被中断的链接和表单提交的导航会取消正在进行的数据请求，并立即处理新事件。

Fetcher 稍微复杂一些，因为它们不是像导航那样的单例事件。Fetcher 不能中断其他 fetcher 实例，但可以中断自身，行为与其他情况相同：取消被中断的请求并立即处理新的请求。

然而，Fetcher 在重新验证方面确实会相互交互。当 fetcher 的 action 请求返回到浏览器后，会为所有页面数据发送重新验证请求。这意味着多个重新验证请求可以同时在进行中。React Router 会提交所有"新鲜"的重新验证响应并取消任何过期的请求。过期请求是任何在已返回的请求*之前*开始的请求。

这种网络管理防止了由网络竞态条件引起的最常见 UI bug。

由于网络是不可预测的，而你的服务器仍然会处理这些被取消的请求，你的后端可能仍然会遇到竞态条件和潜在的数据完整性问题。这些风险与使用纯 HTML `<forms>` 的默认浏览器行为的风险相同，我们认为这种风险很低，并且超出了 React Router 的范围。

## 实际好处

考虑构建一个输入即搜索的组合框。当用户输入时，你向服务器发送请求。每输入一个新字符你就发送一个新请求。重要的是不要向用户显示与文本框中当前值不匹配的结果。

使用 fetcher 时，这会自动为你管理。考虑以下伪代码：

```tsx
// route("/city-search", "./search-cities.ts")
export async function loader({ request }) {
  const { searchParams } = new URL(request.url);
  return searchCities(searchParams.get("q"));
}
```

```tsx
export function CitySearchCombobox() {
  const fetcher = useFetcher();

  return (
    <fetcher.Form action="/city-search">
      <Combobox aria-label="城市">
        <ComboboxInput
          name="q"
          onChange={(event) =>
            // 在 onChange 时提交表单以获取城市列表
            fetcher.submit(event.target.form)
          }
        />

        {fetcher.data ? (
          <ComboboxPopover className="shadow-popup">
            {fetcher.data.length > 0 ? (
              <ComboboxList>
                {fetcher.data.map((city) => (
                  <ComboboxOption
                    key={city.id}
                    value={city.name}
                  />
                ))}
              </ComboboxList>
            ) : (
              <span>未找到结果</span>
            )}
          </ComboboxPopover>
        ) : null}
      </Combobox>
    </fetcher.Form>
  );
}
```

调用 `fetcher.submit` 会自动取消该 fetcher 上的待处理请求。这确保你永远不会向用户显示来自不同输入值的请求结果。
