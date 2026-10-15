## 它的作用

`setup-matt-pocock-skills` 会针对一个仓库回答三个问题：问题存放在哪里、分流标签叫什么，以及领域文档存放在哪里。它会将答案记录为 `docs/agents/` 下的 Markdown 文件。

这些文件是各仓库之间唯一会变化的内容。技能本身在任何仓库中都相同。运行时，它们会读取 `docs/agents/issue-tracker.md` 并按照其中说明执行。因此，这组技能不绑定 GitHub，你也不需要为了切换到其他 issue tracker 而编辑技能文件。你可以调用它并说"将技能连接到自定义 issue tracker"；只要能以编程方式连接，任何 issue tracker 都能使用，无需更改技能。

这是一项由提示驱动的技能，不是确定性脚本。它会读取你的 `git remote`、`CLAUDE.md` 和 `GLOSSARY.md`，说明发现的配置，并在写入之前等待你的确认。

## 何时使用

输入 `/setup-matt-pocock-skills` 来调用它；[代理](https://www.aihero.dev/ai-coding-dictionary/agent)不会自行调用。它的元数据会有意将其设为不可调用，因此其他技能不会替你启动它。

在每个仓库中运行一次，并且要在首次使用任何其他工程技能前运行。如果 [triage](https://aihero.dev/skills-triage)、[to-spec](https://aihero.dev/skills-to-spec)、[to-tickets](https://aihero.dev/skills-to-tickets) 或 [wayfinder](https://aihero.dev/skills-wayfinder) 开始猜测问题应该放在哪里，或应用 issue tracker 中不存在的标签，就说明该仓库尚未完成设置。即使仓库中的项目已进行到一半，你仍可运行此技能。技能会读取现有内容，因此不会丢失之前的工作。

## 前置条件

它会写入运行它的仓库：

| 写入内容 | 存放位置 |
| --- | --- |
| `issue-tracker.md` | `docs/agents/` |
| `domain.md` | `docs/agents/` |
| `triage-labels.md` | 只有安装了 `triage` 技能时才写入 `docs/agents/` |
| `## Agent skills` 区块 | 已存在的 `CLAUDE.md` 或 `AGENTS.md` |

你会将所有内容作为 Markdown 提交。它没有用户级或全局模式。配置存放在仓库中，因此每个仓库都有自己的配置副本。

## 三项决定

每个部分一开始都会先提出建议答案，并且跳过探索期间已经解决的问题。大多数运行只需要两次确认。

| 决定 | 建议内容 | 何时询问 |
| --- | --- | --- |
| **issue tracker** | 与你的 `git remote` 相符的 issue tracker | 总是询问，因为这是唯一真正需要选择的事项 |
| **分流标签** | 保留五个规范名称(`needs-triage`、`needs-info`、`ready-for-agent`、`ready-for-human`、`wontfix`) | 只有安装了 `triage` 技能时才询问 |
| **领域文档** | 单一上下文：在根目录放置一个 `GLOSSARY.md` 和一个 `docs/adr/` | 只有发现单体仓库信号时才询问，之后会提供多上下文的 `GLOSSARY-MAP.md` 选择 |

可选 issue tracker 如下：

| 选项 | 问题存放位置 | 所需工具 |
| --- | --- | --- |
| **GitHub** | 仓库的 GitHub Issues | `gh` CLI |
| **GitLab** | 仓库的 GitLab Issues | `glab` CLI |
| **本地 Markdown** | 本仓库 `.scratch/<feature>/` 下的文件 | 无需工具，甚至不需要远程仓库 |
| **其他** | 用户指定的位置 | 用户用一段话描述工作流程 |

前三种选项作为模板随技能提供，可以直接使用。本地 Markdown 是一种完整选项，不是后备选项。没有远程仓库的个人项目也受支持。一个限制是：如果你使用 GitHub，就不要选择本地 Markdown。它们是替代方案，只能选一种。

"其他"也是完整选项，可用于 Jira、Linear、Azure DevOps 和 Beads。你描述工作流程，技能将文字说明记录在 `docs/agents/issue-tracker.md`，下游技能再遵循说明。用户已经构建过多种实现：通过 [MCP](https://www.aihero.dev/ai-coding-dictionary/mcp) 连接 Jira、设计成 `gh` 命令形式的 Gitea CLI，以及手工构建的本地仪表板。

## 常见问题

**我必须使用 GitHub 吗？**

不必。GitHub、GitLab 和 `.scratch/` 下的本地 Markdown 都提供了现成模板；其他 issue tracker 则通过"其他"选项配置。这是被问得最多的问题，常见的问法包括："是否锁定使用 GitHub？"、"可以用 GitLab/Jira 吗？"、"Azure DevOps 怎么样？"答案始终相同：由 setup 选择 issue tracker，而不是技能本身。

**更新技能后，我需要重新运行设置吗？**

v1.1 发布后的直接答案是需要。但技能自身的结尾消息说法较为宽松：只有切换 issue tracker 或从头开始时才需要重新运行。两种说法都有道理。不同版本的初始模板会发生变化，因此旧版 `docs/agents/issue-tracker.md` 可能与现在会读取它的技能不再一致。如果下游技能的行为与文档描述不符，就重新运行设置。操作成本很低。

**它写入了 `CLAUDE.md`，但我使用 Codex。**

这是一个已知问题，而且仍未解决。文件选择规则是"如果存在 `CLAUDE.md`，就编辑它，否则编辑 `AGENTS.md`"。它会检查哪个文件存在，而不会检查当前正在运行的[harness](https://www.aihero.dev/ai-coding-dictionary/harness)。在遗留了 Claude Code 的 `CLAUDE.md` 的仓库中，技能会将 `## Agent skills` 区块写入 Codex 不会读取的文件。有两种变通方式：手动将该区块移动到 `AGENTS.md`，或者将 `AGENTS.md` 作为规范文件，并让 `CLAUDE.md` 仅指向它。如果两个文件都不存在，技能会询问你想创建哪个，而不是自行选择。这让原本期待它自行决定的人感到困惑。

**它没有创建我的分流标签。**

它不会。`docs/agents/triage-labels.md` 是一种*映射关系*：它告诉 `/triage`，你的 issue tracker 中哪些字符串对应五种规范角色。它不会运行 `gh label create`。全新的 GitHub 仓库中还不存在这些标签，因此用户已多次将此问题报告为错误。它带来两种结果：

- 如果你的 issue tracker 已经使用规范名称，该映射就是恒等表，无需配置任何内容。这是预期中最常见的情况，不是遗漏步骤。
- 此技能也不会创建 [wayfinder](https://aihero.dev/skills-wayfinder) 所需的 `wayfinder:map` 和 `wayfinder:<type>` 标签，而且 GitHub 上的 `gh issue create --label <missing>` 会失败，不会自动创建标签。在 GitHub 仓库首次运行 wayfinder 前，先手动创建这些标签。

**我可以在这里配置其他技能的行为吗([grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) 的节奏、问题形式、语气)？**

不可以。此技能配置三个事项：issue tracker、标签、文档布局。有人要求在这里设置针对用户的偏好。答案是：技能保持自身判断，不接受针对每个用户的配置。偏好应作为普通指示放入 `CLAUDE.md`，每项技能都会读取该文件。

**我可以将配置放在 `~/.claude`，而不是提交到每个仓库吗？**

目前不可以。跨多个仓库使用这些技能的一位用户为此提交了请求，但仍不存在用户级设置模式。每个仓库都有自己的 `docs/agents/`。

**用一个技能来配置其他技能，不是很奇怪吗？**

有一个长期未解决的抱怨认为确实很奇怪，原话是："需要一个技能来设置其他技能让我感觉不太合适：这意味着 LLM 正在配置自己的技能。"这种取舍确实存在。如果没有设置步骤，每个涉及问题的技能都需要复制一份 issue tracker 说明。此技能的输出是可以阅读和编辑的 Markdown 文件，因此限制了风险。你可以阅读并手动修改写入的所有文件。日常变更应采用这种方式，而不是再次运行技能。

## 使用效果良好时

- `docs/agents/issue-tracker.md` 和 `docs/agents/domain.md` 均已存在；如果安装了 `triage`，还要有 `triage-labels.md`。
- 运行环境读取的指示文件中出现了 `## Agent skills` 区块，每个文档都有一行说明和对应指针。
- 建议的 issue tracker 与你使用的远程仓库一致，标签字符串也与你的 issue tracker 中实际存在的标签一致。
- 设置后，`/to-tickets` 发布工单时不会再询问问题存放位置，`/triage` 会应用现有标签而不会发明新标签。
- 技能文件本身没有任何变更。如果设置过程修改了 `SKILL.md`，说明出了问题。

## 所在流程

`setup-matt-pocock-skills` 是工程流程中**只需运行一次的设置**，是其他所有步骤的前置条件，而不是流程中的一步。它的使用者就是它的邻近技能：[triage](https://aihero.dev/skills-triage) 使用此处指定的标签词汇；[to-spec](https://aihero.dev/skills-to-spec) 和 [to-tickets](https://aihero.dev/skills-to-tickets) 会将内容发布到此处指定的 issue tracker；[wayfinder](https://aihero.dev/skills-wayfinder) 会读取 issue tracker 文件中的"Wayfinding operations"章节，以了解如何存放地图和子[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)。之后，[domain-modeling](https://aihero.dev/skills-domain-modeling) 会填充 setup 记录的领域文档布局。只有在术语或决定得到确认时，它才会创建 `GLOSSARY.md` 和 ADR，因此设置后没有领域文档的仓库是正常的。如果你不确定接下来该使用哪项技能，[ask-matt](https://aihero.dev/skills-ask-matt) 会为你指路。
