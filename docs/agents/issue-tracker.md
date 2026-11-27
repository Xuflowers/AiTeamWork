# 议题跟踪：GitHub

本仓库的 issue 与规格都存放在 GitHub Issues。所有操作使用 `gh` CLI。

## 约定

- **新建 issue**：`gh issue create --title "..." --body "..."`。多行正文用 heredoc。
- **读取 issue**：`gh issue view <number> --json number,title,body,labels,comments`。
- **列出 issue**：`gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'`，按需加 `--label` 与 `--state` 过滤。
- **子议题**：`gh issue create --parent <parent> ...`，或事后 `gh issue edit <parent> --add-sub-issue <child>`（`gh` 2.94+）。旧版 `gh`：`gh api --method POST repos/<owner>/<repo>/issues/<parent>/sub_issues -F sub_issue_id=<child-db-id>`（数据库 id，见下面「阻塞」）。不支持子议题时，在子 issue 正文顶部写 `Part of #<parent>`。
- **阻塞**：使用 GitHub 原生 issue 依赖，这是规范且界面可见的表示。加边：`gh issue create --blocked-by <n>,<n> ...` 或 `gh issue edit <child> --add-blocked-by <blocker>`（`gh` 2.94+）。旧版 `gh`：`gh api --method POST repos/<owner>/<repo>/issues/<child>/dependencies/blocked_by -F issue_id=<blocker-db-id>`，其中 `<blocker-db-id>` 是阻塞者的数字数据库 id（`gh api repos/<owner>/<repo>/issues/<n> --jq .id`，不是 `#number`，也不是 `node_id`）。依赖功能不可用时，回退为子 issue 正文顶部的 `Blocked by: #<n>, #<n>` 一行。所有阻塞项关闭后该工单才算可开工。
- **统计未关闭的阻塞项**：`gh api repos/<owner>/<repo>/issues/<n> --jq .issue_dependencies_summary.blocked_by`（只算未关闭项，0 表示可以开工）。`gh issue view <n> --json blockedBy` 也会列出已关闭的阻塞项，需要过滤：`--jq '[.blockedBy.nodes[] | select(.state == "OPEN")] | length'`。
- **评论**：`gh issue comment <number> --body "..."`。
- **加/去标签**：`gh issue edit <number> --add-label "..."` / `--remove-label "..."`。
- **关闭**：先评论说明原因，再 `gh issue close <number>`。对已关闭的 issue，`gh issue close --comment` 会丢掉评论但仍返回 0。

仓库从 `git remote -v` 推断；在 clone 内运行 `gh` 时会自动识别。

只往自己的仓库提交 issue。不要把 issue 提到 `mattpocock/skills`（这些技能来源的仓库），除非你拥有它。在 fork 里 `gh` 可能默认指向上游父仓库，所以每次会话第一次 `gh issue create` 前，先用 `gh repo view --json nameWithOwner` 确认目标仓库；如果不是用户自己的仓库，先跟用户确认。

## Pull Request 作为请求入口

**PR 作为请求入口：否。**（若设为 `yes`，`/triage` 会把 PR 也纳入队列。）

设为 `yes` 时，PR 与 issue 走同样的标签与状态，使用对应的 `gh pr` 命令：

- **读取 PR**：`gh pr view <number>`，再 `gh pr view <number> --comments`（非 TTY 下 `--comments` 只打印评论、不含正文），diff 用 `gh pr diff <number>`。
- **列出待 triage 的外部 PR**：`gh api --paginate 'repos/{owner}/{repo}/pulls?state=open' --jq '.[] | select(.author_association | IN("OWNER","MEMBER","COLLABORATOR") | not) | {number, title, author: .user.login, author_association, labels: [.labels[].name]}'`。
- **评论 / 加标签 / 关闭**：`gh pr comment`、`gh pr edit --add-label`/`--remove-label`、`gh pr close`。

GitHub 的 issue 和 PR 共用一套编号，裸 `#42` 可能是两者之一：先用 `gh pr view 42` 解析，失败再回退 `gh issue view 42`。

## 当技能说「发布到 issue tracker」

创建 GitHub issue。

## 当技能说「取相关工单」

按上面「读取 issue」处理。

## 寻路操作（Wayfinding）

供 `/wayfinder` 使用。**地图**是一个 issue，**子 issue** 是 ticket。

- **地图**：单个 issue，打 `wayfinder:map` 标签，正文放 Notes / Decisions-so-far / Fog。`gh issue create --label wayfinder:map`。
- **子 ticket**：通过 GitHub 子议题挂到地图下（见上面「子议题」）。子议题不可用时，把子 ticket 加进地图正文的任务列表，并在子 issue 正文顶部写 `Part of #<map>`。标签：`wayfinder:<type>`（`research`/`prototype`/`grilling`/`task`）。认领后分配给对应开发者。
- **阻塞**：见「约定」里的「阻塞」。
- **前沿查询**：列出地图未关闭的子 issue（`gh issue list --state open`，限定到地图的子议题/任务列表），剔除有未关闭阻塞项（见「统计未关闭的阻塞项」，或正文里的 `Blocked by` 行）或有 assignee 的；按地图顺序取第一个。
- **认领**：`gh issue edit <n> --add-assignee @me`，这是本会话的第一次写操作。
- **解决**：`gh issue comment <n> --body "<answer>"`，再 `gh issue close <n>`，最后把上下文指针（摘要 + 链接）追加到地图的 Decisions-so-far。

本文档只描述仓库约定，不涉及具体机器的 gh 安装路径、认证方式或网络配置。若 gh 不在 PATH 或需要代理，请自行在本机处理，不要写进仓库文档。
