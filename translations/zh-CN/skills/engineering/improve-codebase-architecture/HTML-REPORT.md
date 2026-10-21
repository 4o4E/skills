# HTML 报告格式

架构审查应渲染为单个独立 HTML 文件，并保存在操作系统临时目录中。Tailwind 和 Mermaid 均来自 CDN。Mermaid 可可靠地呈现图形结构的图示；手工编写的 div 和内嵌 SVG 用于更具编辑风格的图示(体量图、剖面图)。两者应混合使用：不要凡事都依靠 Mermaid，否则报告会显得千篇一律。

## 框架

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Architecture review for {{repo name}}</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script type="module">
      import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs";
      mermaid.initialize({ startOnLoad: true, theme: "neutral", securityLevel: "loose" });
    </script>
    <style>
      /* small custom layer for things Tailwind doesn't cover cleanly:
         dashed seam lines, hand-drawn-feeling arrow heads, etc. */
      .seam { stroke-dasharray: 4 4; }
      .leak { stroke: #dc2626; }
      .deep { background: linear-gradient(135deg, #0f172a, #1e293b); }
    </style>
  </head>
  <body class="bg-stone-50 text-slate-900 font-sans">
    <main class="max-w-5xl mx-auto px-6 py-12 space-y-12">
      <header>...</header>
      <section id="candidates" class="space-y-10">...</section>
      <section id="top-recommendation">...</section>
    </main>
  </body>
</html>
```

## 页眉

显示仓库名称、日期和简洁图例：实线框表示模块，虚线表示接缝，红色箭头表示泄漏，粗深色框表示深层模块。不要写介绍段落，直接展示候选项。

## 候选项卡片

图示是卡片的重点。文字应当简洁、平实，并自然使用词汇表中的术语(来自 `/codebase-design` 技能)。

每个候选项都使用一个 `<article>`：

- **标题**：简短说明加深模块的方式(例如"合并 Order 接收流程")。
- **徽章行**：建议强度(`Strong` = 翠绿色，`Worth exploring` = 琥珀色，`Speculative` = 石板色)，以及依赖类别标签(`in-process`、`local-substitutable`、`ports & adapters`、`mock`)。
- **文件**：使用等宽字体的文件列表，`font-mono text-sm`。
- **修改前 / 修改后图示**：核心内容。并排显示两栏。见下方模式。
- **问题**：一句话说明痛点。
- **解决方案**：一句话说明更改内容。
- **收益**：使用项目符号，每条最多 6 个单词。例如："测试触及一个接口"、"Pricing 逻辑不再泄漏"、"删除 4 个浅层包装层"。
- **决定提示框**(如适用)：用一行文字指出与已确认的 OpenSpec 决定冲突。

不要写解释段落。如果需要用一整段来解释图示，就重新绘制图示。

## 图示模式

选择适合候选项的模式，也可以混合使用。不要让所有图示都长得一样，多样性也是目的之一。

### Mermaid 图(适用于依赖关系 / 调用流程)

当要表达"X 调用 Y，Y 调用 Z，看看有多乱"时，使用 Mermaid 的 `flowchart` 或 `graph`。将它放进 Tailwind 样式卡片，避免显得格格不入。使用 `classDef` 将泄漏边缘标成红色，并将深层模块标成深色。对于"修改前：往返 6 次；修改后：往返 1 次"的情况，顺序图效果很好。

```html
<div class="rounded-lg border border-slate-200 bg-white p-4">
  <pre class="mermaid">
    flowchart LR
      A[OrderHandler] --> B[OrderValidator]
      B --> C[OrderRepo]
      C -.leak.-> D[PricingClient]
      classDef leak stroke:#dc2626,stroke-width:2px;
      class C,D leak
  </pre>
</div>
```

### 手工绘制的方框与箭头(Mermaid 布局不合适时使用)

使用带边框和标签的 `<div>` 表示模块。使用内嵌 SVG 的 `<line>` 或 `<path>` 元素绘制箭头，并将它们绝对定位在相对定位的容器上方。如果希望"修改后"的图示呈现为一个粗边框的深层模块，内部内容颜色变淡，则应采用此方法，因为 Mermaid 无法以合适的视觉权重来呈现这种结构。

### 剖面图(适合展示浅层结构)

将水平条带堆叠(`h-12 border-l-4`)，展示调用必须经过的层级。修改前：6 个薄层，各自毫无作用。修改后：1 个厚条带，标注合并后的职责。

### 体量图(适合展示"接口和实现一样宽")

每个模块使用两个矩形：一个表示接口外在形式，一个表示实现。修改前：接口矩形几乎与实现矩形一样高(浅层)。修改后：接口矩形很矮，实现矩形很高(深层)。

### 调用图折叠

修改前：将函数调用树显示为嵌套方框。修改后：将同一棵树折叠进一个方框，并在其中淡化显示现在已成为内部调用的部分。

## 样式指引

- 倾向编辑排版风格，不要像企业控制面板。使用充足留白。标题可选用衬线字体(`font-serif` 与石板色系搭配效果良好)。
- 谨慎使用颜色：一种强调色(翠绿色或靛蓝色)，再加上表示泄漏的红色和表示警告的琥珀色。
- 图示高度约为 320px，让修改前 / 修改后可以舒适地并排显示，无需滚动。
- 在图示内部使用 `text-xs uppercase tracking-wider` 标示模块，让它们看起来像示意图，而不是用户界面。
- 只使用 Tailwind CDN 和 Mermaid ESM 导入脚本。报告其他部分都是静态内容，不包含应用代码或任何交互，只有 Mermaid 自身的渲染功能。

## 首要建议章节

使用一张较大的卡片。写出候选项名称、一句话说明原因，以及指向其卡片的锚点链接。仅此而已。

## 语气

语言通俗简练；架构名词和动词直接取自 `/codebase-design` 技能。简练不代表可以偏离用词。

**准确使用：** module、interface、implementation、depth、deep、shallow、seam、adapter、leverage、locality。

**绝不替换为：** component、service、unit(表示 module 时)· API、signature(表示 interface 时)· boundary(表示 seam 时)· layer、wrapper(表示 module 时)。

**符合风格的表述：**

- "Order 接收模块较浅：接口几乎与实现一样复杂。"
- "Pricing 会跨接缝泄漏。"
- "加深模块：一个接口，一个测试位置。"
- "两个适配器足以证明接缝合理：生产环境使用 HTTP，测试使用内存适配器。"

**收益项目符号**要使用词汇表术语说明收益：*"局部集中度：错误集中在一个模块中"*、*"调用方收益：一个接口，N 个调用位置"*、*"接口缩小；实现吸收包装层"*。不要写 *"更易于维护"* 或 *"代码更整洁"*，因为这些术语不在词汇表中，不值得占用篇幅。

不要含糊其辞，不要先说无关紧要的话，也不要使用"值得注意的是……"。如果一句话可以改成项目符号，就改成项目符号。如果项目符号可以删掉，就删除它。如果 `/codebase-design` 词汇表中没有某个术语，应优先使用已有术语，不要发明新词。
