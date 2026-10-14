# 效率

通用工作流程工具，不限于代码。

## 由用户调用

只有在你输入技能名称时才会调用（Claude Code：设置 `disable-model-invocation: true`；Codex：在 `agents/openai.yaml` 中设置 `policy.allow_implicit_invocation: false`）。

- **[grill-me](./grill-me/SKILL.md)**：围绕计划或设计持续访谈，直到设计树的每个分支都已解决。
- **[handoff](./handoff/SKILL.md)**：将当前对话整理成交接文档，让另一位代理可以继续工作。
- **[teach](./teach/SKILL.md)**：在多次会话中，使用当前目录作为有状态教学工作区，教用户一项新技能或概念。
- **[to-questionnaire](./to-questionnaire/SKILL.md)**：将你无法独自决定的事项转成 Markdown 问卷，交给唯一能够回答的人（由对方异步填写，或在会议中共同填写）。
- **[wait-what](./wait-what/SKILL.md)**：消息没有讲明白时立即调用。代理会补充你缺少的背景，使用平实的英语，并采用 `GLOSSARY.md` 中的词汇重新表达。

## 由模型调用

模型或用户都可以调用（提供丰富的触发描述，让模型能够找到合适的技能）。

- **[grilling](./grilling/SKILL.md)**：持续访谈用户，讨论计划、决定或想法，直到设计树的每个分支都已解决。
- **[writing-for-agents](./writing-for-agents/SKILL.md)**：撰写供代理使用的文档：技能、`AGENTS.md`/`CLAUDE.md`，以及代理通过指针访问的任何文档。
