---
name: implement
description: "根据规格说明或一组工单实现一项工作。"
disable-model-invocation: true
---

实现用户在规格说明或工单中描述的工作。

如果用户提供工单引用，请从 issue tracker 获取内容，并在开始前说明工单标题。如果引用不明确，请询问。

在可行时，于事先约定的接缝处调用 Skill 工具并使用 `tdd`。

定期运行类型检查，定期运行单个测试文件，并在最后运行完整测试套件。

完成后，调用 Skill 工具并使用 `code-review` 审查工作。

将工作提交到当前分支。
