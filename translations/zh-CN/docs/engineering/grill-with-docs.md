## 它的作用

`grill-with-docs` 会围绕计划或设计访谈你，直到你和[代理](https://www.aihero.dev/ai-coding-dictionary/agent)对此有共同理解，将领域词汇保存在术语表中，并通过项目已安装的官方 OpenSpec 工作流记录实际决定。它与 [grill-me](https://aihero.dev/skills-grill-me) 使用相同的访谈方式(提出一轮问题、等待回答、再进入下一轮)，但会针对代码库开展访谈。

它是**[有状态的](https://www.aihero.dev/ai-coding-dictionary/stateful)**。其他深入追问技能都只把[会话](https://www.aihero.dev/ai-coding-dictionary/session)留在你的记忆里；此技能会留下项目记录。术语得到确认时，它立即将其写入 `GLOSSARY.md`，而不是最后一次性写入。人工输入确定或改变决定时，项目已安装的官方 OpenSpec 工作流会记录实际确认来源和适用范围。技能不会另建平行格式或位置。如果 OpenSpec 不可用，决定记录会保持未完成，直到满足前置条件。

## 何时使用

输入 `/grill-with-docs` 来调用它；代理不会自行调用。

在仓库中开始一项改动时，如果计划仍然模糊、相关用语还没有确定，就使用它。它适用于单次会话。该选用哪种深入追问技能取决于当前情形：

| 当前情形 | 使用 |
| --- | --- |
| 你完全不在工作目录中 | [grill-me](https://aihero.dev/skills-grill-me) |
| 有仓库，且一次会话就能确定改动 | `grill-with-docs` |
| 工作过于庞大，无法在一次会话中处理(从零开始的构建、大型功能) | [wayfinder](https://aihero.dev/skills-wayfinder) |
| 仓库完全没有领域文档，也没有特定功能目标 | 针对仓库本身运行 `grill-with-docs`，而不是针对某项改动 |
| 决策受到其他人掌握的信息阻碍 | [to-questionnaire](https://aihero.dev/skills-to-questionnaire) |

选择 wayfinder 还是此技能，取决于所需会话数：`/grill-with-docs` 用于单次会话规划，`/wayfinder` 用于多次会话规划。

## 前置条件

技能会写入你的仓库，因此你需要位于一个可以安全写入的位置。确认的术语会写入根目录的 `GLOSSARY.md`；如果根目录的 `GLOSSARY-MAP.md` 表明仓库有多个上下文，则写入相关上下文的 `GLOSSARY.md`。决定记录使用目标项目已安装的官方 OpenSpec 工作流、现有配置和所需设置。OpenSpec 负责格式、位置、变更选择和生命周期。如果工作流尚未安装或项目需要初始化，技能会报告此前置条件，并说明决定记录尚未完成。

它还需要另外两项已安装的技能，因为它自己的 `SKILL.md` 只有一行，会将任务委派给它们。[grilling](https://aihero.dev/skills-grilling) 提供访谈流程，[domain-modeling](https://aihero.dev/skills-domain-modeling) 负责写入文档。单独安装 `grill-with-docs` 后，此技能无法正常工作。

## 记录内容

术语和决定有各自的存放位置。

| 确认的内容 | 存放位置 |
| --- | --- |
| 术语：项目对某个概念的专用词汇 | `GLOSSARY.md`，在确认的当下写入 |
| 人工输入确定或改变的实际决定 | 项目已安装的官方 OpenSpec 工作流，使用其已配置的产物 |

`GLOSSARY.md` 仍然只是术语表。它不包含实现细节、规格或临时笔记。OpenSpec 通过自己的官方工作流维护规格和决定产物。[to-spec](https://aihero.dev/skills-to-spec) 生成的 issue tracker 简报用于记录构建上下文，工作交付后可以舍弃；它不会取代项目的 OpenSpec 记录，也不会使这些记录失效。

术语表是主要产物。此技能建立领域语言：项目已经达成一致的专用词汇，让你、代理和同事无需再次推敲它们。并非所有人都同意这能提升代理性能。最强烈的反对理由是：对于[模型](https://www.aihero.dev/ai-coding-dictionary/model)，术语和通俗直白的自然语言解释会产生相同的结果，而专业词汇主要缩短了共享这些术语的人类之间的沟通成本。按照这种观点，术语表仍然有价值，但受益者是人。

## 常见问题

**我应该使用此技能还是 `/wayfinder`？**
由范围决定。一次会话能够处理的内容使用此技能；规模过大、无法在一次会话中处理时，使用 [wayfinder](https://aihero.dev/skills-wayfinder)，它会先将工作绘制为决策[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)地图。Wayfinder 更慢、内容更密集；为范围明确的功能选择它是常见错误。它不会取代此技能，也可以针对地图中适合单次会话处理的部分开始深入追问。

**技能已经运行，但没有出现术语表或决定记录。**
术语只有在确定新词时才会写入 `GLOSSARY.md`。决定则需要目标项目已安装并配置官方 OpenSpec；如果缺少此前置条件，技能会报告决定记录尚未完成。记录完成后，应检查工作目录并确认官方工作流生成的产物位置。

**它一次问了所有问题，没有提供建议，也从未提到 `GLOSSARY.md`。**
这是因为技能没有加载两个依赖项。由于 `SKILL.md` 只有一行委派指令，如果代理没有加载 [grilling](https://aihero.dev/skills-grilling) 和 [domain-modeling](https://aihero.dev/skills-domain-modeling)，它就会猜测深入追问的含义，于是毫无结构地一次提出所有问题。只加载部分技能会更加令人困惑：加载了 `grilling`，却没有加载 `domain-modeling`，于是你会得到良好的访谈，却没有任何书面记录。发生频率取决于[模型](https://www.aihero.dev/ai-coding-dictionary/model)和[推理力度](https://www.aihero.dev/ai-coding-dictionary/effort)，这是此技能最常被报告的问题。如果你怀疑出现了此情况，直接询问代理加载了哪些技能。

**issue tracker spec 会取代项目的 OpenSpec 规格吗？**
不会。[to-spec](https://aihero.dev/skills-to-spec) 生成的 issue tracker 简报会为实施保留构建上下文，工作交付后可以舍弃。项目的官方 OpenSpec 工作流继续负责规格和已确认的决定产物。向另一个会话交接工作时，请区分这两者的用途。

**我可以针对完全没有文档的现有仓库使用它吗？**
可以。对于没有决定记录、领域语言或设计原则的代码库，这是合适的技能：调用它并说"帮我为仓库编写文档"。用户常会将它与 [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 配合使用，以建立或修复 `GLOSSARY.md`。需要准备好引导它：它会阅读代码并询问它从中发现的内容，由你决定代码库中已有的哪些词语才是合适的用词。

**会话结束后我应该做什么？**
技能的结束消息经常没有明确后续步骤，这是一个已知问题。主流程中的答案是在同一对话中使用 [to-spec](https://aihero.dev/skills-to-spec)。如果改动足够小，可以立即构建，就直接使用 [implement](https://aihero.dev/skills-implement)。

**为什么叫这个名字？**
没有人对这个名字满意。目前有一项未解决的更名建议，想改为更准确描述其行为的 `grill-domain-model`。目前没有任何进展。如果最终改名，文档页面也会随之迁移，URL 也会改变。

## 使用效果良好时

- `GLOSSARY.md` 会在会话**期间**逐个术语地更新，而不是最后一次性出现。
- 术语表中只有纯粹词汇(项目自己的用词和简洁定义)，没有实现细节或规格说明式文字。
- 代码库本身能够回答的问题会通过阅读代码库得到回答，而不是转头询问你。
- 每项实际决定都通过已安装的 OpenSpec 工作流记录，并清楚标明确认来源和适用范围。
- 如果现有术语表对你使用的词有不同定义，它会质疑该用词。

## 所在流程

`grill-with-docs` 是主要构建流程的起点：

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review → retro
```

它在 issue tracker 简报写入前运行。它会先建立共同理解并确定词汇，然后由 [to-spec](https://aihero.dev/skills-to-spec) 综合讨论内容，且不再访谈你；交付后 issue 简报可以舍弃，项目 OpenSpec 规格和决定记录仍按官方生命周期维护。与它最接近的技能是 [grill-me](https://aihero.dev/skills-grill-me)，访谈方式相同，但不需要仓库或写入文件；以及 [domain-modeling](https://aihero.dev/skills-domain-modeling)，由前者驱动术语表维护和 OpenSpec 决定记录。两者都使用 [grilling](https://aihero.dev/skills-grilling) 基本流程开展访谈。上游的 [wayfinder](https://aihero.dev/skills-wayfinder) 会绘制超出单次会话规模的工作地图，也可以将地图中的部分内容交回此技能。如果你不确定应该使用哪项技能或工作流程，[ask-matt](https://aihero.dev/skills-ask-matt) 会为你指路。
