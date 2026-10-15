## 它的作用

`to-tickets` 会将计划、[规格说明](https://www.aihero.dev/ai-coding-dictionary/spec)或当前对话拆分为一组发布到 issue tracker 上的**[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)**。每张工单都声明自己的**阻塞关系**：哪些其他工单必须先完成，它才能开始。

每张工单都是一颗**曳光弹(tracer bullet)**：一条贯穿改动所有层(数据结构定义(schema)、API、界面、测试)的狭窄但完整的路径；它一旦交付就可以独立演示。这就是此技能与直观拆分工作的方式之间的区别：后者每次只处理一层，最后再集成。它也会确保每张工单的规模适合一个全新的[上下文窗口](https://www.aihero.dev/ai-coding-dictionary/context-window)，这样从未见过规格说明的[会话](https://www.aihero.dev/ai-coding-dictionary/session)也能领取工单。

## 何时使用

输入 `/to-tickets` 来调用它。[代理](https://www.aihero.dev/ai-coding-dictionary/agent)不会自行调用。

| 当前进度 | 使用方式 |
| --- | --- |
| 有一张规格说明 issue，构建工作跨越多个会话 | 使用 `/to-tickets` 或 `/to-tickets #<spec_issue>` |
| 计划只在对话中，尚未写入文档 | 直接运行 `/to-tickets`，它会读取对话，不需要规格说明 |
| 整项改动适合一个上下文窗口 | 使用 [implement](https://aihero.dev/skills-implement)，跳过工单 |
| 还没有决定任何内容 | 使用 [grill-with-docs](https://aihero.dev/skills-grill-with-docs)，再使用 [to-spec](https://aihero.dev/skills-to-spec) |
| [wayfinder](https://aihero.dev/skills-wayfinder) 地图已清晰 | 先使用 [to-spec](https://aihero.dev/skills-to-spec) 归纳地图，再使用 `/to-tickets` |

`to-tickets` 生成的工单已经可以交给代理处理。不要对它们运行 [triage](https://aihero.dev/skills-triage)。Triage 用于他人提交的工作。

## 前置条件

`to-tickets` 会将工单发布到 issue tracker，因此 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 必须已为本仓库配置 issue tracker 及分流标签词汇。真实 issue tracker(如 GitHub 或 Linear)和 `.scratch/` 下的本地 Markdown 文件都可以使用，后者无需额外设置。

## 使用曳光弹，不要按层拆分

**横向切片 (Horizontal slice)**会发布改动中的一层。每一层完成前都无法正常运作，而且每张工单的验收标准都要涉及其他工单负责的工作。**纵向切片 (Vertical slice)**(曳光弹 / Tracer bullet)会一次性贯穿所有层，形成一条细小的路径，因此能够独立验证并涵盖它要检验的所有内容。

这是人们最常违反的规则。有一个团队将工作按层拆分成 26 张工单(语料库、生产者、聚合器、选择器)，每关闭一张工单大约运行 20 次代理会话，其中约四分之三都需要返工。他们在事后分析中发现，每一类失败都源自横向切片，而不是具体实现。

发布前有两项工作。`to-tickets` 会先寻找预先重构的机会(原则是"先让改动变简单，再完成简单的改动")，并把这项工作安排在前面。然后它会用编号列表展示拆分结果，并询问你：粒度是否适当，阻塞关系是否真实，是否应该合并或拆分工单。未经你批准，不会有任何内容进入 issue tracker；此时你可以提出修改意见。

## 阻塞关系

工单的核心价值就是阻塞关系。它们根据 issue tracker 有两种表达方式：

| issue tracker | 阻塞关系的存放位置 | 处理方式 |
| --- | --- | --- |
| 本地 Markdown | `.scratch/<feature>/issues/<NN>-<slug>.md` 下每张工单的独立文件中，先编号阻塞项，再编号被阻塞的工单 | 手动从上到下处理 |
| 真实 issue tracker(GitHub、Linear) | 原生阻塞链接；如果 issue tracker 支持，也可以使用子问题 | 阻塞项都已完成的工单位于**前沿**，可以领取 |

无论采用哪种方式，阻塞关系都属于工单本身。issue tracker 只决定能否并行使用它们。`to-tickets` 负责生成工单；逐张处理(一次处理一个，或并行调度一组代理)是你的工作，不属于该技能。

## 大范围重构的例外

有一种情况不符合曳光弹规则。**大范围重构**是一项单一机械变更(例如重命名列、更改共享符号的类型)，其**影响范围**覆盖整个代码库。一次编辑会破坏数千个调用点，因此无法以任何纵向切片保持通过。

这种情形下，`to-tickets` 会采用**扩展与收缩**顺序安排工作：

- **扩展**：在旧形式旁增加新形式，确保没有内容损坏。
- **迁移**：按照影响范围将调用点分批迁移(每个软件包或目录一批)，每批一张工单，且每批都受扩展工单阻塞。由于旧形式仍然存在，持续集成会保持通过。
- **收缩**：所有调用者迁移后，在一张受每个迁移批次阻塞的工单中删除旧形式。

如果各批次都无法单独保持通过，就让它们共用集成分支，并全部阻塞最终的集成与验证工单。持续集成只需要在最终工单处通过。

## 常见问题

**一次只有三行的改动为什么产生了十二张工单？**
过度拆分是此技能最常被报告的问题，许多用户都遇到过。[模型](https://www.aihero.dev/ai-coding-dictionary/model)默认会选择最小单位，因而无法保留能让它们有意义的组合关系。在提问环节合并工单即可修正。还存在一个下限：如果整项改动适合一个上下文窗口，就完全不需要此技能，直接使用 [implement](https://aihero.dev/skills-implement)。

**工单变成了每层一张：一张负责所有数据库结构，另一张负责所有 API。**
这是制定纵向切片规则要避免的情况，但技能有时仍会这样做。在询问环节逐张提出这个问题："完成后我可以演示什么？"没有明确答案的工单就是横向切片。有人因此在每张工单上添加"演示路径"一行，并报告这能让模型倾向于采用纵向切片。

**在 GitHub 上，工单没有成为规格说明 issue 的子问题。**
这个问题在十几次运行和多个模型中都有人报告，[问题 #554 中的描述最完整](https://github.com/mattpocock/skills/issues/554)。现已修复：`to-tickets` 会将每张工单设为其来源 issue 的子问题，GitHub issue tracker 模板也提供了命令(`gh issue create --parent <n>`，要求 `gh` 2.94+)。重新运行 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills)，以更新较旧的 `docs/agents/issue-tracker.md`。

**"Blocked by"被写入 issue 正文，而不是实际的阻塞链接。**
这是类似的问题，[曾在问题 #513 中报告](https://github.com/mattpocock/skills/issues/513)，当时代理甚至声称 GitHub 完全不支持原生阻塞关系。实际上支持：`gh issue create --blocked-by 12,15`。技能会先发布阻塞项，因此创建工单时总能拿到它们的编号。正文内容是为不支持原生关系的 issue tracker 提供的后备方式，不是默认方式。现在原生设置了阻塞关系时，工单模板会省略 `## Blocked by`。

**本地工单保存在哪里？v1.1 的说明提到根目录的 `tickets.md`。**
确实提到了，那是错误。多个并行代理共同编辑一个文件也会造成竞争条件。本地模式现在会按依赖顺序，在 `.scratch/<feature-slug>/issues/<NN>-<slug>.md` 下为每张工单写入一个文件，符合本地 issue tracker 模板已经说明的布局。`NN` 前缀是实际工单编号，因此可以运行 `/implement 03`，不必重新输入长标题。

**它读取规格说明时不断截断。**
非常大的规格说明可能超出 issue tracker issue 可完整返回的容量。没有本地副本可以回退，因此代理会花费[工具调用](https://www.aihero.dev/ai-coding-dictionary/tool-call)反复获取内容片段，却始终读不到结尾。不要在 `/to-spec` 和 `/to-tickets` 之间[清空](https://www.aihero.dev/ai-coding-dictionary/clearing)或[压缩](https://www.aihero.dev/ai-coding-dictionary/compaction)上下文。在同一个上下文窗口中运行两者，代理就不需要重新获取规格说明。

**验收标准无法衡量任何内容：有些标准在开始工作前就已经通过。**
模板要求列出标准，却没有规定标准必须能够失败，因此会出现这种情况。常见的有三种：基准提交时标准就已满足；只有另一张工单的工作才能满足标准；或标准只是复述请求，而不是从产物推导出来的内容。纵向切片能够避免大多数情况，因为交付新行为的切片在基准提交时必然不通过。仍然值得手动检查：针对每项标准，指出什么现象可以证明它不成立，并确认它在实现人员开始工作的提交上确实失败。

**工单已经发布，我该如何运行它们？**
技能到生成工单为止，没有自动派发模式。需要手动派发：查看看板，统计没有开放阻塞项的工单数量，再打开相同数量的代理会话。为每张工单提供全新上下文，并在它们之间清空上下文。无论在 GitHub 还是本地 Markdown 中，[implement](https://aihero.dev/skills-implement) 完成后都不会稳定地关闭工单或勾选完成状态，因此你需要自行更新工单状态。

## 使用效果良好时

- 每张工单都能回答"完成后我可以演示什么？"，答案描述的是行为，而不是某一层。
- 工单以编号清单的形式返回，每张工单都注明"Blocked by"，之后才会发布。
- 列表顶部的工单没有阻塞项，可以立即开始。
- 除原型生成的代码片段外，工单正文没有文件路径或行号。
- 每张工单的描述都能让全新会话在你不参与的情况下完成工作。
- 如果技能发现预先重构的工作，就将其放在处理顺序最前面，而不是混入功能工单。

## 所在流程

`to-tickets` 是主要构建流程中的一步：

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review → retro
```

上游的 [to-spec](https://aihero.dev/skills-to-spec) 会提供已经确定的规格说明，供它拆分。两者应在同一上下文窗口中运行，中间不要清空上下文。下游的 [implement](https://aihero.dev/skills-implement) 会在每张工单的新会话中构建功能，运行 [tdd](https://aihero.dev/skills-tdd) 编写测试，并以 [code-review](https://aihero.dev/skills-code-review) 收尾。另一条下游路径是 [implement-spec](https://aihero.dev/skills-implement-spec)：它会将相同的阻塞关系作为任务图，读取所有就绪工单，并在一个集成分支上并行构建。如果你不确定该使用哪项技能或工作流程，[ask-matt](https://aihero.dev/skills-ask-matt) 会为你指路。
