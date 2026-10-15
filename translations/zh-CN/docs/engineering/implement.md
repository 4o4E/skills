## 功能说明

`implement` 根据已经决定的工作进行实现。你可以为它提供[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)、[规格说明](https://www.aihero.dev/ai-coding-dictionary/spec)，或刚刚在对话中达成一致的计划；它会编写代码，在接缝处运行 [tdd](https://aihero.dev/skills-tdd)，过程中执行类型检查，最后运行 [code-review](https://aihero.dev/skills-code-review)，并提交到当前分支。

它不会重新讨论计划。它不会采访你、进行澄清，也不会提出其他方案。上游已经决定的内容就是输入，此技能的全部工作就是将其变成一次提交。这与在全新[代理](https://www.aihero.dev/ai-coding-dictionary/agent)中输入"构建这个"不同；后者经常会一边构建一边重新设计工作内容。

## 适用情形

你需要自行输入 `/implement` 调用此技能，代理不会自行调用。此技能设置了 `disable-model-invocation: true`，因此其他技能也无法调用它。无论 [ask-matt](https://aihero.dev/skills-ask-matt) 或 [to-tickets](https://aihero.dev/skills-to-tickets) 何时说"然后对每个工单运行 `/implement`"，这都是对你的指示，不是代理会主动执行的内容。

工作目前处于哪种状态，决定是否适用此技能：

| 工作状态 | 使用方式 |
|---|---|
| 跟踪系统中的工单 | `/implement #42`，每个[会话](https://www.aihero.dev/ai-coding-dictionary/session)处理一个工单，并在工单之间[清理](https://www.aihero.dev/ai-coding-dictionary/clearing)上下文 |
| 规格说明尚未拆分，且实现工作要跨多个会话 | 先使用 [to-tickets](https://aihero.dev/skills-to-tickets)，然后每个工单运行一次 `/implement` |
| 规格说明已经确定，且实现范围较小 | 直接针对规格说明运行 `/implement` |
| 工作只存在于刚刚结束的对话中，而且仍然很小 | 在同一窗口中直接运行 `/implement` |
| 尚未在任何地方记录 | 使用 [grill-with-docs](https://aihero.dev/skills-grill-with-docs)，如果没有代码库则使用 [grill-me](https://aihero.dev/skills-grill-me) |
| 想先编写测试，实现某个具体行为，但没有规格说明 | 直接使用 [tdd](https://aihero.dev/skills-tdd) |
| 已经完成实现，希望进行审查 | 直接使用 [code-review](https://aihero.dev/skills-code-review) |

值得指出同一会话的情况，因为技能正文开头没有涵盖这种情形。`SKILL.md` 写着"规格说明或工单"，会促使[模型](https://www.aihero.dev/ai-coding-dictionary/model)寻找实际上不存在的文件。如果计划只存在当前聊天中，调用技能时要说明这一点。

## 前置条件

`implement` 会将内容提交到当前分支。它不会创建分支，也不会询问你。开始前，确认当前所在分支就是你希望用于此次工作的分支。

如果工单由 [to-tickets](https://aihero.dev/skills-to-tickets) 生成，则 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 已配置工单所在的跟踪系统。`code-review` 会读取相同的配置，在收尾时查找发起工作的规格说明。

## 每次运行会做什么

一次运行包含五个步骤，顺序如下：

1. 阅读工单或规格说明，并确定接缝。
2. 使用 [tdd](https://aihero.dev/skills-tdd) 在事先约定的接缝处测试，一次一个红绿切片。
3. 经常执行类型检查，并在过程中运行单个测试文件。
4. 最后运行一次完整测试套件。
5. 运行 [code-review](https://aihero.dev/skills-code-review)，然后提交到当前分支。

一次运行只处理一个工单。[to-tickets](https://aihero.dev/skills-to-tickets) 生成的工单是曳光弹式的纵向切片，大小适合一个全新的[上下文窗口](https://www.aihero.dev/ai-coding-dictionary/context-window)，因此预期节奏是：清理上下文、实现一个工单、提交、再次清理上下文。每个工单都是独立完整的，因此可以舍弃先前工单的上下文。

## 事先约定的接缝

此技能的核心概念是**接缝**：在不深入内部的情况下观察行为的公开边界。测试应位于接缝处。在编写代码前约定接缝，测试才能长期有效；你也可以在不修改测试的情况下重写底层实现。

"事先约定"很重要，但也是此技能最薄弱的地方。`implement` 自身不会约定接缝。会要求用户约定接缝的是 `tdd`，而且它会拒绝在未经确认的接缝处编写测试。因此，实际约定要么发生在上游规格说明中，要么发生在此次运行的第一轮交流中。如果两处都没有约定，运行就会变成"直接写代码"，而没有任何警告。将接缝写入规格说明才能避免这种情况。

## 常见问题

**它结束了，但我的工单仍处于未关闭状态，验收标准也未勾选。**

这是预期行为。`implement` 没有完成收尾步骤。它在提交时结束，不会处理工作项。无论使用 GitHub Issues 还是本地 Markdown 跟踪系统，情况都相同，因此不是跟踪系统集成问题。它也不会处理 `code-review` 提出的发现，不会勾选发起议题中的 `- [ ]` 项。你要自行关闭工单并核对验收标准。这在依赖链中尤其重要，因为 `to-tickets` 会将所有阻塞项均已关闭的工单定义为前沿工单。如果没有任何工单关闭，就不会有任何工单被明显解除阻塞。

**可以一次交给它所有工单，或让它并行运行多个工单吗？**

不能使用 `/implement` 这样做：一次调用只处理一个工单。要在一次运行中完成整份规格说明，请使用 [implement-spec](https://aihero.dev/skills-implement-spec)；它会将就绪前沿上的每个工单交给各自在独立 worktree 中运行的[子代理](https://www.aihero.dev/ai-coding-dictionary/subagent)，然后将结果合并到一个集成分支。在同一个检出目录中同时运行多个 `/implement` 会话，不只是没有支持那么简单，实际风险更大。一份实地报告描述了一个下午处理三个议题时，同一会话的 `git commit --amend` 修改了另一个会话的提交，暂存内容从 `refs/stash` 消失，提交还落到了错误分支。各会话共用一个工作目录、一个索引和一个 HEAD。用户使用 git worktree 作为替代办法，但 worktree 之间仍共用 `refs/stash`，所以单靠 worktree 不能解决暂存内容问题。

**它能打开 PR 而不是提交吗？**

没有内置此功能。它会直接提交到当前分支。有人认为它过于主动，因为他们还没验证代码是否有效，代码就已经提交了。目前没有配置标记，也没有 PR 模式。有人会在调用时另行说明("提交到分支并创建 PR")，或编辑本地技能副本来覆盖此行为。代理编写 PR 时，[pr](https://aihero.dev/skills-pr) 会提供正文格式。

**`code-review` 说它看不到我的更改。**

`code-review` 检查 `git diff <fixed-point>...HEAD`，不包括暂存区或工作区更改。`implement` 会在提交之前运行它，所以除非已经存在中间提交，否则没有差异可供审查。多个人报告过此问题，目前两侧都未修复。先提交，再从创建分支时的参照点进行审查。

另外，有人不希望在一次运行内部审查，因为代理审查自己刚刚写出的代码时会偏向自己的方案。另一种可行方式是在新会话中，针对固定参照点运行 [code-review](https://aihero.dev/skills-code-review)。该技能也将两个审查方面分配给不同子代理，原因同样是避免偏见。

**一个工单花了 150k 个词元。我用错了吗？**

可能没有。工单更可能是太大了。一次运行要探索代码库、针对每个接缝执行红绿循环、运行完整测试套件并进行审查，因此复杂工单超过 100k 个[词元](https://www.aihero.dev/ai-coding-dictionary/token)很常见，并不代表系统出错。应在上游解决：在 [to-tickets](https://aihero.dev/skills-to-tickets) 中合理控制工单规模，让每个工单适合一个新的上下文窗口。如果单个工单持续超出此规模，就拆分工单，而不是提高[推理力度](https://www.aihero.dev/ai-coding-dictionary/effort)等级。

**在新会话中运行 `/implement #2`，结果却处理了完全无关的内容。**

代理可能将 `#2` 解释为上下文中的其他编号列表，例如待办文件或检查清单，而不是已配置的跟踪系统。`implement` 现在会从议题跟踪系统获取传入的引用，并在开始前说明工单标题；引用不明确时会询问。确认标题与目标工单相符。传入议题 URL 或 `owner/repo#2` 就能消除歧义。

## 判断是否有效

- 会话开始时会先阅读工单或规格说明，并重述要构建什么，而不是询问你要构建什么。
- 执行记录中能看到实际调用了 `tdd` Skill 工具，而不只是差异中出现了测试。
- 运行期间多次执行类型检查和单个测试文件，并在临近结束时运行一次完整测试套件。
- 无需你提醒继续，它就会在当前分支上完成提交。
- 差异只包含一个工单的工作：它是贯穿各层的纵向切片，而不是把几个工单合在一起。

## 它在工作流程中的位置

`implement` 是主流程链中的实现步骤：

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review → retro
```

相邻技能包括 [to-tickets](https://aihero.dev/skills-to-tickets)，它生成此技能要实现的工单，并声明决定执行顺序的阻塞边；[tdd](https://aihero.dev/skills-tdd)，它在每个接缝处提供测试流程；以及 [code-review](https://aihero.dev/skills-code-review)，它在提交前运行。此技能位于规划技能之后，并信任上游结果。它不会重新验证接手内容的结构，因此组织不当的任务图或横向分层的工单都会照原样实现。

这也是 [wayfinder](https://aihero.dev/skills-wayfinder) 将自身接入 [to-spec](https://aihero.dev/skills-to-spec) 而不是把计划图直接交给 `implement` 的原因。只有当图中的工作量最终很小时，才直接从图开始运行 `implement`。

如果不确定自己处于哪种流程中，[ask-matt](https://aihero.dev/skills-ask-matt) 会在整组技能中进行引导。
