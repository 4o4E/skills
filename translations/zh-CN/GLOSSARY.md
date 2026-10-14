# Matt Pocock 技能

由我日常使用的一组代理技能（斜线命令和行为），通过 Claude Code 加载。技能按类别组织，并由 `/setup-matt-pocock-skills` 生成的每个代码仓库配置使用。

## 术语

**议题跟踪系统**：
托管代码仓库议题的工具，例如 GitHub Issues、Linear、本地 `.scratch/` Markdown 约定，或类似工具。`to-tickets`、`to-spec` 和 `triage` 等技能会从中读取并写入内容。

**议题**：
议题跟踪系统中一项经过追踪的工作：缺陷、任务、规格说明，或由 `to-tickets` 生成的一项工作内容。
_避免使用_：工单（只在引用将其称为 ticket 的外部系统时使用；或指下文的**决策工单**）

**决策工单**：
`wayfinder` 中的一项内容：`wayfinder:map` 的子议题，内容是一个问题，其解决方式是作出决定，而不是执行一部分构建工作。**决策**这一限定词使其区别于实施工单；`wayfinder` 引入该术语后会使用“ticket”。

**分流角色**：
分流过程中赋给某个议题的规范状态机标签（例如 `needs-triage`、`ready-for-afk`）。每个角色都会通过 `docs/agents/triage-labels.md` 映射到议题跟踪系统中的实际标签字符串。

## 关系

- 一个**议题跟踪系统**包含多个**议题**。
- 一个**议题**在同一时间对应一个**分流角色**。
- **决策工单**也是**议题**（它是 `wayfinder:map` 的子议题）。

## 已指出的歧义

- 过去，“backlog”同时指托管议题的_工具_以及其中的_工作内容_。现已明确：该工具是**议题跟踪系统**；“backlog”不再作为领域术语使用。
- “backlog backend” / “backlog manager”。现已明确：合并为**议题跟踪系统**。
