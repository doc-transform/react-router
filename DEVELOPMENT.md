# React Router 开发指南

## 发布

新的 7.x 版本应从源自 `dev` 分支的发布分支创建。如果你要进行 6.x 发布，请参阅下面的 [v6 部分](#v6-releases)。

当你准备开始发布流程时：

- 确保已从 GitHub 拉取 `dev` 和 `main` 分支的所有更改
  - `git checkout main && git pull origin main`
  - `git checkout dev && git pull origin dev`
- 检出 `dev` 分支
  - `git checkout dev`
- 创建新的 `release-next` 分支
  - `git checkout -b release-next`
  - 从技术上讲，任何 `release-*` 分支名称都可以，因为这是触发我们最终发布版本的 GitHub CI 工作流的条件 - 但我们始终使用 `release-next`
  - 我们使用 `release-v6` 进行[持续的 v6 发布](#v6-releases)
- 将 `main` 合并到 `release-next` 分支
  - `git merge --no-ff main`

Changesets 将承担我们发布的大部分繁重工作。当对代码库进行更改时，应附带一个 changeset 文件来记录更改。这些文件将决定 Changesets 如何对我们的包进行版本控制以及更新日志中显示的内容。

### 开始新的预发布

- 确保你在新的 `release-next` 分支上
  - `git checkout release-next`
- 使用 `pre` 标签进入 Changesets 预发布模式：
  - `pnpm changeset pre enter pre`
- 提交更改并将 `release-next` 分支推送到 GitHub
  - `git commit -a -m "Enter prerelease mode"`
  - `git push --set-upstream origin release-next`
- 等待 changesets CI 工作流完成，它将打开一个指向 `release-next` 的 PR，该 PR 将递增所有版本号并生成更新日志
- 如果你需要/想要对 `CHANGELOG.md` 文件进行任何更改，可以直接提交到 PR 分支
  - 预发布通常不需要这样做
- 一旦 changeset 文件状态良好，将 PR 合并到 `release-next`
- PR 合并后，发布工作流将发布更新的 `X.Y.Z-pre.*` 包到 npm

### 准备发布说明草稿

- 此时，你可以开始在仓库根目录的 `CHANGELOG.md` 文件中撰写最终稳定版本的发布说明
  - 复制注释掉的新版本模板并相应更新版本号和链接
  - 将所有包中的相关更新日志条目复制到发布说明中并进行调整
    - `find packages -name 'CHANGELOG.md' -mindepth 2 -maxdepth 2 -exec code {} \;`
  - 将这些更改直接提交到 `release-next` 分支 - 由于不包含 changeset，它们不会触发新的预发布

### 迭代预发布

你可能需要在发布最终稳定版本之前对预发布进行更改。方法如下：

- 从 `release-next` 创建分支并进行所需的更改
- 创建新的 changeset：`pnpm changeset`
  - **重要：** 即使你最终不想将这些更改包含在日志中，这也是必需的。请记住，更新日志可以在发布前编辑，但 Changeset 版本脚本需要看到新的 changeset 才能创建新版本
- 将分支推送到 GitHub 并向 `release-next` 提交 PR
- 审查/批准后，将 PR 合并到 `release-next` 分支
- 等待发布工作流完成，Changesets action 将打开其 PR 以递增所有版本号
  - 注意：如果需要更多更改，只需将它们合并到 `release-next`，此 PR 将自动就地更新
- 审查 PR，进行必要的调整，然后将其合并到 `release-next` 分支
- PR 合并后，发布工作流将发布更新的 `X.Y.Z-pre.*` 包到 npm
- 确保将新的 changeset 内容复制到仓库根目录 `CHANGELOG.md` 文件中的稳定版本发布说明中

### 发布稳定版本

- 在 `release-next` 分支中退出 Changesets 预发布模式：
  - `pnpm changeset pre exit`
- 将编辑后的预发布文件连同所有未发布的 changeset 一起提交，并将 `release-next` 分支推送到 GitHub
- 等待发布工作流完成 - 工作流中的 Changesets action 将打开一个 PR，递增所有版本号并生成稳定版本的更新日志
- 审查 PR 中更新的 `CHANGELOG` 文件并进行必要的调整
  - `find packages -name 'CHANGELOG.md' -mindepth 2 -maxdepth 2 -exec code {} \;`
  - 我们的自动发布流程应该已经移除了预发布条目
- 最终确定发布说明
  - 这在仓库根目录的 `CHANGELOG.md` 文件中应该已经处于相当好的状态，因为每次预发布都会添加更改
  - 快速检查所有迭代的预发布 changeset 是否已被复制
- 将 PR 合并到 `release-next` 分支
- PR 合并后，发布工作流将发布更新的包到 npm
- 发布完成后：
  - 拉取包含你刚合并的 PR 的最新 `release-next` 分支
  - 使用**非快进合并**将 `release-next` 分支合并到 `main` 并推送到 GitHub
    - `git checkout main`
    - `git merge --no-ff release-next`
    - `git push origin main`
    - _注意：_ 对于 `v7.0.0` 稳定版本，这里可能会在 `docs/**/*.md` 文件上有大量冲突，因为我们对 v6 文档进行了更改，但在 `dev` 中我们删除了许多这些文件，转而使用自动生成的 API 文档。要解决这些冲突，我们应该接受 `release-next` 分支的删除操作。
  - 使用**非快进合并**将 `release-next` 分支合并到 `dev` 并推送到 GitHub
    - `git checkout dev`
    - `git merge --no-ff release-next`
    - `git push origin dev`
  - 将 `react-router@6.x.y` 标签转换为 GitHub 上名为 `v6.x.y` 的 Release，并添加指向 `CHANGELOG.md` 中发布标题的深层链接
  - 删除本地和 GitHub 上的 `release-next` 分支

### 热修复发布

热修复发布遵循与上述标准发布相同的流程，但 `release-next` 分支应从最新的 `main` 而非 `dev` 分支创建。一旦稳定的热修复发布完成，`release-next` 分支应像正常发布一样合并回 `main` 和 `dev`。

### v6 发布

6.x 发布以类似于上述流程进行管理，但从 `v6` 分支开始，且不会自动将更改合并回 `dev`/`main`。

- 6.x 的更改应带着 changeset 向 `v6` 分支提交 PR
- 如果这些更改也应应用于 v7，请 cherry-pick 或重新在 `dev` 分支上进行这些更改（包括 changeset）。这些更改将随下一个 v7 发布进入 `main`。
- 6.x 的发布流程启动方式与上述相同，但有几个例外：
  - 从 `v6` 而非 `dev` 分支创建
  - 使用 `release-v6` 而非 `release-next`
  - **不要**将 `main` 合并到 `release-v6`
- 步骤：
  - `git checkout v6 && git pull origin v6`
  - `git checkout -b release-v6`
  - `pnpm changeset pre enter pre-v6`
  - PR 和迭代预发布的流程保持不变
- 稳定版本发布后：
  - 使用**普通合并**将 `release-v6` 合并回 `v6`
  - **不要**将 `release-v6` 合并到 `main`
  - 手动将新的根 `CHANGELOG.md` 条目复制到 `main` 和 `dev`
    - 后续 v6 发布我们不需要将各个 `packages/*/CHANGELOG.md` 更新回移到 `main`
  - *代码*更改应该已经在 `dev` 分支中
    - 这应该在进行 v6 更改时就已完成（弃用警告等更改除外）
    - 确认此版本中的提交是否已包含在 `dev` 中，如果没有，可以通过 cherry-pick 提交或重新进行工作来手动带入

### 实验性发布

实验性发布和热修复不需要从 `dev` 分支创建。实验性发布可以从任何位置创建分支，因为它们不面向一般用途。

- 为发布创建新分支：`git checkout -b release-experimental`
- 进行所需的更改并提交：`git add . && git commit "experimental changes!"`
- 更新版本号并创建发布标签：`pnpm run version:experimental`
- 推送到 GitHub：`git push origin --follow-tags`
- CI 工作流应该会从实验性标签自动触发，将发布版本发布到 npm
