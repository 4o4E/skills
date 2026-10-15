# Matt Pocock 技能

这套智能体技能包含日常使用的各类命令与行为规范，通过 Claude Code 加载运行。所有技能按类别划分，并通过 `/setup-matt-pocock-skills` 为代码仓库完成专属配置。

## 术语

**issue tracker**：
托管代码仓库 issue 的工具，例如 GitHub Issues、Linear、本地 `.scratch/` Markdown 约定，或类似工具。`to-tickets`、`to-spec` 和 `triage` 等技能会从中读取并写入内容。
_避免使用_：backlog manager, backlog backend, issue host

**issue**：
issue tracker 中一项经过追踪的工作：缺陷、任务、规格说明，或由 `to-tickets` 生成的一项工作内容。
_避免使用_：工单(只在引用将其称为 ticket 的外部系统时使用；或指下文的**决策工单**)

**决策工单 (Decision ticket)**：
`wayfinder` 中的一项内容：`wayfinder:map` 的子 issue，内容是一个问题，其解决方式是作出决定，而不是执行一部分构建工作。**决策**这一限定词使其区别于实施工单；`wayfinder` 引入该术语后会使用"ticket"。

**分流角色 (Triage role)**：
分流过程中赋给某个 issue 的规范状态机标签(例如 `needs-triage`、`ready-for-afk`)。每个角色都会通过 `docs/agents/triage-labels.md` 映射到 issue tracker 中的实际标签字符串。

## 关系

- 一个 **issue tracker** 包含多个 **issue**。
- 一个 **issue** 在同一时间对应一个**分流角色**。
- **决策工单**也是 **issue**(它是 `wayfinder:map` 的子 issue)。

## 已指出的歧义

- 过去，"backlog"同时指托管 issue 的_工具_以及其中的_工作内容_。现已明确：该工具是 **issue tracker**；"backlog"不再作为领域术语使用。
- "backlog backend" / "backlog manager"。现已明确：合并为 **issue tracker**。
