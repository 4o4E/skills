# issue tracker：GitHub

本仓库的 issue 和规格说明存放在 GitHub issue 中。所有操作都使用 `gh` CLI。

## 约定

- **创建 issue**：`gh issue create --title "..." --body "..."`。多行正文使用 heredoc。
- **读取 issue**：`gh issue view <number> --json number,title,body,labels,comments`。
- **列出 issue**：使用 `gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'`，并传入适当的 `--label` 和 `--state` 筛选参数。
- **将 issue 设为另一个 issue 的子 issue**：使用 `gh issue create --parent <parent> ...`，或之后使用 `gh issue edit <parent> --add-sub-issue <child>`(`gh` 2.94+)。较旧版本的 `gh`：`gh api --method POST repos/<owner>/<repo>/issues/<parent>/sub_issues -F sub_issue_id=<child-db-id>`(数据库 id，见下文的**阻塞关系**)。如果不支持子 issue，在子 issue 正文顶部写入 `Part of #<parent>`。
- **评论 issue**：`gh issue comment <number> --body "..."`
- **添加或移除标签**：`gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **关闭 issue**：`gh issue close <number> --comment "..."`

根据 `git remote -v` 推断仓库；在克隆目录中运行时，`gh` 会自动推断。

## 将 PR 作为分流入口

**将 PR 作为请求入口：no。**_(如果本仓库将外部 PR 视作功能请求，请设为 `yes`；`/triage` 会读取此标记。)_

设为 `yes` 时，PR 使用与问题相同的标签和状态，并使用对应的 `gh pr` 命令：

- **读取 PR**：使用 `gh pr view <number> --comments` 和 `gh pr diff <number>` 查看差异。
- **列出待分流的外部 PR**：`gh api --paginate 'repos/{owner}/{repo}/pulls?state=open' --jq '.[] | select(.author_association | IN("OWNER","MEMBER","COLLABORATOR") | not) | {number, title, author: .user.login, author_association, labels: [.labels[].name]}'`。
- **评论、添加标签或关闭**：使用 `gh pr comment`、`gh pr edit --add-label`/`--remove-label`、`gh pr close`。

GitHub 的 issue 和 PR 共用一个编号空间，因此单独的 `#42` 可能指其中任意一种：先使用 `gh pr view 42` 查询，如果失败，再使用 `gh issue view 42`。

## 技能提到"发布到 issue tracker"时

创建一个 GitHub issue。

## 技能提到"获取相关工单"时

按**读取问题**中的方式读取。

## Wayfinding 操作

由 `/wayfinder` 使用。**地图**是一个 issue，其**子 issue**是工单。

- **地图**：单个标记为 `wayfinder:map` 的 issue，其中包含 Notes / Decisions-so-far / Fog 正文。使用 `gh issue create --label wayfinder:map`。
- **子工单**：关联到地图的 GitHub 子 issue(见**将 issue 设为另一个 issue 的子 issue**)。如果未启用子 issue，在地图正文中的任务列表加入该子工单，并在其正文顶部写入 `Part of #<map>`。标签：`wayfinder:<type>`(`research`/`prototype`/`grilling`/`task`)。认领后，将工单分配给负责推进的开发人员。
- **阻塞关系**：使用 GitHub **原生 issue 依赖关系**，这是规范且可在界面中查看的表示方式。使用 `gh api --method POST repos/<owner>/<repo>/issues/<child>/dependencies/blocked_by -F issue_id=<blocker-db-id>` 添加依赖，其中 `<blocker-db-id>` 是阻塞工单的数字**数据库 id**(使用 `gh api repos/<owner>/<repo>/issues/<n> --jq .id` 获取，_不是_ `#number` 或 `node_id`)。GitHub 会报告 `issue_dependencies_summary.blocked_by`(仅包含尚未关闭的阻塞项，这是当前有效的限制)。如果无法使用依赖关系，则退回到在子工单正文顶部写入 `Blocked by: #<n>, #<n>`。所有阻塞项都关闭后，工单才不再受阻。
- **前沿查询**：列出地图尚未关闭的子工单(使用 `gh issue list --state open`，范围限定为地图的子 issue 或任务列表)，移除任何存在未关闭阻塞项(`issue_dependencies_summary.blocked_by > 0`，或 `Blocked by` 行中有尚未关闭的 issue)或已分配负责人的工单；按地图中的顺序选择第一个。
- **认领**：使用 `gh issue edit <n> --add-assignee @me`，这是会话中的第一次写入操作。
- **解决**：使用 `gh issue comment <n> --body "<answer>"`，然后使用 `gh issue close <n>`，最后在地图的 Decisions-so-far 中追加上下文指针(简述 + 链接)。
