# React Router 开发指南

## 发布

新的 7.x 版本应从 `dev` 分支创建的发布分支上创建。如果你要发布 6.x 版本，请参阅下面的 [v6 部分](#v6-发布)。

当你准备开始发布流程时：

- 确保你已从 GitHub 拉取了 `dev` 和 `main` 分支的所有变更
  - `git checkout main && git pull origin main`
  - `git checkout dev && git pull origin dev`
- 切换到 `dev` 分支
  - `git checkout dev`
- 创建一个新的 `release-next` 分支
  - `git checkout -b release-next`
  - 从技术上讲，任何 `release-*` 分支名称都可以，因为这是触发我们 GitHub CI 工作流最终发布版本的条件——但我们始终使用 `release-next`
  - 我们使用 `release-v6` 进行[持续的 v6 发布](#v6-发布)
- 将 `main` 合并到 `release-next` 分支
  - `git merge --no-ff main`

Changesets 会为我们的发布承担大部分繁重工作。当代码库发生更改时，应附带一个 changeset 文件来记录该更改。这些文件将决定 Changesets 如何对我们的包进行版本管理以及变更日志中显示的内容。

### 开始新的预发布

- 确保你在新的 `release-next` 分支上
  - `git checkout release-next`
- 使用 `pre` 标签进入 Changesets 预发布模式：
  - `pnpm changeset pre enter pre`
- 提交更改并将 `release-next` 分支推送到 GitHub
  - `git commit -a -m "Enter prerelease mode"`
  - `git push --set-upstream origin release-next`
- 等待 changesets CI 工作流完成，它会打开一个指向 `release-next` 的 PR，该 PR 将递增所有版本并生成变更日志
- 如果你需要/想要对 `CHANGELOG.md` 文件进行任何更改，可以直接提交到 PR 分支
  - 预发布通常不需要这样做
- 当 changeset 文件状态良好后，将 PR 合并到 `release-next`
- PR 合并后，发布工作流将把更新后的 `X.Y.Z-pre.*` 包发布到 npm

### 准备发布说明草稿

- 此时，你可以开始在仓库根目录的 `CHANGELOG.md` 文件中编写最终稳定版本的发布说明
  - 复制新版本的注释模板并相应更新版本号和链接
  - 将所有包的相关变更日志条目复制到发布说明中并进行相应调整
    - `find packages -name 'CHANGELOG.md' -mindepth 2 -maxdepth 2 -exec code {} \;`
  - 将这些更改直接提交到 `release-next` 分支——它们不会触发新的预发布，因为不包含 changeset

### 迭代预发布

你可能需要在发布最终稳定版之前对预发布进行更改。操作步骤：

- 从 `release-next` 创建分支并进行所需的更改
- 创建新的 changeset：`pnpm changeset`
  - **重要：** 即使你最终不想在日志中包含这些更改，这也是必需的。请记住，变更日志可以在发布前编辑，但 Changeset 版本脚本需要看到新的 changeset 才能创建新版本
- 将分支推送到 GitHub 并向 `release-next` 提交 PR
- 审核/批准后，将 PR 合并到 `release-next` 分支
- 等待发布工作流完成，Changesets action 会打开其 PR 来递增所有版本
  - 注意：如果需要更多更改，可以直接合并到 `release-next`，此 PR 会自动就地更新
- 审核 PR，进行必要的调整，然后合并到 `release-next` 分支
- PR 合并后，发布工作流将把更新后的 `X.Y.Z-pre.*` 包发布到 npm
- 确保将新的 changeset 内容复制到仓库根目录 `CHANGELOG.md` 文件中的稳定版发布说明中

### 发布稳定版

- 在 `release-next` 分支中退出 Changesets 预发布模式：
  - `pnpm changeset pre exit`
- 提交编辑后的预发布文件以及任何未发布的 changeset，并将 `release-next` 分支推送到 GitHub
- 等待发布工作流完成——工作流中的 Changesets action 会打开一个 PR 来递增所有版本并为稳定版生成变更日志
- 审核 PR 中更新的 `CHANGELOG` 文件并进行必要的调整
  - `find packages -name 'CHANGELOG.md' -mindepth 2 -maxdepth 2 -exec code {} \;`
  - 我们的自动化发布流程应该已经删除了预发布条目
- 最终确定发布说明
  - 仓库根目录的 `CHANGELOG.md` 文件应该已经相当完善，因为每次预发布都会添加更改
  - 快速检查所有迭代的预发布 changeset 是否都已复制过来
- 将 PR 合并到 `release-next` 分支
- PR 合并后，发布工作流将把更新后的包发布到 npm
- 版本发布后：
  - 拉取包含你刚合并的 PR 的最新 `release-next` 分支
  - 使用**非快进合并**将 `release-next` 分支合并到 `main` 并推送到 GitHub
    - `git checkout main`
    - `git merge --no-ff release-next`
    - `git push origin main`
    - _注意：_ 对于 `v7.0.0` 稳定版发布，这里可能会在 `docs/**/*.md` 文件上出现大量冲突，因为我们对 v6 文档进行了更改，但在 `dev` 中我们删除了很多这些文件，转而使用自动生成的 API 文档。要解决这些冲突，我们应该接受来自 `release-next` 分支的删除。
  - 使用**非快进合并**将 `release-next` 分支合并到 `dev` 并推送到 GitHub
    - `git checkout dev`
    - `git merge --no-ff release-next`
    - `git push origin dev`
  - 将 `react-router@6.x.y` 标签转换为 GitHub 上的 Release，命名为 `v6.x.y`，并添加指向 `CHANGELOG.md` 中发布标题的深层链接
  - 在本地和 GitHub 上删除 `release-next` 分支

### 热修复发布

热修复发布遵循与上述标准发布相同的流程，但 `release-next` 分支应从最新的 `main` 而非 `dev` 创建。稳定的热修复发布后，`release-next` 分支应像正常发布一样合并回 `main` 和 `dev`。

### v6 发布

6.x 版本的管理流程与上述类似，但从 `v6` 分支进行，且不会自动将更改合并回 `dev`/`main`。

- 6.x 的更改应通过 PR 提交到 `v6` 分支，并附带 changeset
- 如果这些更改也应应用于 v7，请将这些更改 cherry-pick 或重新应用到 `dev` 分支（包括 changeset）。这些更改将随下一个 v7 版本进入 `main`。
- 启动 6.x 发布流程与上述相同，但有一些例外：
  - 从 `v6` 而非 `dev` 创建分支
  - 使用 `release-v6` 而非 `release-next`
  - **不要**将 `main` 合并到 `release-v6`
- 步骤：
  - `git checkout v6 && git pull origin v6`
  - `git checkout -b release-v6`
  - `pnpm changeset pre enter pre-v6`
  - PR 和预发布迭代的流程保持不变
- 稳定版发布后：
  - 使用**普通合并**将 `release-v6` 合并回 `v6`
  - **不要**将 `release-v6` 合并到 `main`
  - 手动将新的根 `CHANGELOG.md` 条目复制到 `main` 和 `dev`
    - 我们不会为后续的 v6 发布将各个 `packages/*/CHANGELOG.md` 更新回移到 `main`
  - *代码*更改应该已经在 `dev` 分支中了
    - 这应该在 v6 更改时就已经完成（弃用警告等更改除外）
    - 确认此版本中的提交是否都已包含在 `dev` 中，如果没有，可以通过 cherry-pick 提交或重新进行工作来手动迁移

### 实验性发布

实验性发布和热修复不需要从 `dev` 创建分支。实验性发布可以从任何地方创建分支，因为它们不用于一般用途。

- 为发布创建新分支：`git checkout -b release-experimental`
- 进行所需的更改并提交：`git add . && git commit "experimental changes!"`
- 更新版本号并创建发布标签：`pnpm run version:experimental`
- 推送到 GitHub：`git push origin --follow-tags`
- CI 工作流应该会从实验性标签自动触发，将版本发布到 npm
