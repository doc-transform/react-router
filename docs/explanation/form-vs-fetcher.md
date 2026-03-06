---
title: Form 与 fetcher
---

# Form 与 fetcher

[MODES: framework, data]

## 概述

在 React Router 中开发提供了丰富的工具集，有时功能会有重叠，这可能让新手感到困惑。高效开发 React Router 的关键在于理解每个工具的细微差别和适用场景。本文旨在阐明何时以及为什么使用特定的 API。

## 关注的 API

- [`<Form>`][form-component]
- [`useFetcher`][use-fetcher]
- [`useNavigation`][use-navigation]

理解这些 API 的区别和交叉点对于高效开发 React Router 至关重要。

## URL 考虑因素

在这些工具之间选择时的主要标准是你是否想要 URL 改变：

- **需要 URL 改变**：在页面之间导航或过渡时，或在某些操作（如创建或删除记录）之后。这确保用户的浏览器历史记录准确反映他们在应用中的浏览轨迹。
  - **预期行为**：在很多情况下，当用户点击后退按钮时，他们应该被带回到上一页。其他时候历史记录条目可能被替换，但 URL 的变化仍然很重要。

- **不需要 URL 改变**：对于不会显著改变当前视图上下文或主要内容的操作。这可能包括更新单个字段或不需要新 URL 或页面重载的次要数据操作。这也适用于使用 fetcher 加载数据的场景，如弹出框、组合框等。

### 当 URL 应该改变时

这些操作通常反映了用户上下文或状态的重大变化：

- **创建新记录**：创建新记录后，通常会将用户重定向到专门展示该新记录的页面，在那里他们可以查看或进一步修改。

- **删除记录**：如果用户在某个特定记录的页面上并决定删除它，逻辑上的下一步是将他们重定向到一个通用页面，如所有记录的列表。

对于这些情况，开发者应考虑结合使用 [`<Form>`][form-component] 和 [`useNavigation`][use-navigation]。这些工具可以协同处理表单提交、调用特定 action、通过组件 props 获取 action 相关数据以及管理导航。

### 当 URL 不应该改变时

这些操作通常更加细微，不需要为用户切换上下文：

- **更新单个字段**：也许用户想要更改列表中某个项目的名称或更新记录的某个特定属性。这个操作很小，不需要新页面或新 URL。

- **从列表中删除记录**：在列表视图中，如果用户删除一个项目，他们可能期望留在列表视图中，只是该项目不再出现在列表中。

- **在列表视图中创建记录**：向列表添加新项目时，让用户保持在该上下文中通常是合理的，让他们看到新项目被添加到列表中而无需完整的页面过渡。

- **为弹出框或组合框加载数据**：当为弹出框或组合框加载数据时，用户的上下文保持不变。数据在后台加载并显示在一个小的、自包含的 UI 元素中。

对于此类操作，[`useFetcher`][use-fetcher] 是首选 API。它功能多样，结合了这些 API 的功能，非常适合 URL 不需要改变的任务。

## API 对比

如你所见，这两组 API 有很多相似之处：

| 导航/URL API              | Fetcher API          |
| ------------------------- | -------------------- |
| `<Form>`                  | `<fetcher.Form>`     |
| `actionData`（组件 prop） | `fetcher.data`       |
| `navigation.state`        | `fetcher.state`      |
| `navigation.formAction`   | `fetcher.formAction` |
| `navigation.formData`     | `fetcher.formData`   |

## 示例

### 创建新记录

```tsx filename=app/pages/new-recipe.tsx lines=[16,23-24,29]
import {
  Form,
  redirect,
  useNavigation,
} from "react-router";
import type { Route } from "./+types/new-recipe";

export async function action({
  request,
}: Route.ActionArgs) {
  const formData = await request.formData();
  const errors = await validateRecipeFormData(formData);
  if (errors) {
    return { errors };
  }
  const recipe = await db.recipes.create(formData);
  return redirect(`/recipes/${recipe.id}`);
}

export function NewRecipe({
  actionData,
}: Route.ComponentProps) {
  const { errors } = actionData || {};
  const navigation = useNavigation();
  const isSubmitting =
    navigation.formAction === "/recipes/new";

  return (
    <Form method="post">
      <label>
        标题: <input name="title" />
        {errors?.title ? <span>{errors.title}</span> : null}
      </label>
      <label>
        配料: <textarea name="ingredients" />
        {errors?.ingredients ? (
          <span>{errors.ingredients}</span>
        ) : null}
      </label>
      <label>
        步骤: <textarea name="directions" />
        {errors?.directions ? (
          <span>{errors.directions}</span>
        ) : null}
      </label>
      <button type="submit">
        {isSubmitting ? "保存中..." : "创建食谱"}
      </button>
    </Form>
  );
}
```

该示例利用 [`<Form>`][form-component]、组件 props 和 [`useNavigation`][use-navigation] 来实现直观的记录创建流程。

使用 `<Form>` 确保了直接且合理的导航。创建记录后，用户自然被引导到新食谱的唯一 URL，强化了操作的结果。

组件 props 连接了服务端和客户端，提供了关于提交问题的即时反馈。这种快速响应使用户能够毫无阻碍地纠正任何错误。

最后，`useNavigation` 动态反映表单的提交状态。这种细微的 UI 变化，如切换按钮的标签，让用户确信他们的操作正在被处理。

