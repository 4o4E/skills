## 它的作用

`ask-matt` 是本仓库技能的路由器。你描述所处的情形（有一个无从着手的想法、一堆新收到的错误报告、已经持续很久的[会话](https://www.aihero.dev/ai-coding-dictionary/session)），它会指出适合的技能或技能顺序，以及这段流程中哪些地方需要人作决定。

它只提出建议，然后停止。它不会深入追问、编写[规格说明](https://www.aihero.dev/ai-coding-dictionary/spec)、打开文件，也不会运行刚才提到的技能；它给你的只是下一步应该输入的内容，由你亲自输入。它也是一张由人手动维护的本仓库技能地图，而不是对已安装技能进行扫描，因此不会为你引导到自有技能或其他作者的技能。

## 何时使用

输入 `/ask-matt` 来调用它；代理不会自行调用。

| 你的情形 | 路由器会告诉你什么 |
| --- | --- |
| 有一个想法，却不知道从哪里开始 | 主流程的起点，以及构建规模是否足够小，可以跳过规格说明 |
| 收到其他人的错误报告和请求 | [triage](https://aihero.dev/skills-triage) 入口，以及为什么你自己生成的[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)不属于此入口 |
| 两项技能看起来可以互换 | 它们之间的区别，通常由一个具体检查决定，而不是个人偏好。[grill-me](https://aihero.dev/skills-grill-me) 还是 [grill-with-docs](https://aihero.dev/skills-grill-with-docs)，取决于你是否在工作目录中；[grill-with-docs](https://aihero.dev/skills-grill-with-docs) 还是 [wayfinder](https://aihero.dev/skills-wayfinder)，取决于工作是否适合一次会话 |
| 会话持续很久，需要决定如何处理[上下文](https://www.aihero.dev/ai-coding-dictionary/context) | 阶段边界处五种选择的有序决策路径 |
| 已经选好了技能 | 无有用建议。直接调用那项技能。 |

## 前置条件

路由器会提到技能，但不会安装技能。它所指向的每项技能都必须已经安装，建议才可执行，而且它只了解本仓库中已正式发布的技能。

依赖议题跟踪系统的路径（triage、`to-spec`、`to-tickets`、`implement`）都假定本仓库已通过 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 配置议题跟踪系统。即使还没有完成设置，路由器仍然会建议使用这些技能。

## 工作流程，而不是技能清单

技能描述的首要概念是**工作流程**，即一条*穿过*多项技能的路径，而不是单独一项技能。当你说明自己的情形时，路由器会将你放在某条工作流程的某个步骤上。这不同于“以下是与你的关键词匹配的技能”。有五类路径，技能正文会详细说明：

- **主流程**：从想法到发布。深入追问、规格说明、工单、实现（一次处理一张工单，或与 [implement-spec](https://aihero.dev/skills-implement-spec) 并行处理整个任务图）、审查，然后使用 [retro](https://aihero.dev/skills-retro) 将构建过程中的经验反馈到代理运行环境中。它有两个分支，一个是原型旁支，适用于需要可运行代码才能解决的问题；另一个是规格说明与工单拆分，只适用于跨越多个会话的构建工作。
- **工作入口**：适用于产生工作后再汇入主流程的情形：收到错误报告、出现故障，或工作规模太大、太模糊，无法在一次会话中处理。
- **代码库维护**：关注维护，不是功能开发。[improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 会检查代码以寻找加深模块的机会；每个找到的机会都能作为新想法重新进入主流程。
- **独立技能**：脱离所有流程单独使用，包括原型、问卷和研究。
- **底层词汇层**：其他技能在问题出在用词而不是流程时调用的两项参考技能。

## 阶段边界

此技能的另一个核心概念是**阶段边界**。阶段是会话中的一段工作（[深入追问](https://www.aihero.dev/ai-coding-dictionary/grilling)、实现、质量保证），只有在两个阶段的边界处，才需要考虑“如何处理当前上下文？”阶段进行中无需作出决定：继续工作，或将剩余工作拆分给[子代理](https://www.aihero.dev/ai-coding-dictionary/subagent)。

| 选择 | 适用情况 |
| --- | --- |
| **Continue** | 下一阶段需要逐字使用当前内容，或你仍处于[智能区（smart zone）](https://www.aihero.dev/ai-coding-dictionary/smart-zone)。这是唯一能将会话保留为[一手资料](https://www.aihero.dev/ai-coding-dictionary/primary-source)的方式，因此先判断能否 Continue（继续）；不能继续时再考虑其他方式 |
| **`/clear`** | 当前会话中所有内容都可以丢弃。这是成本最低的选择；如果判断错误，就无法撤销 |
| **[handoff](https://aihero.dev/skills-handoff)** | 需要带走一些内容：切换到新的[运行环境](https://www.aihero.dev/ai-coding-dictionary/harness)、新目录、同事，或在阶段中途拆分的旁支任务 |
| **Subagent** | 任务范围足够明确，可以让代理在你[离开键盘](https://www.aihero.dev/ai-coding-dictionary/afk)时自行完成 |
| **`/compact`** | 以上选项都不适用。它是默认选项，而且通常会成为判断路径的最后一步 |

人们经常把其中两个选项弄错，因此路由器要告诉你先后顺序，而不只列出选项。`/handoff` 看起来像是在上下文窗口之间切换的一般方式，但事实并非如此。它只提供可移植性。`/compact` 位于判断路径末尾而不是开头，因为前四项都更便宜或更精确。

## 常见问题

**README 中不能直接放一个按正确顺序排列的技能清单吗？**

很多人一直在 README 中要求这样的清单。此技能就是那份清单。固定表格只能写 `wayfinder → to-spec → to-tickets → implement → code-review → retro`，但大多数情形并不适用。真正重要的是分支：是否有代码库？构建是否跨越多个会话？能否通过交谈解决问题？缺点是路由器需要手动维护，因此会落后于仓库。`/grilling` 很早以前就已发布，但路由器在之后才提到它。

**它说一半技能都没有安装。**

这是一个已知问题，而且尚未修复。路由器指向的大多数技能都设置了 `disable-model-invocation: true`，因此运行环境不会将它们放入注入代理上下文的技能清单中。代理会把清单当成完整列表，报告说这些技能缺失。某次被报告的会话中，它声称整个规格说明与工单流程都不存在，于是改为只用 `/grilling` 和 `/tdd`。该插件的二十七项技能中有十六项带有此标记，因此这是常见情况，而不是边缘情况。它们已经安装。仍可输入斜线命令，也可以检查 `.claude-plugin/plugin.json`；该文件是确认技能是否存在的权威来源。

**它描述了某项技能的行为，但那项技能并非如此。**

这也是一个真实的问题，而且尚未修复。路由器会根据每项技能自己的一行摘要回答，而不是阅读技能正文。一份详细报告记录了同一会话中的三次情况，其中包括只根据“将讨论整理成规格说明”这行摘要就建议跳过 [to-spec](https://aihero.dev/skills-to-spec)。路由器从未打开 `to-spec/SKILL.md`。每一次都是在用户提出质疑之后才检查，并非主动检查。那次跳过 `to-spec` 导致没有检查真正的接缝，而且生成的工单少于实际所需数量。对于你即将据以行动的其他技能，要求路由器先打开相应的 `SKILL.md`。对于地图完全没有涵盖的问题，例如是否使用[计划模式](https://www.aihero.dev/ai-coding-dictionary/agent-mode)，也应采取同样做法：答案来自[模型](https://www.aihero.dev/ai-coding-dictionary/model)的推断，而不是文档中已有的内容。

**为什么使用段落，而不是编号检查清单？**

这是合理的批评。有一个未关闭的问题认为，大多数路由选择都是确定性的，而叙述形式不易浏览。你可以要求它提供压缩形式：“只给我操作顺序”就会得到操作顺序。段落用于承载条件信息：不同分支、预期人作出决定的地方，以及每个步骤之间应该清空或压缩的位置。扁平检查清单正好会遗漏这些内容。

**它可以把我的技能或其他作者的技能纳入路由吗？**

不可以。已经有三项提案要求路由器读取本地 `skills/` 目录，并根据已安装技能进行推荐。`ask-matt` 不是这样的工具。它是一个由人维护的固定技能集合地图，对你编写的技能或从其他地方安装的技能一无所知。

**它让我编辑 SKILL.md。**

这种建议通常正确，但改动很少能保留下来。有人问它如何让 [implement](https://aihero.dev/skills-implement) 关闭工单；它让对方在技能中添加一行，对方马上发现了问题：`npx skills update` 会覆盖该文件，而插件安装目录是只读的。应将长期行为写入自己的 `CLAUDE.md` 或 `AGENTS.md`，或在调用技能时直接说明。在提示中作出的修改不会被更新覆盖。人们也会用这种方式让流程使用 Linear 而不是 GitHub，或询问哪些开放工单可以并行处理。

**它提到我没有的技能，或漏掉我有的技能。**

假设技能已移除前，先检查更新日志中是否记载了更名。`writing-great-skills` 更名为 [writing-for-agents](https://aihero.dev/skills-writing-for-agents)，没有别名；`to-prd` 更名为 [to-spec](https://aihero.dev/skills-to-spec)；`pathfinder` 更名为 [wayfinder](https://aihero.dev/skills-wayfinder)。有四项技能已彻底退役，功能并入其他技能：`ubiquitous-language`、`design-an-interface`、`qa` 和 `request-refactor-plan`。如果路由器漏掉了你已有的技能，这是路由器本身维护滞后的问题，前面已经说明。

## 使用效果良好时

- 它以说明下一步要输入什么并停止作为结尾，而不是自行开始工作。
- 它提供的路径会说明何时清空或压缩上下文、何时需要你审查，而不仅仅是列出技能名称。
- 两项技能相近时，它会说明该选择哪一项以及另一项为何不适合。
- 它对其他技能行为的任何说明，都能在操作记录中看到它读取了该技能的 `SKILL.md`。
- 它给出的内容能让你认出自身情形，而不是套用最相近的通用情形。

## 所在流程

`ask-matt` 是一项覆盖完整技能集合的**独立路由器**，永远不会成为某条流程中的一步。它会指向各条流程，其他文档页面也会链接回它，因此不必各自重新绘制整个图。从这里最常见的下一步是主流程起点 [grill-with-docs](https://aihero.dev/skills-grill-with-docs)，或用于处理他人提交工作问题的入口 [triage](https://aihero.dev/skills-triage)。

它是所描述技能的[二手资料](https://www.aihero.dev/ai-coding-dictionary/secondary-source)。路由器与 `SKILL.md` 发生冲突时，以 `SKILL.md` 为准。
