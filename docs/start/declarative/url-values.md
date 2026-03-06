---
title: URL 值
---

# URL 值

[MODES: declarative]

## 路由参数

路由参数是从动态路径段中解析出的值。

```tsx
<Route path="/concerts/:city" element={<City />} />
```

在这个例子中，`:city` 是动态段。解析后的城市值可以通过 `useParams` 获取。

```tsx
import { useParams } from "react-router";

function City() {
  let { city } = useParams();
  let data = useFakeDataLibrary(`/api/v2/cities/${city}`);
  // ...
}
```

## URL 搜索参数

搜索参数是 URL 中 `?` 之后的值。可以通过 `useSearchParams` 访问，它返回一个 [`URLSearchParams`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams) 实例。

```tsx
function SearchResults() {
  let [searchParams] = useSearchParams();
  return (
    <div>
      <p>
        You searched for <i>{searchParams.get("q")}</i>
      </p>
      <FakeSearchResults />
    </div>
  );
}
```

## Location 对象

React Router 创建了一个自定义的 `location` 对象，包含一些有用的信息，可以通过 `useLocation` 访问。

```tsx
function useAnalytics() {
  let location = useLocation();
  useEffect(() => {
    sendFakeAnalytics(location.pathname);
  }, [location]);
}

function useScrollRestoration() {
  let location = useLocation();
  useEffect(() => {
    fakeRestoreScroll(location.key);
  }, [location]);
}
```