综合使用，这些 API 提供了结构化导航和反馈的平衡组合。

### 更新记录

现在考虑我们正在查看一个食谱列表，每个项目上都有删除按钮。当用户点击删除按钮时，我们想要从数据库中删除该食谱并将其从列表中移除，而不离开列表页面。

首先，考虑基本的路由设置以在页面上获取食谱列表：

```tsx filename=app/pages/recipes.tsx
import type { Route } from "./+types/recipes";

export async function loader({
  request,
}: Route.LoaderArgs) {
  return {
    recipes: await db.recipes.findAll({ limit: 30 }),
  };
}

export default function Recipes({
  loaderData,
}: Route.ComponentProps) {
  const { recipes } = loaderData;
  return (
    <ul>
      {recipes.map((recipe) => (
        <RecipeListItem key={recipe.id} recipe={recipe} />
      ))}
    </ul>
  );
}
```

现在我们来看删除食谱的 action 和渲染列表中每个食谱的组件。

```tsx filename=app/pages/recipes.tsx lines=[10,21,27]
import { useFetcher } from "react-router";
import type { Recipe } from "./recipe.server";
import type { Route } from "./+types/recipes";

export async function action({
  request,
}: Route.ActionArgs) {
  const formData = await request.formData();
  const id = formData.get("id");
  await db.recipes.delete(id);
  return { ok: true };
}

export default function Recipes() {
  return (
    // ...
    // 无所谓，某处使用了 <RecipeListItem />
  )
}

function RecipeListItem({ recipe }: { recipe: Recipe }) {
  const fetcher = useFetcher();
  const isDeleting = fetcher.state !== "idle";

  return (
    <li>
      <h2>{recipe.title}</h2>
      <fetcher.Form method="post">
        <input type="hidden" name="id" value={recipe.id} />
        <button disabled={isDeleting} type="submit">
          {isDeleting ? "删除中..." : "删除"}
        </button>
      </fetcher.Form>
    </li>
  );
}
```

在这种场景下使用 [`useFetcher`][use-fetcher] 完美适用。我们不需要导航离开或刷新整个页面，我们想要的是就地更新。当用户删除一个食谱时，`action` 被调用，fetcher 管理相应的状态过渡。

这里的关键优势是上下文的维持。删除完成后用户停留在列表上。fetcher 的状态管理能力被用来提供实时反馈：它在 `"删除中..."` 和 `"删除"` 之间切换，清楚地表明正在进行的过程。

此外，由于每个 `fetcher` 都能自主管理自己的状态，对单个列表项的操作变得独立，确保对一个项的操作不会影响其他项（尽管页面数据的重新验证是一个共享关注点，在[网络并发管理][network-concurrency-management]中有介绍）。

本质上，`useFetcher` 为不需要 URL 或导航变化的操作提供了一种无缝机制，通过提供实时反馈和上下文保留来增强用户体验。

### 标记文章为已读

假设你想在当前用户在页面上停留一段时间并滚动到底部后，标记文章已被阅读。你可以创建一个类似这样的 hook：

```tsx
import { useFetcher } from "react-router";

function useMarkAsRead({ articleId, userId }) {
  const marker = useFetcher();

  useSpentSomeTimeHereAndScrolledToTheBottom(() => {
    marker.submit(
      { userId },
      {
        action: `/article/${articleId}/mark-as-read`,
        method: "post",
      },
    );
  });
}
```

### 用户头像详情弹出框

无论何时显示用户头像，你都可以添加悬停效果，从 loader 获取数据并在弹出框中显示。

```tsx filename=app/pages/user-details.tsx
import { useState, useEffect } from "react";
import { useFetcher } from "react-router";
import type { Route } from "./+types/user-details";

export async function loader({ params }: Route.LoaderArgs) {
  return await fakeDb.user.find({
    where: { id: params.id },
  });
}

type LoaderData = Route.ComponentProps["loaderData"];

function UserAvatar({ partialUser }) {
  const userDetails = useFetcher<LoaderData>();
  const [showDetails, setShowDetails] = useState(false);

  useEffect(() => {
    if (
      showDetails &&
      userDetails.state === "idle" &&
      !userDetails.data
    ) {
      userDetails.load(`/user-details/${partialUser.id}`);
    }
  }, [showDetails, userDetails, partialUser.id]);

  return (
    <div
      onMouseEnter={() => setShowDetails(true)}
      onMouseLeave={() => setShowDetails(false)}
    >
      <img src={partialUser.profileImageUrl} />
      {showDetails ? (
        userDetails.state === "idle" && userDetails.data ? (
          <UserPopup user={userDetails.data} />
        ) : (
          <UserPopupLoading />
        )
      ) : null}
    </div>
  );
}
```

## 总结

React Router 提供了一系列工具来满足各种开发需求。虽然某些功能可能看起来有重叠，但每个工具都是针对特定场景精心设计的。通过理解 `<Form>`、`useFetcher` 和 `useNavigation` 的细微差别和理想应用场景，以及数据如何通过组件 props 流动，开发者可以创建更加直观、响应迅速和用户友好的 Web 应用。

[form-component]: ../api/components/Form
[use-fetcher]: ../api/hooks/useFetcher
[use-navigation]: ../api/hooks/useNavigation
[network-concurrency-management]: ./concurrency
