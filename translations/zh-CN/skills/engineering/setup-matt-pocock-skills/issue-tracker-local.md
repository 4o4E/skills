# 议题跟踪系统：本地 Markdown

本仓库的问题和规格说明以 Markdown 文件形式存放在 `.scratch/` 中。

## 约定

- 每个功能使用一个目录：`.scratch/<feature-slug>/`
- 规格说明位于 `.scratch/<feature-slug>/spec.md`
- 每张实现工单对应 `.scratch/<feature-slug>/issues/<NN>-<slug>.md` 中的一个文件，编号从 `01` 开始；不得把所有工单放在一个合并文件中
- 分流状态记录在每个问题文件顶部附近的 `Status:` 行中（角色字符串参见 `triage-labels.md`）
- 评论和对话历史追加到文件底部的 `## Comments` 标题下

## 技能提到“发布到议题跟踪系统”时

在 `.scratch/<feature-slug>/` 下创建新文件（如有需要，创建该目录）。

## 技能提到“获取相关工单”时

读取引用路径中的文件。用户通常会直接提供路径或问题编号。

## Wayfinding 操作

由 `/wayfinder` 使用。**地图**是一个文件，每张工单对应一个**子**文件。

- **地图**：`.scratch/<effort>/map.md`（包含 Notes / Decisions-so-far / Fog 正文）。
- **子工单**：`.scratch/<effort>/issues/NN-<slug>.md`，编号从 `01` 开始，正文写明问题。`Type:` 行记录工单类型（`research`/`prototype`/`grilling`/`task`）；`Status:` 行记录 `claimed`/`resolved`。
- **阻塞关系**：顶部附近的 `Blocked by: NN, NN` 行。当它列出的所有文件都已是 `resolved` 时，工单不再受阻。
- **前沿**：扫描 `.scratch/<effort>/issues/`，查找仍开放、没有阻塞且尚未认领的文件；编号最小者优先。
- **认领**：将 `Status: claimed` 写入文件并保存，然后才开始任何工作。
- **解决**：在 `## Answer` 标题下追加答案，将 `Status: resolved` 写入文件，然后在 `map.md` 的 Decisions-so-far 中追加上下文指针。
