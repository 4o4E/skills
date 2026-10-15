# 编写代理工作说明

代理工作说明是一条结构化评论，在 GitHub 议题或 PR 转为 `ready-for-agent` 时发布。它是 AFK 代理工作依据的权威规格说明。原始正文和讨论只是上下文：代理工作说明才是约定。

工作说明描述**代理应该做什么**，这适用于两种对象：对于议题，是从头构建改动；对于 PR，是完成现有差异中剩下的工作：补完功能、填补缺口、处理审查意见。两者遵循相同原则；下方的 PR 示例展示了它们之间的差别。

## 原则

### 持久性优先于精确性

问题可能会在 `ready-for-agent` 状态下停留数天或数周。与此同时，代码库可能发生变化。编写工作说明时，要确保即使文件被重命名、移动或重构，它仍然有用。

- **应该**描述接口、类型和行为合约
- **应该**指出代理应查找或修改的具体类型、函数签名或配置形状
- **不要**引用文件路径：它们很快会过时
- **不要**引用行号
- **不要**假设当前实现结构会一直保持不变

### 描述行为，而不是步骤

描述系统**应该做什么**，而不是**应该如何实现**。代理会重新探索代码库并自行作出实现决定。

- **良好：**"`SkillConfig` 类型应接受可选的 `schedule` 字段，其类型为 `CronExpression`"
- **不佳：**"打开 src/types/skill.ts 并在第 42 行添加 schedule 字段"
- **良好：**"用户在没有参数的情况下运行 `/triage` 时，应看到需要处理的问题摘要"
- **不佳：**"在主处理函数中添加 switch 语句"

### 完整的验收标准

代理需要知道什么时候算完成。每份代理工作说明都必须包含具体且可测试的验收标准，并且每项标准都应能独立验证。

- **良好：**"运行 `gh issue list --label needs-triage` 会返回已完成初步分类的问题"
- **不佳：**"分流应该正确运行"

### 明确范围边界

说明不在范围内的事项，避免代理过度美化实现，或对相邻功能作出假设。

## 模板

```markdown
## Agent Brief

**Category:** bug / enhancement
**Summary:** 用一行描述需要实现的内容

**Current behavior:**
描述现在发生的情况。对于错误，说明出错行为。
对于功能增强，说明新功能建立在什么现状之上。

**Desired behavior:**
描述代理工作完成后的行为。
明确说明边缘情形和错误情况。

**Key interfaces:**
- `TypeName`：需要修改什么以及原因
- `functionName()` return type：当前返回什么以及应该返回什么
- Config shape：需要的任何新配置选项

**Acceptance criteria:**
- [ ] 具体、可测试的标准 1
- [ ] 具体、可测试的标准 2
- [ ] 具体、可测试的标准 3

**Out of scope:**
- 此问题中不应修改或处理的事项
- 可能看似相关但实际属于其他范围的相邻功能
```

## 示例

### 良好的代理工作说明(错误)

```markdown
## Agent Brief

**Category:** bug
**Summary:** 技能描述截断时会从单词中间切断，产生错误输出

**Current behavior:**
当技能描述超过 1024 个字符时，无论是否处于单词边界，都会恰好在
第 1024 个字符处截断。这会导致描述在单词中间结束(例如："Use when the user wants to confi")。

**Desired behavior:**
应当在 1024 个字符之前最近的单词边界处截断，并附加 "..." 表示内容已截断。

**Key interfaces:**
- `SkillMetadata` 类型的 `description` 字段：不需要更改类型，
  但填充该字段的验证/处理逻辑需要遵守单词边界
- 任何读取 SKILL.md frontmatter 并提取描述的函数

**Acceptance criteria:**
- [ ] 少于 1024 个字符的描述保持不变
- [ ] 超过 1024 个字符的描述在
      1024 个字符之前最近的单词边界处截断
- [ ] 截断后的描述以 "..." 结尾
- [ ] 包含 "..." 在内的总长度不超过 1024 个字符

**Out of scope:**
- 更改 1024 字符的限制本身
- 支持多行描述
```

