# 范围

此代码仓库包含我日常使用的技能，并经过精心筛选：欢迎提出想法，但每项想法都要按照以下标准评估。议题用于追踪技能变更。问题和讨论请提交至 [GitHub Discussions](https://github.com/mattpocock/skills/discussions)。

## 评估标准

只有同时符合以下两项标准，想法或反馈议题才会继续开放：

1. **观察到的故障。** 说明真实会话中出现的问题：你运行了什么、技能做了什么、你期望它做什么。假设性的改进（“如果……会更好”）不符合此项要求。
2. **符合理念。** 内容不能与 [`.out-of-scope/`](../../.out-of-scope) 或过去已拒绝的提案相符，也不能是配置选项、特定运行环境的分支，或只适合某个人工作流程的调整。这些内容应放在你自己的 `CLAUDE.md` / `AGENTS.md` 中，或放到分支版本中（skills.sh 会提供可编辑副本）。

规模无关紧要：只改一个词的修正与重写整份内容适用相同标准。回应和表示赞同的评论不计入评估。

## 按类别划分

- **`engineering/`、`productivity/`、`in-progress/`**：适用上述标准。
- **`misc/`**：已冻结，不再维护。所有议题都会关闭。参阅 [`frozen-misc-skills.md`](../../.out-of-scope/frozen-misc-skills.md)。
- **新技能**：提案和贡献已关闭。如果某种行为可以由现有技能组合完成，就不会增加新技能。参阅 [`new-skills.md`](../../.out-of-scope/new-skills.md)。

## 已有结论

每份 [`.out-of-scope/`](../../.out-of-scope) 文件都记录一个已拒绝的概念及拒绝原因。提交之前请先阅读：

- [`frozen-misc-skills.md`](../../.out-of-scope/frozen-misc-skills.md)
- [`harness-name-collisions.md`](../../.out-of-scope/harness-name-collisions.md)
- [`mainstream-issue-trackers-only.md`](../../.out-of-scope/mainstream-issue-trackers-only.md)
- [`native-question-tool.md`](../../.out-of-scope/native-question-tool.md)
- [`new-skills.md`](../../.out-of-scope/new-skills.md)
- [`question-limits.md`](../../.out-of-scope/question-limits.md)
- [`setup-skill-verify-mode.md`](../../.out-of-scope/setup-skill-verify-mode.md)
- [`subagent-recursion.md`](../../.out-of-scope/subagent-recursion.md)

## 未明确的议题

如果无法按照评估标准判断某项议题，就进行一轮提问，并加上 `needs-info` 标签。如果报告者在 14 天内没有回复，就关闭该议题。拒绝的议题会以“not planned”理由关闭。
