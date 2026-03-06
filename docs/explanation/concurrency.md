---
title: 网络并发管理
---

# 网络并发管理

[MODES: framework, data]

<br/>
<br/>

构建 Web 应用时，管理网络请求可能是一项艰巨的任务。确保数据最新和处理并发请求的挑战通常会导致应用中出现复杂的逻辑来处理中断和竞态条件。React Router 通过自动化网络管理来简化这一过程，同时模拟和扩展了 Web 浏览器的直观行为。

为了帮助理解 React Router 如何处理并发，重要的是要记住在 `form` 提交后，React Router 会从 `loader` 中获取新数据。这称为重新验证。

## 与浏览器行为的自然对齐

React Router 对网络并发的处理深受 Web 浏览器处理文档时默认行为的启发。

### 链接导航

**浏览器行为**：当你在浏览器中点击一个链接，然后在页面过渡完成之前点击另一个链接，浏览器会优先处理最新的操作。它取消初始请求，只关注最后点击的链接。

**React Router 行为**：React Router 以相同方式管理客户端导航。当在 React Router 应用中点击链接时，它会为目标 URL 绑定的每个 `loader` 发起 fetch 请求。如果另一个导航中断了初始导航，React Router 会取消之前的 fetch 请求，确保只有最新的请求继续执行。

### 表单提交

**浏览器行为**：如果你在浏览器中发起一个表单提交，然后很快再次提交另一个表单，浏览器会忽略第一个提交，只处理最新的。

**React Router 行为**：React Router 在处理表单时模拟此行为。如果表单被提交，而在第一个完成之前又发生了另一个提交，React Router 会取消原始的 fetch 请求。然后它等待最新的提交完成后再次触发页面重新验证。

## 并发提交和重新验证

虽然标准浏览器对导航和表单提交限制为一次只能有一个请求，React Router 提升了这种行为。与导航不同，使用 [`useFetcher`][use_fetcher] 可以同时有多个请求在进行中。

React Router 设计用于高效处理对服务器 `action` 的多个表单提交和并发重新验证请求。它确保一旦有新数据可用，状态就会被立即更新。然而，React Router 也通过避免在其他 `action` 引入竞态条件时提交过期数据来防范潜在的陷阱。

例如，如果有三个表单提交正在进行中，其中一个完成了，React Router 会立即用该数据更新 UI，而不等待其他两个，这样 UI 保持响应性和动态性。随着剩余提交的完成，React Router 继续更新 UI，确保显示最新的数据。

使用以下图例：

- `|`：提交开始
- ✓：Action 完成，数据重新验证开始
- ✅：重新验证的数据提交到 UI
- ❌：请求被取消

我们可以用以下图表可视化这个场景：

```text
submission 1: |----✓-----✅
submission 2:    |-----✓-----✅
submission 3:             |-----✓-----✅
```

然而，如果后续提交的重新验证比早先的更快完成，React Router 会丢弃较早的数据，确保只有最新的信息反映在 UI 中：

```text
submission 1: |----✓---------❌
submission 2:    |-----✓-----✅
submission 3:             |-----✓-----✅
```

因为提交 (2) 的重新验证启动晚于提交 (1) 但先于其完成，来自提交 (1) 的请求被取消，只有提交 (2) 的数据被提交到 UI。它是后请求的，所以更可能包含来自 (1) 和 (2) 的更新值。

## 过期数据的可能性

你的用户不太可能遇到这种情况，但在非常罕见的条件下，由于不一致的基础设施，用户仍有可能看到过期数据。即使 React Router 取消了对过期数据的请求，它们仍会到达服务器。在浏览器中取消请求只是释放该请求的浏览器资源；它无法"追赶"并阻止请求到达服务器。在极其罕见的条件下，被取消的请求可能会在中断操作的 `action` 的重新验证完成后更改数据。考虑以下图表：

```text
     👇 被新提交中断
|----❌----------------------✓
       |-------✓-----✅
                             👆
                  初始请求在中断提交完成
                  重新验证之后到达服务器
```

用户现在看到的数据与服务器上的不同。注意这个问题非常罕见，并且在默认浏览器行为中也存在。初始请求在第二个提交和重新验证之后才到达服务器的概率在任何网络和服务器基础设施上都是意外的。如果这对你的基础设施是个问题，你可以在表单提交中发送时间戳，并编写服务器逻辑来忽略过期提交。

## 示例

在像组合框这样的 UI 组件中，每次按键都可能触发一个网络请求。管理如此快速、连续的请求可能很棘手，尤其是在确保显示结果与最新查询匹配时。然而，使用 React Router，这个挑战会自动处理，确保用户看到正确的结果，而开发者无需微观管理网络。

```tsx filename=app/pages/city-search.tsx
export async function loader({ request }) {
  const { searchParams } = new URL(request.url);
  const cities = await searchCities(searchParams.get("q"));
  return cities;
}

export function CitySearchCombobox() {
  const fetcher = useFetcher<typeof loader>();

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

        {/* 用 loader 的数据渲染 */}
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

应用只需要知道如何查询数据和如何渲染它。React Router 处理网络。

## 总结

React Router 为开发者提供了一种直观的、基于浏览器的网络请求管理方法。通过模拟浏览器行为并在需要时增强它们，它简化了并发、重新验证和潜在竞态条件的复杂性。无论你是在构建简单的网页还是复杂的 Web 应用，React Router 确保你的用户交互流畅、可靠且始终保持最新。

[use_fetcher]: ../api/hooks/useFetcher