### 良好的代理工作说明(功能增强)

```markdown
## Agent Brief

**Category:** enhancement
**Summary:** 增加 `.out-of-scope/` 目录，以跟踪被拒绝的功能请求

**Current behavior:**
拒绝功能请求时，会使用 `wontfix` 标签关闭问题并添加评论。没有持久记录决定或理由的地方。
未来遇到相似请求时，维护人员必须回忆或搜索以前的讨论。

**Desired behavior:**
应将被拒绝的功能请求记录在 `.out-of-scope/<concept>.md`
文件中，其中包含决定、理由以及所有提出该功能的问题链接。分流新问题时，应检查这些文件以查找匹配内容。

**Key interfaces:**
- `.out-of-scope/` 中的 Markdown 文件格式：每个文件应包含
  `# Concept Name` 标题、`**Decision:**` 行、`**Reason:**` 行，
  以及列出问题链接的 `**Prior requests:**` 清单
- 分流工作流程应尽早读取所有 `.out-of-scope/*.md` 文件，
  并按照概念相似度与新问题进行匹配

**Acceptance criteria:**
- [ ] 以 wontfix 关闭功能时，会在 `.out-of-scope/` 中创建或更新文件
- [ ] 文件包含决定、理由以及已关闭问题的链接
- [ ] 如果已经存在匹配的 `.out-of-scope/` 文件，则将新问题
      追加到其"Prior requests"清单，而不是创建重复文件
- [ ] 分流期间会检查现有 `.out-of-scope/` 文件，并在新问题匹配
      以前被拒绝的请求时显示相关文件

**Out of scope:**
- 自动匹配(由人确认是否匹配)
- 重新打开以前被拒绝的功能
- 错误报告(只有功能增强被拒绝时才使用 `.out-of-scope/`)
```

### 良好的代理工作说明(PR)

对于 PR，"Current behavior"描述差异当前的状态，工作说明要求代理完成或修复它，而不是从头构建。

```markdown
## Agent Brief

**Category:** enhancement
**Summary:** 完成贡献者为 `triage list` 添加的 `--json` 输出标记

**Current behavior:**
PR 添加了 `--json` 标记，将问题清单序列化为 JSON。正常流程可以运行，差异也符合项目的命令结构。还有两个缺口：错误仍以人类可读文本输出(而不是 JSON)，并且新标记没有测试覆盖。

**Desired behavior:**
启用 `--json` 时，所有输出(包括错误)都应在 stdout 上输出格式正确的 JSON，
并且命令的退出代码保持不变。未指定标记时，现有的人类可读输出不受影响。

**Key interfaces:**
- 使用 `--json` 时，命令的错误路径应输出 `{ "error": string }`，
  而不是纯文本错误
- 复用 PR 已经添加的序列化器；不要再引入第二个

**Acceptance criteria:**
- [ ] `triage list --json` 在成功和错误情形下都输出有效 JSON
- [ ] 退出代码与非 JSON 命令相同
- [ ] 有一项测试覆盖 `--json` 成功输出和一种错误情况
- [ ] 默认(非 JSON)输出逐字节保持不变

**Out of scope:**
- 在其他命令中添加 `--json`
- 更改 PR 已定义的成功负载 JSON 结构
```

### 不佳的代理工作说明

```markdown
## Agent Brief

**Summary:** 修复分流错误

**What to do:**
分流出了问题。查看主文件并修复它。
第 150 行附近的函数有问题。

**Files to change:**
- src/triage/handler.ts(第 150 行)
- src/types.ts(第 42 行)
```

此工作说明不佳，原因如下：
- 没有类别
- 描述含糊("分流出了问题")
- 引用了很快会过时的文件路径和行号
- 没有验收标准
- 没有范围边界
- 没有说明当前行为与期望行为之间的区别
