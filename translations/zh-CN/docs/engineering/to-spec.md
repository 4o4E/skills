## 它的作用

`to-spec` 会将刚才的对话整理成一份**[规格说明](https://www.aihero.dev/ai-coding-dictionary/spec)**，并以单个 issue 发布到 issue tracker。

它不会访谈你。使用它时，决定已经作出。因此它会综合已知内容(来自对话、代码库、`GLOSSARY.md` 和 ADR)，而不会开始新一轮提问。规格说明记录你已经作出的决定，不用于作出新的决定。

## 何时使用

输入 `/to-spec` 来调用它；[代理](https://www.aihero.dev/ai-coding-dictionary/agent)不会自行调用。

当构建规模超过单个代理[会话](https://www.aihero.dev/ai-coding-dictionary/session)的承载能力，必须拆分到多个会话中完成时，使用它。这就是全部触发条件：

| 当前进度 | 使用方式 |
| --- | --- |
| 还没有决定任何内容 | 先使用 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) |
| 已经作出决定，工作适合一个[上下文窗口](https://www.aihero.dev/ai-coding-dictionary/context-window) | 使用 [implement](https://aihero.dev/skills-implement)：跳过规格说明 |
| 已经作出决定，工作跨越多个会话 | 使用 `/to-spec`，然后使用 [to-tickets](https://aihero.dev/skills-to-tickets) |
| [wayfinder](https://aihero.dev/skills-wayfinder) 地图已经清晰 | 使用 `/to-spec #<map_issue>` |

## 前置条件

`to-spec` 会将规格说明发布为 issue，因此 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 必须先为本仓库配置 issue tracker 和分流标签词汇。任一种 issue tracker 都可以：例如 GitHub 这样的真实 issue tracker，或无需额外设置即可使用的 `.scratch/` 下本地 Markdown 文件。

## 规格说明用于记录决定

规格说明存在的原因是上下文窗口终会结束。在[深入追问](https://www.aihero.dev/ai-coding-dictionary/grilling)期间，你已经确定了许多事项：解决方案的形态、讨论过的选择，以及你有意拒绝的内容。所有这些都记录在一段即将被清除的对话中。规格说明会将它们保留下来。

因此，规格说明不会验证或决定任何内容。它会用项目自己的词汇记录你已作出的决定，让新的会话可以继续工作，而无需你重新解释。如果规格说明中写了你从未说过的内容，那就是缺陷。

## 先确认接缝，再写文字

写入任何内容之前，`to-spec` 会拟定测试功能的**接缝**，并与你确认。它优先选择现有接缝，并尽量使用最高层的接缝。一项改动理想的接缝数量是一个。

其他技能之后也会使用你同意的接缝。[tdd](https://aihero.dev/skills-tdd) 只会在你预先同意的接缝处工作。[code-review](https://aihero.dev/skills-code-review) 会根据规格说明审查差异，因此未经同意的接缝会成为审查发现。两个环节都通过本文档关联。因此此处的接缝讨论很重要，不要把它留到实现阶段。

## 常见问题

**`/to-prd` 去哪里了？**
它就是此技能，在 v1.1 中更名了。现在所有地方都统一使用"Spec"这个术语，旧的 `to-prd` 名称不再有效，所以要使用新名称重新安装。旧词汇被"规格说明"和"工单"这一组词取代。规格说明是目的地，也是确定目的地的决定。[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)是抵达目的地的步骤。如果改变方向，就删除尚未完成的工单并保留规格说明。

**为什么规格说明有 `ready-for-agent` 标签？我不希望代理根据它来实现功能。**
该标签表示"无需继续分流"：文档已经足够完整，代理可以据此开展工作。它标记的是输入，而不是工作指令。但轮询 `ready-for-agent` 的 [AFK](https://www.aihero.dev/ai-coding-dictionary/afk) 代理无法识别这一区别。它们会尝试在一次运行中构建整份规格说明，而不是领取其中的工单切片。这是此技能最常被报告的问题。在问题解决前，需在 AFK 代理的提示中明确排除父级规格说明，或在 `/to-tickets` 运行后移除该标签。

**为什么不在深入追问后直接运行 `/to-tickets`，跳过规格说明？**
很多时候你确实应该这样做。只有适用于多个会话的工作，规格说明这一步才有价值。它的价值在于工单可以舍弃，规格说明则不会。每张工单的规模适合一个全新的上下文窗口，完成后就会删除或关闭；规格说明则会持续记录工单背后的理由。对于单会话改动，这一步毫无用处，而且你还要付出一次额外综合的成本，[模型](https://www.aihero.dev/ai-coding-dictionary/model)可能会在这一步偏离原意。此时应从深入追问直接进入 `/implement`。

**我刚完成一张 wayfinder 地图，应该把什么交给它？**
提供主要地图 issue，使用 `/to-spec #<map_issue>`，不要提供单独的决策工单。[wayfinder](https://aihero.dev/skills-wayfinder) 生成的是散布在地图各处的决定，而不是交付成果。`to-spec` 会将它们归纳成一份可据此构建的文档。如果将地图直接交给 `/implement`，就会跳过归纳这一步。

**规格说明是给我审阅的，还是只给代理看的？**
主要给代理看，内容读起来也确实如此：完整、密集，并且充满引用。阅读接缝和范围外章节即可。在这两处，错误决定最容易及早发现，之后再发现的代价也最大。有人确实抱怨必须通读整份文档，而且没有摘要模式。但如果规格说明让你感到意外，说明深入追问还不够深入，而不是规格说明太长。

**开始处理工单后，我应该冻结规格说明，还是让代理改写它？**
没有任何机制会保持规格说明与实现同步。实际上，它只是你在那个时刻所掌握内容的一份快照；实现工作第一次带来新知识时，它就会过时。工作发布后应将其视为可舍弃内容。`GLOSSARY.md` 和 ADR 才是需要长期保留的文件。如果你在实现期间学到应该长期保留的知识，就写入这些文件，而不是修改规格说明。

**我的工作是重构或模块边界改动，而不是功能。模板适用吗？**
适用性较差，这是已知限制。模板非常依赖用户故事，而用户故事不适合架构工作。你最后会围绕无人提出的问题编写用户故事，而实际决定涉及接口和不变量。改用"实现决策"和"测试决策"章节。持久架构决策应通过 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 记录为 ADR，而不是写入规格说明。

**它会检查 issue tracker 中是否有相关工作，或者引用它遵守的 ADR 吗？**
两者都不会。它会读取并遵守所涉及区域的 ADR，但不会链接到它们。写入前它也不会搜索 issue tracker 中是否存在重叠的问题，因此规格说明可能重复他人已经提交的工作，也没有任何提示。如果该领域工作繁忙，应先自行搜索 issue tracker。

**`/to-tickets` 读取规格说明时不断截断，无法读取完整内容。**
issue tracker issue 可能无法完整返回超大型规格说明，也没有本地副本可替代。要解决此问题，不要在 `/to-spec` 和 `/to-tickets` 之间[清空](https://www.aihero.dev/ai-coding-dictionary/clearing)或[压缩](https://www.aihero.dev/ai-coding-dictionary/compaction)上下文。在同一个窗口中运行两者，`/to-tickets` 就不必重新获取规格说明。

## 使用效果良好时

- 它会开始写作，而不是再问你一轮新问题。
- 写作前会展示接缝，并尽可能少地提出接缝。
- 使用项目自己的名词，而不是通用产品管理套话。
- 你记得自己作出了其中的每个决定；它不会为填充章节而发明内容。
- 范围外章节列出真实内容。你拒绝处理的事项通常是页面上最有用的内容。

## 所在流程

`to-spec` 是主要构建流程中的一步，但只用于其中的多会话分支：

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review → retro
```

上游的 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 会作出本技能只负责记录的决定；完成的 [wayfinder](https://aihero.dev/skills-wayfinder) 地图也会在此处汇入流程。下游的 [to-tickets](https://aihero.dev/skills-to-tickets) 会将规格说明切分为曳光弹(tracer bullet)工单，交由 [implement](https://aihero.dev/skills-implement) 构建。如果你不确定该使用哪项技能或工作流程，[ask-matt](https://aihero.dev/skills-ask-matt) 会为你指路。
