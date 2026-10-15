# 议题跟踪系统：GitLab

本仓库的议题和规格说明存放在 GitLab 议题中。所有操作都使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI。

## 约定

- **创建议题**：`glab issue create --title "..." --description "..."`。多行描述使用 heredoc。传入 `--description -` 可打开编辑器。
- **读取议题**：`glab issue view <number> --comments`。使用 `-F json` 获取机器可读输出。
- **列出议题**：使用 `glab issue list -O json`，并传入适当的 `--label` 筛选参数。
- **评论议题**：`glab issue note <number> --message "..."`。GitLab 将评论称为 notes。
- **添加或移除标签**：`glab issue update <number> --label "..."` / `--unlabel "..."`。可以用逗号分隔多个标签，也可以重复使用该参数。
- **关闭议题**：`glab issue close <number>`。`glab issue close` 不接受关闭评论，因此先使用 `glab issue note <number> --message "..."` 发布说明，再关闭议题。
- **合并请求**：GitLab 将 PR 称为 merge requests。使用 `glab mr create`、`glab mr view`、`glab mr note` 等命令，命令格式与 `gh pr ...` 相同，只是将 `pr` 替换为 `mr`，将 `comment`/`--body` 替换为 `note`/`--message`。

根据 `git remote -v` 推断仓库；在克隆目录中运行时，`glab` 会自动推断。

## 将合并请求作为分流入口

**将 MR 作为请求入口：no。**_(如果本仓库将外部合并请求视作功能请求，请设为 `yes`；`/triage` 会读取此标记。)_

设为 `yes` 时，MR 使用与问题相同的标签和状态，并使用对应的 `glab mr` 命令：

- **读取 MR**：使用 `glab mr view <number> --comments` 和 `glab mr diff <number>` 查看差异。
- **列出待分流的外部 MR**：使用 `glab mr list -F json`，然后只保留作者不是项目成员或负责人的 MR(贡献者提交的 MR，而非维护人员正在处理的工作)。
- **评论、添加标签或关闭**：使用 `glab mr note`、`glab mr update --label`/`--unlabel`、`glab mr close`。

与 GitHub 不同，GitLab 为议题和 MR 分别编号，因此只要知道维护者指的是哪种对象，`#42` 就没有歧义。

## 技能提到"发布到议题跟踪系统"时

创建一个 GitLab 议题。

## 技能提到"获取相关工单"时

运行 `glab issue view <number> --comments`。

## Wayfinding 操作

由 `/wayfinder` 使用。**地图**是单个议题，其**子议题**是工单。

- **地图**：单个标记为 `wayfinder:map` 的议题，其中包含 Notes / Decisions-so-far / Fog 正文。使用 `glab issue create --label wayfinder:map`。(在支持原生 epic 的 GitLab 版本中，也可以用 epic 保存地图；带标签的议题在所有版本中都可用。)
- **子工单**：在描述顶部写入 `Part of #<map>`，并使用 `wayfinder:<type>`(`research`/`prototype`/`grilling`/`task`)标签的议题。认领后，将工单分配给负责推进的开发人员。
- **阻塞关系**：使用 GitLab **原生阻塞链接**，这是规范且可在界面中查看的表示方式。使用 `/blocked_by #<n>` 快捷操作，并通过 `glab issue note <child> --message "/blocked_by #<blocker>"` 发布 note 来添加关系。原生阻塞链接需要 Premium/Ultimate 套餐；在免费套餐中(或功能不可用时)，退回到在描述顶部写入 `Blocked by: #<n>, #<n>`。所有阻塞项都关闭后，工单才不再受阻。
- **前沿查询**：使用 `glab issue list -O json`，范围限定为地图的子工单，移除任何存在尚未关闭阻塞项的工单：与尚未关闭议题相连的原生 `blocked_by` 链接(`glab api projects/:id/issues/<child-iid>/links`)、`Blocked by` 行中尚未关闭的议题，或已分配负责人的工单；按地图中的顺序选择第一个。
- **认领**：使用 `glab issue update <n> --assignee @me`，这是会话中的第一次写入操作。
- **解决**：使用 `glab issue note <n> --message "<answer>"`，然后使用 `glab issue close <n>`，最后在地图的 Decisions-so-far 中追加上下文指针(简述 + 链接)。
