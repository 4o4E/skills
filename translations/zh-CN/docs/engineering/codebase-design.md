## 功能说明

`codebase-design` 规定设计模块时使用的词语：**模块**、**接口**、**深度**、**接缝**、**适配器**、**调用方收益**、**局部集中度**。它精确定义每个术语，禁止使用含糊替代词("组件"、"服务"、"API"、"边界")，并说明由此得出的少数原则。

它是参考资料，而非流程。它不会运行循环、生成成果，也不会停下来询问你。其他涉及设计的技能都会使用它的词汇。单独使用时，它只提供词汇。调用前要了解这一点。如果你让一个[会话](https://www.aihero.dev/ai-coding-dictionary/session)运行一个没有流程和停止规则的技能并说"开始"，代理就会自行发明流程。下面的问题说明了可能出现的情况。

## 适用情形

输入 `/codebase-design`，或当设计任务符合其用途时由代理自动调用。

当你已经知道要重新设计哪些代码，并需要思考其形态时使用：接缝应该放在哪里，接口可以缩减到什么程度，提取模块是否能减少调用方的复杂度。也可用它来统一设计术语的含义。

有几个相近技能，请根据问题选择：

| 问题 | 技能 |
|---|---|
| 单个模块的形态：接口、接缝、深度 | `codebase-design` |
| **领域的词语**："account"有三种含义，或两个人对"cancellation"的理解不同 | [domain-modeling](https://aihero.dev/skills-domain-modeling) |
| 尚不知道要重新设计**哪个**模块 | [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture)(用于调查并寻找候选项) |
| 希望设计接受质疑，而不只是给出名称 | [grilling](https://aihero.dev/skills-grilling) |
| 有具体行为需要构建，并希望测试能够经受重构 | [tdd](https://aihero.dev/skills-tdd) |

## 词汇

词汇表就是此技能。它逐一界定术语之间的关系，并说明每个术语替代什么词。

| 术语 | 含义 | 不要说 |
|---|---|---|
| **模块 (Module)** | 任何同时具有接口和实现的事物。刻意不限定规模：可以是函数、类、程序包，或跨多个层级的代码切片。 | 单元、组件、服务 |
| **接口 (Interface)** | 调用方正确使用它所必须了解的一切：类型签名、不变量、顺序约束、错误模式、必要配置、性能特征。 | API、签名 |
| **深度 (Depth)** | 接口所带来的调用方收益：调用方或测试每了解一个单位的接口，就能使用多少行为。**深层**：简洁接口之后有大量行为。**浅层**：接口与实现几乎同样复杂。 | 无 |
| **接缝 (Seam)** | Michael Feathers 的术语：可以在某处之外更改该处行为的位置。它是接口的**位置**；放置位置是单独的决策，与接口背后放置的内容不同。 | 边界 |
| **适配器 (Adapter)** | 在接缝处满足接口的具体事物。它命名的是一个角色，而非某种事物：内存中的假实现和 Postgres 仓储都是适配器。 | 无 |
| **调用方收益 (Leverage)** | 调用方从深度得到的好处：每单位了解的接口都提供更多能力。 | 无 |
| **局部集中度 (Locality)** | 维护人员从深度得到的好处：更改、错误和验证都集中在一处，因此一次修复可以覆盖所有调用方。 | 无 |

此技能**不**将深度定义为实现代码行数与接口代码行数的比率，那是 Ousterhout 本人的定义。该指标会鼓励填充实现，因此此技能采用"深度即调用方收益"的定义。

## 四项原则

- **深度是接口的属性，而不是实现的属性。** 深层模块内部可以由可替换的小型部分构成，调用方只是不知道它们。模块可以同时有供自身测试使用的内部接缝，以及位于其接口处的一个外部接缝。
- **删除模块检验。** 想象删除模块。如果复杂性随之消失，它只是在转发调用。如果复杂性又出现在 N 个调用方中，模块就隐藏了真正的复杂性。
- **接口就是测试面。** 调用方和测试通过同一接缝。如果要越过接口深入模块内部进行测试，说明模块形态不正确。
- **一个适配器代表假设中的接缝。两个适配器代表真实接缝。** 没有变化跨越接缝时，不要创建接缝。单适配器接缝只是增加间接层。

另外两个支持文件包含更多内容；技能需要时才读取，而不是预先全部读取。[DEEPENING.md](https://github.com/mattpocock/skills/blob/main/skills/engineering/codebase-design/DEEPENING.md) 将候选项依赖分为四类(进程内、本地可替代、自有远程服务、真正的外部依赖)，因为类别决定如何通过接缝测试加深后的模块。[DESIGN-IT-TWICE.md](https://github.com/mattpocock/skills/blob/main/skills/engineering/codebase-design/DESIGN-IT-TWICE.md) 会启动并行[子代理](https://www.aihero.dev/ai-coding-dictionary/subagent)，为同一模块生成三个或更多截然不同的接口，然后根据深度、局部集中度和接缝位置进行比较。

## 常见问题

**我在 TypeScript 中究竟应该如何构建深层模块？**

这是关于此技能最常见的问题，但技能并没有回答。它定义了深层模块*是什么*，没有说明如何阻止其他代码绕过接口导入内部内容。[issue #458](https://github.com/mattpocock/skills/issues/458) 表达得很清楚："假设我们对接口满意，它隐藏了细节等。但我们如何强制执行？我认为没有代码检查或明确护栏，人类和大型语言模型最终都会把代码弄乱。"该讨论给出了三种选择：将它封装进类或 IIFE，并接受类会变得很大；在单体仓库中将它做成程序包，并接受需要使用单体仓库工具；或使用 [dependency-cruiser](https://github.com/sverweij/dependency-cruiser) 之类的 linter，禁止绕过接口导入内容。在这些机制中，Effect 最好，dependency-cruiser 次之。仓库 `in-progress/` 类别中有一个 `setup-ts-deep-modules` 技能，它规定了 `src/packages/<name>/index.ts` 惯例，但这是一个测试阶段的技能，没有文档页面，也没有提供代码检查规则。

**我让会话运行它，结果花费 100k 个[token](https://www.aihero.dev/ai-coding-dictionary/token)重新设计我从未要求过的内容。**

这是已知问题，已记录为[issue #449](https://github.com/mattpocock/skills/issues/449)。此技能由模型调用，并说明自己只提供词汇，但没有内容能明确阻止代理把它当成可执行流程。有人要求"在 `/codebase-design` 中恢复，并推动未决决定"，代理便选择了最接近流程的部分：`DESIGN-IT-TWICE.md` 中的并行子代理。它重新探索了前一个会话已经梳理过的代码，并且工作了很久才提出问题。由于它是参考资料，此技能没有驱动型技能具有的护栏(检查点、一次一个问题、不得自动推进)。解决办法是调用驱动型技能(`/grill-with-docs`、`/improve-codebase-architecture` 或 `/tdd`)，并将 `codebase-design` 作为词汇参考。该 issue 仍未解决。

**`design-an-interface` 去哪里了？有 `/interface-design` 技能吗？**

此技能取代了 `design-an-interface` 并接收了它的内容，没有任何内容丢失。它的"双重设计"技巧(基于 Ousterhout，由并行子代理生成截然不同的设计)现在作为 `DESIGN-IT-TWICE.md` 随技能一起发布。另外，有几个人要求增加专门用于深层模块 / 精简接口理念的 `/interface-design` 技能；当前技能已经涵盖了这一理念，因此不计划单独创建技能。如果你在寻找这两个名称中的任何一个，这就是对应页面。

**这难道不是文件结构惯例，例如文件夹、barrel 文件、功能切片吗？**

不是。人们多次提出异议，但技能一直没有改变。[issue #95](https://github.com/mattpocock/skills/issues/95) 提议将形式化的分形树文件结构作为深层模块的具体实现；回应指出两者彼此独立："深层模块关注接口设计，以及通过严格接口访问，不管文件系统是什么样子。完全可以采用这种方式，但仍然设计出浅层模块。"#458 也出现了类似讨论："我认为你可能把模块概念和文件系统绑得太紧了。文件系统确实可以提示模块形态，但构建深层模块并不需要使用文件系统。"词汇表有意将**模块**定义为不限定规模的概念。

**`tdd` 真的会使用这些词汇吗？**

现在会，但很长一段时间里并不会。v1.0 从 `tdd` 中移除了内嵌的深层模块说明，改用此共用技能，却没有添加替代链接，因此 `tdd` 自行定义了"接缝"一词，也没有引用任何内容。现在 `tdd` 中已有链接，接口形态存在疑问而非测试本身存在疑问时，代理会使用该链接。`tdd` 仍负责定义作为测试位置的"接缝"；此技能负责定义接缝背后的模块形态。

**双重设计模式能在 Claude Code 之外使用吗？**

不能顺利使用。`DESIGN-IT-TWICE.md` 说"使用 Agent 工具并行启动 3 个或更多子代理"，而"Agent"是 Claude Code [工具](https://www.aihero.dev/ai-coding-dictionary/tool)的名称。仓库提供了适用于 Codex 等其他[harness](https://www.aihero.dev/ai-coding-dictionary/harness)的元数据，但这些环境可能没有同名工具。因此，并行设计阶段的可移植性不如技能元数据所暗示的那样高。[issue #564](https://github.com/mattpocock/skills/issues/564) 正在跟踪此问题，目前尚未解决。

**我可以在词汇表中添加自己的概念吗，例如共现性、模块秘密、[渐进式披露](https://www.aihero.dev/ai-coding-dictionary/progressive-disclosure)？**

有人提出过这些想法。[issue #180](https://github.com/mattpocock/skills/issues/180) 附有可运行的差异，提议使用 Parnas 的模块秘密和 Page-Jones 的共现性，命名跨接缝泄漏的**内容**；[issue #303](https://github.com/mattpocock/skills/issues/303) 提议在实现内部使用渐进式披露，让公开接口深层的模块内部也具有结构。这两项都尚未合并。发布版词汇表刻意保持精简，技能也说明了原因：保持语言一致是全部目的，而无人能够一致使用的术语还不如没有术语。

## 判断是否有效

- 设计讨论不再出现"组件"、"服务"、"边界"，而是开始使用"模块"、"接口"、"接缝"。
- 对于拟议的模块提取，有人能够明确判断它是否通过删除模块检验，而不含糊其辞。
- 拟议接缝会指出第二个适配器，而不只提出第一个。
- 接口讨论涵盖不变量、顺序和错误模式，而不只是类型签名。
- 调用此技能不会开始一场会话。如果代理仅凭 `/codebase-design` 就开始读取文件并提出重构方案，说明它将参考资料误当成了驱动流程。

## 它在工作流程中的位置

`codebase-design` 是随时可用的独立参考，也是工程技能下层的词汇资料，不是任何流程链中的步骤。最相近的是 [domain-modeling](https://aihero.dev/skills-domain-modeling)，它提供的是**问题领域**用词的对应资料，而不是模块形态用词。通常两者都很有用，因为要为深层模块恰当命名，两方面都需要。[improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 是另一个相关技能：它会调查代码库并寻找加深模块的候选项，所有候选项都使用此词汇表表达，因此它负责找到模块，本技能负责提供设计用词。如果不确定该选择哪种技能或流程，[ask-matt](https://aihero.dev/skills-ask-matt) 会为你引导。
