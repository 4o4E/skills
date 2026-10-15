---
name: setup-matt-pocock-skills
description: "为本仓库配置工程技能：设置议题跟踪系统、分流标签词汇和领域文档布局。在首次使用其他工程技能前运行一次。"
disable-model-invocation: true
---

# 设置 Matt Pocock 的技能

为每个仓库建立工程技能所依赖的配置：

- **议题跟踪系统**：问题所在位置(默认为 GitHub；同时原生支持本地 Markdown)
- **分流标签**：五个规范分流角色所使用的字符串
- **领域文档**：`GLOSSARY.md` 和 ADR 的存放位置，以及读取这些文件的使用规则

这是一项由提示驱动的技能，不是确定性脚本。先探索、说明发现的情况、向用户确认，然后再写入文件。

## 流程

### 1. 探索

查看当前仓库，了解它的初始状态。读取所有现有内容，不要预设：

- `git remote -v` 和 `.git/config`：这是 GitHub 仓库吗？是哪一个？
- 仓库根目录的 `AGENTS.md` 和 `CLAUDE.md`：其中是否有一个存在？其中是否已有 `## Agent skills` 章节？
- 仓库根目录的 `GLOSSARY.md` 和 `GLOSSARY-MAP.md`
- `docs/adr/` 以及任何 `src/*/docs/adr/` 目录
- `docs/agents/`：此技能以前生成的内容是否已存在？
- `.scratch/`：它可能表明已在使用本地 Markdown 问题跟踪约定
- 是否安装了 `triage` 技能？(本技能旁边是否有 `triage` 技能目录，或你的可用技能中是否有 `triage`。)它决定是否需要执行 B 部分。
- 单体仓库信号：`pnpm-workspace.yaml`、`package.json` 的 `workspaces` 字段，或其中包含各自 `src/` 的非空 `packages/*`。只有真正大型的多软件包仓库才会出现这些内容；它们不存在则表示单一上下文，几乎所有仓库都属于这种情况。

### 2. 展示发现并询问

总结哪些内容已经存在、哪些内容缺失。然后按顺序处理各部分：每次处理一部分，收到一个回答后再进入下一部分。

每部分都先提出建议的答案，让用户可以用一个词接受。只有选项确实带来不同分支时才用一句话说明；如果探索已经解决该部分，就完全跳过(没有安装 `triage` 时跳过 B 部分；没有单体仓库时跳过 C 部分)。

**A 部分：议题跟踪系统。**

> 说明：此处的"议题跟踪系统"是指本仓库中问题的存放位置。诸如 `to-tickets`、`triage` 和 `to-spec` 之类的技能需要从中读取和写入内容。它们需要知道应该运行 `gh issue create`、在 `.scratch/` 下编写 Markdown 文件，还是按照你描述的其他工作流程操作。选择你实际用来跟踪本仓库工作的地方。

默认建议：这些技能是为 GitHub 设计的。如果 `git remote` 指向 GitHub，就建议使用它。如果 `git remote` 指向 GitLab(`gitlab.com` 或自托管主机)，就建议使用 GitLab。否则(或者用户有其他偏好)，可以选择：

- **GitHub**：问题存放在本仓库的 GitHub Issues 中(使用 `gh` CLI)
- **GitLab**：问题存放在本仓库的 GitLab Issues 中(使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI)
- **本地 Markdown**：问题以文件形式存放在本仓库的 `.scratch/<feature>/` 下(适合个人项目或没有远程仓库的仓库)
- **其他**(Jira、Linear 等)：请用户用一段话描述工作流程；技能会将其记录为自由格式的说明

将选择记录到 `docs/agents/issue-tracker.md`。GitHub 和 GitLab 模板带有"将 PR 作为请求入口"标记，默认**关闭**。保持关闭，不要提出此选项；如果用户希望将外部 PR 纳入分流队列，可以之后在文件中修改标记。

**B 部分：分流标签词汇。**如果没有安装 `triage` 技能(探索时已经确认)，就完全跳过此部分，因为未安装的技能不需要标签。

如果已安装，只提出一个问题：

> 你是否要保留默认分流标签？(建议：**是**)

默认标签是五个规范角色，且各标签字符串与角色名称相同：`needs-triage`、`needs-info`、`ready-for-agent`、`ready-for-human`、`wontfix`。回答**是**时，原样写入。只有用户回答否时(通常因为其议题跟踪系统已经使用其他名称，例如用 `bug:triage` 表示 `needs-triage`)，才收集替代标签，让 `triage` 使用现有标签，而不是创建重复标签。

**C 部分：领域文档。**默认使用**单一上下文**(在仓库根目录放置一个 `GLOSSARY.md` 和一个 `docs/adr/`)。这适用于几乎所有仓库；直接写入，无需询问。

只有在探索时发现单体仓库信号，才提出使用**多上下文**(在根目录放置 `GLOSSARY-MAP.md` 并指向各个上下文的 `GLOSSARY.md` 文件)。接着确认用户想采用哪种布局。

### 3. 确认并编辑

向用户展示以下内容的草稿：

- 要添加到正在编辑的 `CLAUDE.md` 或 `AGENTS.md` 中的 `## Agent skills` 区块(文件选择规则见第 4 步)
- `docs/agents/issue-tracker.md`、`docs/agents/domain.md` 的内容；以及仅在安装了 `triage` 时提供的 `docs/agents/triage-labels.md`

写入之前让用户修改。

### 4. 写入

**选择要编辑的文件：**

- 如果存在 `CLAUDE.md`，就编辑它。
- 否则，如果存在 `AGENTS.md`，就编辑它。
- 如果两者都不存在，询问用户要创建哪一个；不要自行决定。

如果已经存在 `CLAUDE.md`，绝不要创建 `AGENTS.md`(反之亦然)；始终编辑已经存在的那个文件。

当选定文件中已经存在 `## Agent skills` 区块时，在原处更新其内容，不要附加重复区块。不要覆盖周围章节中用户所做的修改。

区块如下：

```markdown
## Agent skills

### Issue tracker

[用一行总结问题的跟踪位置]。参见 `docs/agents/issue-tracker.md`。

### Triage labels

[用一行总结标签词汇]。参见 `docs/agents/triage-labels.md`。

### Domain docs

[用一行总结布局："single-context"或"multi-context"]。参见 `docs/agents/domain.md`。
```

只有在安装了 `triage` 且已执行 B 部分时，才要包含 `### Triage labels` 子区块，并写入 `docs/agents/triage-labels.md`。未满足条件时，两者都省略。

在 GitHub 或 GitLab 上执行 B 部分时，为议题跟踪系统缺少的每个已配置标签创建标签(`gh label create` / `glab label create`)。

然后，以此技能目录中的模板为基础编写文档文件：

- [issue-tracker-github.md](./issue-tracker-github.md)：GitHub 议题跟踪系统
- [issue-tracker-gitlab.md](./issue-tracker-gitlab.md)：GitLab 议题跟踪系统
- [issue-tracker-local.md](./issue-tracker-local.md)：本地 Markdown 议题跟踪系统
- [triage-labels.md](./triage-labels.md)：标签映射(仅在安装了 `triage` 时使用)
- [domain.md](./domain.md)：领域文档使用规则和布局

对于"其他"议题跟踪系统，依据用户的描述从头编写 `docs/agents/issue-tracker.md`。

### 5. 完成

告知用户设置已完成，并说明哪些工程技能现在会读取这些文件。提及用户之后可以直接编辑 `docs/agents/*.md`；只有在他们要切换议题跟踪系统或从头重新配置时，才需要重新运行此技能。
