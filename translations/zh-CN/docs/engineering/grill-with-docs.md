## 它的作用

`grill-with-docs` 会围绕计划或设计访谈你，直到你和[代理](https://www.aihero.dev/ai-coding-dictionary/agent)对此有共同理解，同时将词汇和重要决定写入仓库。它与 [grill-me](https://aihero.dev/skills-grill-me) 使用相同的访谈方式（提出一轮问题、等待回答、再进入下一轮），但会针对代码库开展访谈。

它是**[有状态的](https://www.aihero.dev/ai-coding-dictionary/stateful)**。其他深入追问技能都只把[会话](https://www.aihero.dev/ai-coding-dictionary/session)留在你的记忆里；此技能会把文件留在磁盘上。术语得到确认时，它立即将其写入 `GLOSSARY.md`，而不是最后一次性写入。决定符合三项条件时，技能会将其写为 ADR。这就是全部区别，也是人们使用此技能时遇到的大多数问题的原因。产物是真实仓库中的真实文件，因此它们可能在你以为已经存在时仍然缺失，也可能在多人同时写入时发生分歧。

## 何时使用

输入 `/grill-with-docs` 来调用它；代理不会自行调用。

在仓库中开始一项改动时，如果计划仍然模糊、相关用语还没有确定，就使用它。它适用于单次会话。该选用哪种深入追问技能取决于当前情形：

| 当前情形 | 使用 |
| --- | --- |
| 你完全不在工作目录中 | [grill-me](https://aihero.dev/skills-grill-me) |
| 有仓库，且一次会话就能确定改动 | `grill-with-docs` |
| 工作过于庞大，无法在一次会话中处理（从零开始的构建、大型功能） | [wayfinder](https://aihero.dev/skills-wayfinder) |
| 仓库完全没有领域文档，也没有特定功能目标 | 针对仓库本身运行 `grill-with-docs`，而不是针对某项改动 |
| 决策受到其他人掌握的信息阻碍 | [to-questionnaire](https://aihero.dev/skills-to-questionnaire) |

选择 wayfinder 还是此技能，取决于所需会话数：`/grill-with-docs` 用于单次会话规划，`/wayfinder` 用于多次会话规划。

## 前置条件

技能会写入你的仓库，因此你需要位于一个可以安全写入的位置。确认的术语会写入根目录的 `GLOSSARY.md`；如果根目录的 `GLOSSARY-MAP.md` 表明仓库有多个上下文，则写入相关上下文的 `GLOSSARY.md`。决定会写入 `docs/adr/`。只有在需要时，技能才会创建这两个位置。第一个术语或决定尚未确定时，它们都不会存在，因此无需预先设置任何内容。

它还需要另外两项已安装的技能，因为它自己的 `SKILL.md` 只有一行，会将任务委派给它们。[grilling](https://aihero.dev/skills-grilling) 提供访谈流程，[domain-modeling](https://aihero.dev/skills-domain-modeling) 负责写入文档。单独安装 `grill-with-docs` 后，此技能无法正常工作。

## 记录内容

一次会话会产生三类内容，它们的重要性不同。

| 确认的内容 | 存放位置 |
| --- | --- |
| 术语：项目对某个概念的专用词汇 | `GLOSSARY.md`，在确认的当下写入 |
| 难以撤销、没有背景时会令人意外、确实存在取舍的决定 | `docs/adr/` 下的 ADR |
| 你决定的其他所有内容 | 只留在对话中，不写入其他位置 |

第三行最容易让人意外。`GLOSSARY.md` 只是术语表，不包含实现细节、[规格说明](https://www.aihero.dev/ai-coding-dictionary/spec)或草稿笔记。ADR 必须同时符合三项条件，因此大多数决定不符合条件，大多数会话也不会生成 ADR。会话使术语表更明确但没有生成 ADR，说明它按设计正常工作；但这也意味着大多数你们达成一致的内容只存在于当时的[上下文窗口](https://www.aihero.dev/ai-coding-dictionary/context-window)中。将同一段对话交给 [to-spec](https://aihero.dev/skills-to-spec)，不要清空它（参阅[清理上下文](https://www.aihero.dev/ai-coding-dictionary/clearing)）。

术语表是主要产物。此技能建立领域语言：项目已经达成一致的专用词汇，让你、代理和同事无需再次推敲它们。并非所有人都同意这能提升代理性能。最强烈的反对理由是：对于[模型](https://www.aihero.dev/ai-coding-dictionary/model)，术语和它的通俗英语解释会产生相同结果，而词汇主要缩短了共享这些术语的人类之间的沟通。按照这种观点，术语表仍然有价值，但受益者是人。

## 常见问题

**我应该使用此技能还是 `/wayfinder`？**
由范围决定。一次会话能够处理的内容使用此技能；规模过大、无法在一次会话中处理时，使用 [wayfinder](https://aihero.dev/skills-wayfinder)，它会先将工作绘制为决策[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)地图。Wayfinder 更慢、内容更密集；为范围明确的功能选择它是常见错误。它不会取代此技能，也可以针对地图中适合单次会话处理的部分开始深入追问。

**技能已经运行，但没有出现 `GLOSSARY.md` 和 ADR。**
已知原因有两个。第一个是没有符合条件的内容。ADR 必须通过三项审核，如果会话讨论的改动没有产生新词汇，就没有内容需要写入。第二个是真正的错误。当技能在其他编排层中运行时（规格驱动开发包装层、多代理框架，或将其作为其他流程步骤调用的规则），用户报告说写入文件的部分会悄无声息地停止工作，但访谈仍然会进行。该错误已经有人报告，但尚未修复。如果你使用这种设置，请在信任会话输出前检查工作目录。

**它一次问了所有问题，没有提供建议，也从未提到 `GLOSSARY.md`。**
这是因为技能没有加载两个依赖项。由于 `SKILL.md` 只有一行委派指令，如果代理没有加载 [grilling](https://aihero.dev/skills-grilling) 和 [domain-modeling](https://aihero.dev/skills-domain-modeling)，它就会猜测深入追问的含义，于是毫无结构地一次提出所有问题。只加载部分技能会更加令人困惑：加载了 `grilling`，却没有加载 `domain-modeling`，于是你会得到良好的访谈，却没有任何书面记录。发生频率取决于[模型](https://www.aihero.dev/ai-coding-dictionary/model)和[推理力度](https://www.aihero.dev/ai-coding-dictionary/effort)，这是此技能最常被报告的问题。如果你怀疑出现了此情况，直接询问代理加载了哪些技能。

**我的其他决定都去了哪里？**
只留在对话中。这是此技能最严重的未解决投诉。术语表不是规格说明，大多数答案不符合写入 ADR 的要求，而且没有记录将每个已确认答案关联到规格说明、工单和测试。后续步骤会将准确答案（排序保证、否定要求、数值默认设置）弱化为较笼统的文字，结果看起来完整，却遗漏了你决定的内容。暂时保留会话，并直接交给 [to-spec](https://aihero.dev/skills-to-spec)。然后根据自己的答案重新阅读规格说明，不要假设它已经完整记录了讨论内容。

**我可以针对完全没有文档的现有仓库使用它吗？**
可以。对于没有 ADR、领域语言或设计原则的代码库，这是合适的技能：调用它并说“帮我为仓库编写文档”。用户常会将它与 [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 配合使用，以建立或修复 `GLOSSARY.md`。需要准备好引导它：它会阅读代码并询问它从中发现的内容，由你决定代码库中已有的哪些词语才是合适的用词。

**会话结束后我应该做什么？**
技能的结束消息经常没有明确后续步骤，这是一个已知问题。主流程中的答案是在同一对话中使用 [to-spec](https://aihero.dev/skills-to-spec)。如果改动足够小，可以立即构建，就直接使用 [implement](https://aihero.dev/skills-implement)。

**为什么叫这个名字？**
没有人对这个名字满意。目前有一项未解决的更名建议，想改为更准确描述其行为的 `grill-domain-model`。目前没有任何进展。如果最终改名，文档页面也会随之迁移，URL 也会改变。

## 使用效果良好时

- `GLOSSARY.md` 会在会话**期间**逐个术语地更新，而不是最后一次性出现。
- 术语表中只有纯粹词汇（项目自己的用词和简洁定义），没有实现细节或规格说明式文字。
- 代码库本身能够回答的问题会通过阅读代码库得到回答，而不是转头询问你。
- 生成的 ADR 很少或没有；生成的 ADR 都是你不希望以后再次争论的决定。
- 如果现有术语表对你使用的词有不同定义，它会质疑该用词。

## 所在流程

`grill-with-docs` 是主要构建流程的起点：

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review → retro
```

它在规格说明写入前运行。它会先建立共同理解并确定词汇，然后由 [to-spec](https://aihero.dev/skills-to-spec) 综合讨论内容，且不再访谈你。与它最接近的技能是 [grill-me](https://aihero.dev/skills-grill-me)，访谈方式相同，但不需要仓库或写入文件；以及 [domain-modeling](https://aihero.dev/skills-domain-modeling)，由前者驱动的术语表与 ADR 规则。两者都使用 [grilling](https://aihero.dev/skills-grilling) 基本流程开展访谈。上游的 [wayfinder](https://aihero.dev/skills-wayfinder) 会绘制超出单次会话规模的工作地图，也可以将地图中的部分内容交回此技能。如果你不确定应该使用哪项技能或工作流程，[ask-matt](https://aihero.dev/skills-ask-matt) 会为你指路。
