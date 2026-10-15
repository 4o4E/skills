# 工程

我日常用于代码工作的技能。

## 由用户调用

只有在你输入技能名称时才会调用(Claude Code：设置 `disable-model-invocation: true`；Codex：在 `agents/openai.yaml` 中设置 `policy.allow_implicit_invocation: false`)。

- **[ask-matt](./ask-matt/SKILL.md)**：询问哪种技能或流程适合当前情况。它会为此代码仓库中由用户调用的技能提供路由。
- **[grill-with-docs](./grill-with-docs/SKILL.md)**：同时建立项目领域模型的追问会话，帮助明确术语，并在对话中更新 `GLOSSARY.md` 和 ADR。
- **[triage](./triage/SKILL.md)**：让 issue 按照由分流角色组成的状态机流转。
- **[improve-codebase-architecture](./improve-codebase-architecture/SKILL.md)**：扫描代码库寻找适合加深模块的机会，以可视化 HTML 报告呈现，再通过追问探讨你选定的机会。
- **[setup-matt-pocock-skills](./setup-matt-pocock-skills/SKILL.md)**：为工程技能配置此代码仓库(issue tracker、分流标签、领域文档结构)。每个代码仓库运行一次。
- **[to-spec](./to-spec/SKILL.md)**：将当前对话转成规格说明并发布到 issue tracker。
- **[to-tickets](./to-tickets/SKILL.md)**：将任何计划、规格说明或对话拆分成一组曳光弹工单，逐项说明阻塞关系；可以写入本地文件，也可以在实际 issue tracker 中建立原生阻塞链接。
- **[implement](./implement/SKILL.md)**：根据规格说明或工单集合开展工作，在预先同意的接缝处使用 `/tdd`，提交前使用 `/code-review` 完成审查。
- **[implement-spec](./implement-spec/SKILL.md)**：在单一集成分支上实施完整规格说明。将工单作为任务拓扑图处理，并发调度实施子代理处理所有已就绪的前沿工单，实现最大并发度，最后通过 `/code-review` 完成审查。
- **[wayfinder](./wayfinder/SKILL.md)**：将大到无法由一个代理会话完成的工作，规划成 issue tracker 中的共享决策工单地图；逐项处理决定，直到清楚该如何到达目标。
- **[retro](./retro/SKILL.md)**：会话结束后提出改进编码代理环境的建议(导航、自动检查、编码规范、指导文件、工具)，优先指出严重程度最高的问题。

## 由模型调用

模型或用户都可以调用(提供丰富的触发描述，让模型能够找到合适的技能)。

- **[prototype](./prototype/SKILL.md)**：制作一次性原型以回答设计问题：状态或逻辑问题可制作一个便于分享的 HTML 文件，或制作多个可切换的界面变体。
- **[diagnosing-bugs](./diagnosing-bugs/SKILL.md)**：针对难以排查的缺陷和性能退化，采用严谨的诊断流程：建立会因该缺陷而变红的反馈回路 → 缩小问题 → 提出假设 → 加入观测 → 修复 → 回归测试。
- **[research](./research/SKILL.md)**：依据高度可信的一手资料调查问题，并将附有引用的 Markdown 文件写入代码仓库，由后台代理运行。
- **[tdd](./tdd/SKILL.md)**：采用红绿重构循环进行测试驱动开发，一次处理一个垂直切片，构建功能或修复问题。
- **[domain-modeling](./domain-modeling/SKILL.md)**：通过质疑术语、使用情境进行压力测试，并在对话中更新 `GLOSSARY.md` 和 ADR，主动建立并明确项目领域模型。
- **[codebase-design](./codebase-design/SKILL.md)**：设计深层模块时遵循共同的纪律和词汇：小型接口、清晰的接缝，并可通过接口进行测试。
- **[code-review](./code-review/SKILL.md)**：从固定基准点审查差异，分为两个方面：**编码标准(Standards)**(是否遵循代码仓库规范，并参照 Fowler 的代码异味基准？)和**规格说明(Spec)**(是否忠实实施源 issue 或规格说明？)；由并行子代理分别执行审查。
- **[pr](./pr/SKILL.md)**：决定 PR 正文的格式：以最精简的图示说明变更，提供前后对比证据证明变更有效，并判断合并风险(单向还是双向决策，以及影响范围)。
- **[wizard](./wizard/SKILL.md)**：制作交互式 Bash 向导，引导人类完成只有他们能做的步骤：配置基础设施、设置凭据或 CI 机密、操作不熟悉的第三方控制台，或执行一次性迁移和切换。
