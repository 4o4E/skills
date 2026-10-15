<p>
  <a href="https://www.aihero.dev/s/skills-newsletter">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skills-repo-dark_2x.png">
      <source media="(prefers-color-scheme: light)" srcset="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skill-repo-light_2x.png">
      <img alt="技能" src="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skill-repo-light_2x.png" width="369">
    </picture>
  </a>
</p>

# 为真正的工程师准备的技能

[![skills.sh](https://skills.sh/b/mattpocock/skills)](https://skills.sh/mattpocock/skills)

这套智能体技能是我日常从事严谨工程开发使用的工具，而非用来随性写代码(vibe coding)。

开发真正的应用很难。GSD、BMAD 和 Spec-Kit 等方法试图通过接管流程来提供帮助。但这样做会剥夺你的控制权，也会让流程中的缺陷难以解决。

这些技能的设计目标是体积小、易于调整、可以组合，并适用于任何模型。它们以数十年的工程经验为基础。尽情尝试，也可以改造成适合自己的方式。希望你用得开心。

如果你想了解这些技能的更新和我创建的新技能，可以加入我的通讯订阅者，和大约六万名开发者一起接收消息：

[订阅通讯](https://www.aihero.dev/s/skills-newsletter)

## 安装(30 秒完成设置)

插件会自行更新。[skills.sh](https://skills.sh/mattpocock/skills) 会将可编辑文件复制到项目中，你需要手动更新。每种代理工具只选择一种安装方式，避免重复安装导致每项技能重复出现。

### 1. 获取技能

<details>
<summary><strong>Claude Code</strong></summary>

```bash
claude plugin install mattpocock-skills@claude-plugins-official
```

默认会自动更新。

</details>

<details>
<summary><strong>Codex</strong></summary>

```bash
codex plugin marketplace add mattpocock/skills
codex plugin add mattpocock-skills@mattpocock
```

启动时会自动更新。

</details>

<details>
<summary><strong>GitHub Copilot</strong>(命令行工具和 VS Code)</summary>

```bash
copilot plugin marketplace add mattpocock/skills
copilot plugin install mattpocock-skills@mattpocock
```

然后，只需一次，在 `~/.copilot/settings.json` 中添加：

```json
{
  "extraKnownMarketplaces": {
    "mattpocock": { "source": { "source": "github", "repo": "mattpocock/skills" }, "autoUpdate": true }
  }
}
```

在 VS Code 中运行 **Chat: Install Plugin From Source**，并输入 `https://github.com/mattpocock/skills`。它每天会自动更新。

</details>

<details>
<summary><strong>Gemini CLI</strong>(手动更新)</summary>

```bash
gemini skills install https://github.com/mattpocock/skills.git --path skills/engineering
gemini skills install https://github.com/mattpocock/skills.git --path skills/productivity
```

重新运行这两条命令即可更新。

</details>

<details>
<summary><strong>任何其他代理，或可编辑文件</strong>(手动更新)</summary>

```bash
npx skills@latest add mattpocock/skills -a <agent>  # cursor, opencode, devin, windsurf, amp, pi; omit -a to choose
```

安装程序询问要安装哪些技能时，请选择 `setup-matt-pocock-skills`。要更新，请运行 `npx skills@latest update`；要获取新技能，请重新运行 `add`。

</details>

### 2. 运行 `/setup-matt-pocock-skills`

在代理中，每个代码仓库运行一次。它会：

- 询问你想使用哪种议题跟踪系统(GitHub、GitLab、本地文件，或任何你说明的其他工具)
- 询问你在分流工单时使用哪些标签(`/triage` 会使用这些标签)
- 询问你想将我们创建的文档保存到哪里

### 3. 完成以上步骤后，即可开始使用。

## 创建这些技能的原因

我创建这些技能，是为了解决自己使用 Claude Code、Codex 和其他编码代理时经常遇到的问题。

### #1：代理没有按我的想法做

> "没有人能确切知道自己想要什么。"
>
> David Thomas 和 Andrew Hunt，[《务实的程序员》](https://www.amazon.co.uk/Pragmatic-Programmer-Anniversary-Journey-Mastery/dp/B0833F1T3V)

**问题**：软件开发中最常见的失效模式是目标不一致。你以为开发者知道你想要什么；看到他们完成的内容后，才发现他们根本没理解你的要求。

进入人工智能时代后，情况依然如此。你和代理之间存在沟通差距。解决方法是进行**追问会话**：让代理详细询问你正在构建什么。

**解决方法**是使用：

- [`/grill-me`](./skills/productivity/grill-me/SKILL.md)：用于非代码工作
- [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.md)：与 [`/grill-me`](./skills/productivity/grill-me/SKILL.md) 相同，但还会提供其他功能(见下文)

这是我最受欢迎的两项技能。它们会在工作开始前帮助你和代理明确目标，也让你深入思考要做的变更。建议每次准备修改代码时都使用。

### #2：代理说得太多

> 通过通用语言，开发者之间的对话和代码中的表达都来自同一个领域模型。
>
> Eric Evans，[《领域驱动设计》](https://www.amazon.co.uk/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215)

**问题**：项目开始时，开发者与他们正在为之开发软件的人(领域专家)通常使用不同的语言。

我和代理也遇到了同样的问题。代理通常一开始就被放进项目，边做边理解术语。因此，本来一个词就够表达的内容，它会用二十个词来说明。

**解决方法**是建立共同语言。这是一份帮助代理理解项目术语的文档。

<details>
<summary>
示例
</summary>

下面是我的 `course-video-manager` 代码仓库中的一个[词汇表](https://github.com/mattpocock/course-video-manager/blob/076a5a7a182db0fe1e62971dd7a68bcadf010f1c/CONTEXT.md)示例(在此固定版本中仍名为 `CONTEXT.md`，因为当时这些技能尚未将约定名称更改为 `GLOSSARY.md`)。你觉得哪种说法更容易阅读？

- **之前**："课程某个章节中的一课被‘实体化’(也就是在文件系统中获得一个位置)时会出现问题。"
- **之后**："实体化级联有问题。"

这种简洁表达会在一次又一次的会话中带来好处。

</details>

这项能力已纳入 [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.md)。它是一场追问会话，能帮助你与人工智能建立共同语言，并在 ADR 中记录难以解释的决定。

这项能力的效果极为突出，可能是本仓库中最实用的技巧，值得一试。

> [!TIP]
> 共同语言还有许多其他好处，不只减少冗长表达：
>
> - **变量、函数和文件的命名一致**，因为它们都采用共同语言
> - 因此，**代理更容易浏览代码库**
> - 代理也会**用更少的词元进行思考**，因为它可以使用更精简的语言

### #3：代码无法运行

> "始终采用小步且有意识地推进。反馈速度就是你的速度上限。绝不要承担过大的任务。"
>
> David Thomas 和 Andrew Hunt，[《务实的程序员》](https://www.amazon.co.uk/Pragmatic-Programmer-Anniversary-Journey-Mastery/dp/B0833F1T3V)

**问题**：假设你和代理已经对要构建的内容达成一致。代理还是产出了糟糕的结果，该怎么办？

这时就要检查反馈回路。如果没有关于代码实际运行情况的反馈，代理就只能盲目工作。

**解决方法**：你需要常见的反馈回路：静态类型检查、浏览器访问，以及自动化测试。

自动化测试中，红绿重构循环至关重要。这个流程要求代理先编写一个无法通过的测试，再修复问题。这会给代理持续、稳定的反馈，从而产出好得多的代码。

我编写了一个可以放进任何项目使用的 **[`/tdd`](./skills/engineering/tdd/SKILL.md) 技能**。它鼓励采用红绿重构流程，并为代理提供大量指导，说明什么是好测试和坏测试。

我也编写了一个 **[`/diagnosing-bugs`](./skills/engineering/diagnosing-bugs/SKILL.md) 技能**用于调试。它将最佳调试实践纳入严谨流程，并在每个阶段设置关卡。

### #4：代码库变成一团乱麻

> "每天都要投入精力设计系统。"
>
> Kent Beck，[《极限编程解析》](https://www.amazon.co.uk/Extreme-Programming-Explained-Embrace-Change/dp/0321278658)

> "优秀的模块都很深。它们通过简单的接口提供大量功能。"
>
> John Ousterhout，[《软件设计哲学》](https://www.amazon.co.uk/Philosophy-Software-Design-2nd/dp/173210221X)

**问题**：许多使用代理构建的应用都很复杂，难以修改。代理可以大幅加快编码速度，因此也加速了软件熵增。代码库以从未有过的速度变得越来越复杂。

**解决方法**是一种支持人工智能开发的新方法：重视代码设计。

这些技能的每个层面都包含这种方法：

- [`/to-spec`](./skills/engineering/to-spec/SKILL.md) 会在创建规格说明之前询问你将要修改哪些模块

更重要的是，[`/improve-codebase-architecture`](./skills/engineering/improve-codebase-architecture/SKILL.md) 会调查代码库中适合加深模块的机会，并将候选项交给你。我建议每隔几天在代码库上运行一次。它是调查工具，不是救援工具：对于真正陈旧的代码库，它会找到值得处理的候选项，但不会替你理清混乱。

### 总结

软件工程基础比以往任何时候都更重要。这些技能是我努力将基础知识浓缩成可重复实践的成果，帮助你交付职业生涯中最好的应用。希望你用得开心。

## 参考

以下技能按照一个维度划分：谁可以调用它们。**由用户调用**的技能只有在你输入时才会被触发(例如 `/grill-me`)；它们的作用是协调流程。**由模型调用**的技能可以由你调用，也可以在任务适合时由代理自动调用；它们包含可复用的方法。由用户调用的技能可以调用由模型调用的技能，但绝不能调用另一个由用户调用的技能。

### 工程

我日常用于代码工作的技能。

**由用户调用**

- **[ask-matt](./skills/engineering/ask-matt/SKILL.md)**：询问哪种技能或流程适合当前情况。它会为此代码仓库中由用户调用的技能提供路由。
- **[grill-with-docs](./skills/engineering/grill-with-docs/SKILL.md)**：同时建立项目领域模型的追问会话，帮助明确术语，并在对话中更新 `GLOSSARY.md` 和 ADR。
- **[triage](./skills/engineering/triage/SKILL.md)**：让议题按照由分流角色组成的状态机流转。
- **[improve-codebase-architecture](./skills/engineering/improve-codebase-architecture/SKILL.md)**：扫描代码库寻找适合加深模块的机会，以可视化 HTML 报告呈现，再通过追问探讨你选定的机会。
- **[setup-matt-pocock-skills](./skills/engineering/setup-matt-pocock-skills/SKILL.md)**：为工程技能配置此代码仓库(议题跟踪系统、分流标签、领域文档结构)。使用其他工程技能之前，每个代码仓库运行一次。
- **[to-spec](./skills/engineering/to-spec/SKILL.md)**：将当前对话转成规格说明并发布到议题跟踪系统。不进行访谈，只归纳你已经讨论的内容。
- **[to-tickets](./skills/engineering/to-tickets/SKILL.md)**：将任何计划、规格说明或对话拆分成一组曳光弹(tracer bullet)工单，逐项说明阻塞关系，可以写入本地文件，也可以在实际议题跟踪系统中建立原生阻塞链接。
- **[implement](./skills/engineering/implement/SKILL.md)**：根据规格说明或工单集合开展工作，在预先同意的接缝处使用 `/tdd`，提交前使用 `/code-review` 完成审查。
- **[implement-spec](./skills/engineering/implement-spec/SKILL.md)**：在单一集成分支上实施完整规格说明。将工单作为任务拓扑图处理，并发调度实施子代理处理所有已就绪的前沿工单，实现最大并发度，最后通过 `/code-review` 完成审查。
- **[wayfinder](./skills/engineering/wayfinder/SKILL.md)**：将大到无法由一个代理会话完成的工作，规划成议题跟踪系统中的共享决策工单地图；逐项处理决定，直到清楚该如何到达目标。
- **[retro](./skills/engineering/retro/SKILL.md)**：会话结束后提出改进编码代理环境的建议(导航、自动检查、编码规范、指导文件、工具)，优先指出严重程度最高的问题。

**由模型调用**

- **[prototype](./skills/engineering/prototype/SKILL.md)**：制作一次性原型以回答设计问题：状态或逻辑问题可制作一个便于分享的 HTML 文件，或制作多个差异明显且可从同一路径切换的界面变体。
- **[diagnosing-bugs](./skills/engineering/diagnosing-bugs/SKILL.md)**：针对难以排查的缺陷和性能退化，采用严谨的诊断流程：建立会因该缺陷而变红的反馈回路 → 缩小问题 → 提出假设 → 加入观测 → 修复 → 回归测试。
- **[research](./skills/engineering/research/SKILL.md)**：依据高度可信的一手资料调查问题，并将附有引用的 Markdown 文件写入代码仓库，由后台代理运行。
- **[tdd](./skills/engineering/tdd/SKILL.md)**：采用红绿重构循环进行测试驱动开发，一次处理一个垂直切片，构建功能或修复问题。
- **[domain-modeling](./skills/engineering/domain-modeling/SKILL.md)**：主动建立并明确项目领域模型：根据词汇表质疑术语，使用边缘情境进行压力测试，并在对话中更新 `GLOSSARY.md` 和 ADR。
- **[codebase-design](./skills/engineering/codebase-design/SKILL.md)**：设计深层模块时遵循共同的纪律和词汇：少量接口承载大量行为，将模块放在清晰的接缝处，并确保可以通过接口进行测试。
- **[code-review](./skills/engineering/code-review/SKILL.md)**：从固定基准点审查差异，分为两个方面：**编码标准(Standards)**(是否遵循代码仓库规范，并参照 Fowler 的代码异味基准？)和**规格说明(Spec)**(是否忠实实施源议题或规格说明？)；由并行子代理分别执行，避免相互影响。
- **[pr](./skills/engineering/pr/SKILL.md)**：规范拉取请求(PR)正文格式：以最精简的图示说明变更，提供前后证据证明变更有效，并评估合并风险(单向门还是双向门，以及影响范围)。
- **[wizard](./skills/engineering/wizard/SKILL.md)**：制作交互式 Bash 向导，引导人类完成只有他们能做的步骤：配置基础设施、设置凭据或 CI 机密、操作不熟悉的第三方控制台，或执行一次性迁移和切换。

### 效率

通用工作流程工具，不限于代码。

**由用户调用**

- **[grill-me](./skills/productivity/grill-me/SKILL.md)**：围绕计划或设计持续访谈，直到设计树的每个分支都已解决。
- **[handoff](./skills/productivity/handoff/SKILL.md)**：将当前对话整理成交接文档，让另一位代理可以继续工作。
- **[teach](./skills/productivity/teach/SKILL.md)**：在多次会话中，使用当前目录作为有状态教学工作区，教用户一项新技能或概念。
- **[to-questionnaire](./skills/productivity/to-questionnaire/SKILL.md)**：将你无法独自决定的事项转成 Markdown 问卷，交给唯一能够回答的人，由对方异步填写或在会议中共同填写。它只会追问发送方式(发给谁、需要什么回复)，不会追问主题。
- **[wait-what](./skills/productivity/wait-what/SKILL.md)**：消息没有讲明白时立即调用。代理会补充你缺少的背景，使用通俗平实的语言，并结合 `GLOSSARY.md` 中的词汇重新表达。

**由模型调用**

- **[grilling](./skills/productivity/grilling/SKILL.md)**：持续访谈用户，讨论计划、决定或想法，直到设计树的每个分支都已解决。它是 `grill-me`、`grill-with-docs`、`triage`、`wayfinder` 和 `improve-codebase-architecture` 共用的基础访谈方式。
- **[writing-for-agents](./skills/productivity/writing-for-agents/SKILL.md)**：撰写供代理使用的文档：技能、`AGENTS.md`/`CLAUDE.md`，以及代理通过指针访问的任何文档。
