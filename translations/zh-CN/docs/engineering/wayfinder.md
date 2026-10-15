## 它的作用

`wayfinder` 用于处理规模超出单个代理[会话](https://www.aihero.dev/ai-coding-dictionary/session)承载能力的工作。你已经能说清工作的**目的地**，却还看不到通往目的地的路线。Wayfinder 会在议题跟踪系统上将工作绘制为由**决策工单**组成的共享**地图**，然后逐张解决工单，直到路线清晰。

它负责规划，不负责构建。每张工单都提出一个问题，答案是一个决定，而不是构建工作的一部分。地图完成时，开始构建前不再有任何决定需要作出。这条规则将 wayfinder 工单与普通实现[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)区分开来，也是代理最常违反的规则。地图清晰后，wayfinder 会移交工作，不会继续编写代码。

## 何时使用

输入 `/wayfinder` 来调用它；[代理](https://www.aihero.dev/ai-coding-dictionary/agent)不会自行调用。

它是整套技能中流程最繁重的一项，因此适用条件很窄。工作规模必须超出单个代理会话的承载范围，通往目的地的路径也必须不清晰。选择依据是会话数量：`/grill-with-docs` 用于单次会话规划，`/wayfinder` 用于多次会话规划。

| 当前情形 | 使用方式 |
| --- | --- |
| 范围明确、一次就能确定的功能 | [grill-me](https://aihero.dev/skills-grill-me)，如果有代码库则使用 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) |
| 从零开始的项目，或跨越多个会话的构建，且路线尚不清晰 | `/wayfinder` |
| 已经完成决定的对话 | 使用 [to-spec](https://aihero.dev/skills-to-spec)，跳过地图步骤 |
| 已清晰的 wayfinder 地图 | 使用 [to-spec](https://aihero.dev/skills-to-spec)，然后使用 [to-tickets](https://aihero.dev/skills-to-tickets) 和 [implement](https://aihero.dev/skills-implement) |
| 已经变得过于庞大的现有会话 | 说"移交给 `/wayfinder`"([handoff](https://aihero.dev/skills-handoff) 可以将工作带入地图，也可以从地图带出) |

从零开始并非使用条件。人们经常在旧代码库和尚未完成的代码库中使用 wayfinder，这种情形下它可能更有用，因为大部分迷雾来自"这里已经有什么事实"，而不是"我们应该做什么"。

## 前置条件

地图和其中的工单存放在仓库的议题跟踪系统中，因此 wayfinder 需要通过 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 设置议题跟踪系统。该步骤会编写一个"Wayfinding operations"章节，说明如何在 GitHub、GitLab 或本地 Markdown 中表达地图、子工单、阻塞关系和前沿查询。Wayfinder 通过 `CLAUDE.md`/`AGENTS.md` 中的指针找到该文档，不会在固定路径查找。没有配置议题跟踪系统时，它会退回到本地 Markdown 文件。

议题跟踪系统确实会参与工作。它的原生阻塞链接会在议题跟踪系统自己的界面中显示前沿。若议题跟踪系统没有原生依赖链接(例如自托管的 Gitea)，wayfinder 就会根据地图文字推断阻塞关系。这种方式可用，但需要你更加密切地监督。

## 地图、迷雾和前沿

**地图**是一个标记为 `wayfinder:map` 的议题，其工单是它的子议题。它是一个**索引，而不是内容存储处**。每个决定只存在于一张工单中，地图只提供一行摘要和链接。会话以简略形式加载地图，需要时再打开个别工单。这让地图能够持续扩展，而不用每个会话都加载它的完整历史。

地图有四个章节：

- **Destination。** 地图终点的具体表现。它必须在任何工单建立前确定，因为目的地决定了用来衡量每张工单的范围。
- **Decisions so far。** 每张已关闭工单各占一行，并附有指向详细内容的链接。
- **Not yet specified。** 这是**战争迷雾 (Fog of war)**：你能判断某些决定即将出现，但还无法准确描述。判断属于迷雾还是工单，依据是你现在能否精确表达问题，*而不是*你现在能否回答它。解决某张工单时，它前方的迷雾会散开，任何现在可以明确描述的内容都会晋升为新工单。
- **Out of scope。** 超出目的地范围的工作。迷雾只会朝着目的地汇聚，因此范围外的工作会保持关闭，绝不会晋升为工单。

**前沿 (Frontier)**是一组开放、无阻塞、未认领的工单(即已知范围的边界)。会话在工作开始前将工单分配给自己，以此认领工单。被分配者*就是*认领者，因此并发会话会跳过该工单。会话通过名称引用工单，绝不只写 `#42`，因为叙述中连续出现许多议题编号难以阅读。

## 四种决策工单类型

每张工单都带有一个 `wayfinder:<type>` 标签。每张工单属于**[HITL](https://www.aihero.dev/ai-coding-dictionary/human-in-the-loop)**(与能代表自己表达意见的人共同处理)或 **[AFK](https://www.aihero.dev/ai-coding-dictionary/afk)**(完全由代理推进)。HITL 工单只能通过当场互动解决。如果代理自行回答自己的[深入追问](https://www.aihero.dev/ai-coding-dictionary/grilling)问题，就没有完成任务。

| 类型 | 模式 | 适用情况 | 通过何种方式解决 |
| --- | --- | --- | --- |
| `grilling` | HITL | 默认类型。可以通过讨论解决问题。 | 在新会话中使用 [grilling](https://aihero.dev/skills-grilling) 和 [domain-modeling](https://aihero.dev/skills-domain-modeling) |
| `prototype` | HITL | "应该是什么样子"或"应该如何运作"：无法通过交谈解决的问题。 | 使用 [prototype](https://aihero.dev/skills-prototype)，并在工单中链接构建出来的成果 |
| `research` | AFK | 工作目录之外的事实阻碍了某项决定。 | 绘制地图时启动一个 [research](https://aihero.dev/skills-research) [子代理](https://www.aihero.dev/ai-coding-dictionary/subagent)，在 `research/<name>` 分支上并行运行 |
| `task` | 任一模式 | 没有待决定的内容，但手动工作阻碍了某项决定，例如配置访问权限、注册服务或迁移数据以便查看其形态。 | 能由代理完成时由代理完成，否则为人提供准确的检查清单 |

`task` 是唯一一种会执行工作而不是作出决定的类型。只有在解除某项决定的阻碍时，它才属于地图；它绝不是为了交付目的地的一部分。实际使用中最常发生的错误就是误用此类型。代理会把它看成实现步骤，并开始在地图中编写产品代码。

只有研究工单可以例外地在每个会话中解决超过一张工单。

## 常见问题

**这与 `/grill-with-docs` 有什么区别？我应该从哪个开始？**
由会话数量决定，而不是由项目规模决定。`/grill-with-docs` 用于单次会话规划；wayfinder 用于多次会话规划。如果整个工作能在一次对话中掌握，深入追问成本更低、效果更好；对于这种情况，wayfinder 更慢、内容也更密集。社区的简略说法是：只有工作无法在单次会话中完成时，才应该使用 wayfinder。这是关于 wayfinder 被问得最多的问题。人们不断提问，是因为技能描述没有告诉你自己的任务属于哪种情况。你必须自行判断所需会话数量。

**它询问"destination"时，指的是本会话结束时，还是整个工作的结束时？**
指整张地图的终点，而不是第一个会话的终点。问题的措辞有歧义，但 wayfinder 的定义就是用于多次会话的工具，因此只用一个会话范围作答毫无意义。典型的目的地包括要移交的[规格说明](https://www.aihero.dev/ai-coding-dictionary/spec)、规划开始前必须确定的决定、概念验证，或就地进行的改动(例如数据迁移)。

**地图清晰后，wayfinder 不是已经写好规格说明并建立了工单吗？为什么还要用 `/to-spec` 和 `/to-tickets`？**
不是。Wayfinder 的工单是决策工单，地图关闭时它们也全都已关闭。剩下的是一张包含互相关联决定的地图，而不是构建计划。[to-spec](https://aihero.dev/skills-to-spec) 会将这些相关决定归纳成一份规格说明(`/to-spec #<map_issue>`)，[to-tickets](https://aihero.dev/skills-to-tickets) 会再将规格说明拆分为曳光弹(tracer bullet)式实现工单。如果你直接将地图交给 [implement](https://aihero.dev/skills-implement)，就会跳过归纳步骤，并丢失相互关联的细节。只有工作最终证明规模很小时，才直接开始实现。有人采用较短流程，并报告它可行。多出的两个步骤能生成一份明确的规格说明，审查者或同事都可以阅读；如果你不是独自工作，这一点就更加重要。

**代理在 wayfinder 会话中途开始编写生产代码。**
这是此技能最常被报告的失败情况，而且确实是技能本身的缺口导致的。你可以在地图的 **Notes** 中覆盖 wayfinder 默认的"规划，不要执行"。但 Notes 是代理编写的，因此限制及其例外都存在于由受限代理自行管理的文件中。一位用户曾看到代理在自己的 Notes 中写入"这张地图会包含执行工作"。后续会话中，代理将这句话当成授权，并在生产服务器上构建功能。技能没有针对"我本来想使用默认行为"的强制停止机制。在修复前，查看所有不是由你自己绘制的地图中的 Notes，将实现工作留在独立会话中，并将任何看起来像构建切片的 `wayfinder:task` 工单视为类型标记错误。

**我绘制了 27 张工单，处理到第 13 张时，其余内容已经不再合理。**
这段问题来自用户报告原文，其他人也报告了相同情况。Wayfinder 默认会进行全面规划。如果后续工单依赖的假设被之前工单推翻，地图就会落入批评者所说的瀑布陷阱。有两个方法有帮助。第一，将地图范围限制在一个明确的目的地，而不是整个产品。用户报告说，与漫无边际的"实现 V1"相比，以一项明确的 epic 为范围的地图效果更好。目标是发布小步增量，而不是规划非常庞大的工作。第二，积极使用[原型](https://www.aihero.dev/ai-coding-dictionary/prototyping)。低成本的具体产物能在实现依赖某个假设前暴露不确定性，因此路线可以保持有效。Wayfinder 应该是"最大化原型"，而不是"最大化规划"。

**我可以并行处理多张工单吗？**
前沿能显示哪些工单可以领取，阻塞关系也会让并行工作在纸面上显得安全。实际操作中，一次处理一张是更安全的默认选择。如果同时处理两张深入追问工单，由于不同会话不共享[上下文](https://www.aihero.dev/ai-coding-dictionary/context)，其中一个会话可能会询问你刚在另一个会话中回答过的问题。原型工单也存在已知缺口。一位用户报告说，代理生成了三种界面版本，自行选出一个，然后关闭了工单。选择权应属于你，但技能对此还没有明确说明。如果确实要并行处理工单，先亲自检查依赖关系图。

**我必须使用 GitHub Issues 吗？**
不必。任何议题跟踪系统都可以使用。GitHub 的支持最好，因为它的原生子议题和阻塞关系无需打开地图即可显示前沿。用户也会使用 GitLab、Linear、Jira 和本地 Markdown。有两点需要注意：议题跟踪系统没有原生阻塞关系时，wayfinder 会根据文字推断依赖关系图，你必须手动修正；本地 Markdown 会把产物放进仓库，通常不推荐，因为存放在仓库中的材料往往会意外保留下来。开源维护人员遇到相反的问题(公开议题跟踪系统被代理生成的规划工单塞满)，因此仍经常选择本地 Markdown。

**深入追问让人精疲力尽。每个问题都长达三段。**
这是 wayfinder 目前最严重的未解决投诉，还没有人修复。一位用户将问题概括为：文字冗长本身会造成决策疲劳，问题太长也会隐藏代理提问的*原因*，因此地图逐渐扩展时，你会失去从一项决定到另一项决定的联系。冗长看起来更像当前[模型](https://www.aihero.dev/ai-coding-dictionary/model)的问题，而不是技能本身的问题。用户尝试了两种缓解办法：降低[推理力度](https://www.aihero.dev/ai-coding-dictionary/effort)，并在全局 `CLAUDE.md` 中写入通俗语言指示。无论如何，仍要做好深入思考的准备。Wayfinder 要求你大量思考，这就是它的大部分作用，不是缺陷。

**我已经关闭的决定后来证明是错误的。我应该编辑旧工单，还是创建新工单？**
没有官方指引，而且代理的默认行为没有帮助。它通常会围绕错误决定进行设计，而不是质疑决定，因此你必须自行引导它。有效的做法是明确告诉 wayfinder 哪些内容改变了。然后它会更新地图、修改受影响的工单，并在已关闭工单上评论。你可以在地图中途修正范围变化。但如果你一开始就*设计*了一张会变化的地图，就说明范围有问题。

**`decision-mapping` 去哪里了？**
它就是此技能。v1.1 将其更名为 `wayfinder`，调用命令是 `/wayfinder`。"Decision map"是行业黑话，而且也不准确，因为四种工单类型中只有一种本身就是决定。新名称使用了一套一致的词汇(目的地、战争迷雾、前沿、地图)，而不是在原有概念上再造一个术语。工单单位仍保留"决定"一词：wayfinder 工单是**决策工单**，避免人们将其误解为实现工单。

## 使用效果良好时

- 在建立第一张工单之前，目的地已经写下并达成一致。
- 每张开放工单都在提出问题。任何写着"构建 X"的工单，或者类型有误，或者属于地图之后的工作。
- 无需打开地图，就能在议题跟踪系统中看到哪些工单可以领取，因为原生阻塞关系会显示前沿。
- 一个会话只解决一张工单，发布解决评论，关闭工单，并在地图的 *Decisions so far* 中增加一行，然后就停止。
- **Not yet specified** 随时间缩小。迷雾成为工单后，就会从该章节中移除，不会在两个位置重复出现。
- 开始时采用广度优先方式深入追问，如果没有发现迷雾，技能就会停止，并告知你工作规模足够小，可以跳过地图。
- 完成地图的会话会建议你创建规格说明，而不是拉取请求。

## 所在流程

`wayfinder` 是一项**针对特定情形的工作入口**，不是默认起点。大多数工作仍从以深入追问为主的"想法 → 发布"流程开始。只有想法太大，无法在一次会话中处理时才使用 wayfinder。它会在 [to-spec](https://aihero.dev/skills-to-spec) 处重新汇入主流程，因为地图清晰后会移交工作，而不会开始构建。

大部分工作由 wayfinder 调度的其他技能完成。[grilling](https://aihero.dev/skills-grilling) 和 [domain-modeling](https://aihero.dev/skills-domain-modeling) 会解决默认类型的工单，[prototype](https://aihero.dev/skills-prototype) 会解决无法通过交谈确定的工单，[research](https://aihero.dev/skills-research) 会以子代理的方式运行，让调查过程留在你的会话之外。[handoff](https://aihero.dev/skills-handoff) 负责移入和移出工作：将规模过大的对话移交到地图中，也将会话中途出现的旁支任务从地图中移出。其他任何情况下，[ask-matt](https://aihero.dev/skills-ask-matt) 都可以在整套技能中为你指路。
