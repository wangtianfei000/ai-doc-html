---
name: ai-doc-html
description: 使用「三位一体」格式（HTML 容器 + Mermaid 图表 + Tailwind CSS 组件）生成单文件 HTML 技术文档。适用于：用户需要生成技术方案、设计文档、架构文档、产品规格说明、API 文档，或任何包含架构图、流程图、代码块、界面原型、交互元素的富媒体文档；以及用户提到用户提到类似表述："美观的文档"、"图文并茂的文档"、"能嵌图表/原型的文档"、"浏览器可直接打开的文档"、"替代 Markdown 的富文档"「AI 文档」「ai_doc」「单文件 HTML 文档」「技术方案文档」等关键词的场景。
---

# AI 文档生成（三位一体最优解）

## 核心理念

使用**单文件 HTML** 作为统一载体，同时承载三种内容：

1. **富文本文档**：HTML 标签 + Tailwind CSS 实现专业排版
2. **架构图/流程图**：Mermaid CDN 原生渲染，与文档同源
3. **界面原型**：Tailwind 组件直接嵌入，支持真实交互

输出物为 **一个独立 .html 文件**，零依赖（CDN）、Git 友好、浏览器原生预览、可一键导出 PDF。

## 何时使用本 Skill

触发场景：
- 用户要求生成技术方案 / 架构设计 / 产品需求 / API / 测试报告等富文档
- 涉及架构图、流程图、时序图等图表
- 需要嵌入代码块（多语言、语法高亮）
- 需要嵌入界面原型或交互式组件
- 用户提到类似表述："美观的文档"、"图文并茂的文档"、"能嵌图表/原型的文档"、"浏览器可直接打开的文档"、"替代 Markdown 的富文档"、"三位一体"、"AI 文档"、"单文件 HTML"、"ai_doc"

## 使用流程

### 第一步：参考示例

完整可运行示例位于 [example.html](example.html)（约 1020 行）。**该文件仅作风格、结构与最佳实践的参考**，用于演示 CDN 配置、章节骨架、图表选型、代码块、PDF 导出等惯用法。**不要把示例文件本身当作要交付的产物**——智能体应**根据用户的真实需求重新生成一份独立的 HTML**，按需借鉴示例中的写法。

### 第二步：依据需求重新生成

1. 先解析用户需求：文档主题、需要的章节、是否含架构图 / 流程图 / 代码块 / 界面原型
2. 沿用示例中的整体骨架（`<!DOCTYPE html>` → CDN → Tailwind 配置 → 自定义样式 → 打印 CSS → `<body>` → `<nav>` → `<main>` → `<footer>` → `<script>`）
3. 按需挑选并复用示例中的「构建模块」（见下方速查），章节顺序、标题、内容**根据用户需求自定**，不必与示例一一对应
4. 必须保留以下基础设施：所有 CDN、Tailwind 配置、自定义样式、Prism.js、`@media print` 打印 CSS、`mermaid.initialize`、`copyCode`、`exportToPDF` 函数

### 第三步：图表选型决策

| 场景 | 选择 | 原因 |
|---|---|---|
| 通用流程图 / 时序图 / 树状图 | **Mermaid** `<pre class="mermaid">` | 文本即图、AI 友好 |
| 含**多重判断分支与回流**的业务流程图 | **Mermaid** `flowchart TD` + `classDef` | 自动布局，决策点清晰 |
| **复杂多层架构图**（≥3 层、子系统嵌套、需层级等宽对齐） | **Tailwind HTML** `flex` + `grid` | Mermaid `subgraph` 按内容自适应宽度，无法保证层级等宽 |
| 单层简单方框图 | Mermaid 即可 | 简单 |
| 界面原型 | Tailwind HTML 组件 | 真实可交互 |

> **关键判断**：当架构图需要"严格等宽分层"（如 ERP 四层架构、应用分层模型），**禁止使用 Mermaid**，必须改用 Tailwind HTML。

## 构建模块速查

### 1. 富文本章节骨架

```html
<section class="mb-12 bg-white rounded-xl shadow-sm p-8">
    <h2 class="text-2xl font-bold text-gray-900 mb-6">一、章节标题</h2>
    <div class="prose-custom">
        <p>正文段落...</p>
        <h3>3.1 子标题</h3>
        <ul><li>列表项</li></ul>
    </div>
</section>
```

### 2. Mermaid 图表

```html
<div class="border rounded-lg p-4 bg-gray-50">
    <pre class="mermaid">
graph TD
    A[开始] --> B{判断}
    B -->|是| C[处理]
    B -->|否| D[结束]
    </pre>
</div>
```

支持 `graph TD`、`flowchart TD`、`sequenceDiagram`、`classDiagram`、`erDiagram` 等。Mermaid SVG 已通过全局 CSS 居中（`.mermaid svg { display:block; margin:0 auto; }`）。

### 3. 复杂多层架构图（Tailwind HTML）

参见 [reference.md](reference.md) 第 1 节"复杂多层架构图示例"。核心结构：

```html
<div class="border-2 rounded-lg overflow-hidden">
  <div class="flex border-b-2">
    <div class="w-10 bg-X-100 flex items-center justify-center">
      <span style="writing-mode:vertical-rl;letter-spacing:0.4em;">层级名</span>
    </div>
    <div class="flex-1 bg-X-50 p-3">
      <!-- grid grid-cols-N 内容 -->
    </div>
  </div>
  <!-- 重复其他层 -->
</div>
```

通过左侧固定宽度标签 + 右侧 `flex-1` 内容区，**保证所有层等宽**。

### 4. 代码块（Prism.js + 复制按钮）

```html
<div class="code-block-wrapper">
    <div class="code-block-header">
        <span class="lang-tag"><span class="lang-dot"></span>typescript</span>
        <button class="code-copy-btn" onclick="copyCode(this)">复制</button>
    </div>
<pre class="language-typescript"><code class="language-typescript">// 代码内容</code></pre>
</div>
```

**关键**：`<` `>` `&` 必须 HTML 转义为 `&lt;` `&gt;` `&amp;`。已支持语言：js、ts、python、java、bash、json、sql、yaml。

### 5. 界面原型

直接使用 Tailwind 组件（侧边栏 + 工具栏 + 编辑区结构），可参见 [example.html](example.html) 第五章"界面原型示例"。

### 6. PDF 导出

按钮已内置 `onclick="exportToPDF()"`，调用 `window.print()` 触发浏览器另存为 PDF。已配置 `@media print` 隐藏导航/页脚/按钮、避免分页切割图表。**保留示例中的打印 CSS 不变**。

## 内容编写规范

1. **章节编号**：使用中文序号"一、二、三..."，子节用"1.1, 1.2..."
2. **配色**：主色调遵循 Tailwind 配置（primary=blue、secondary=purple、success=green、warning=amber、danger=red）
3. **章节卡片**：每章用 `<section class="mb-12 bg-white rounded-xl shadow-sm p-8">` 包裹
4. **图表前后留白**：图表上方加描述段落，下方加 `案例要点` 提示框
5. **响应式**：所有自定义组件保留 Tailwind 响应式前缀（`sm:`, `md:`, `lg:`）

## 输出要求

- 必须输出**完整单文件 HTML**，可直接保存为 `.html` 双击打开
- 保留所有 CDN：Tailwind v4、Mermaid v11、Prism 1.29
- 保留 `mermaid.initialize`、`copyCode`、`exportToPDF`、打印 CSS
- 不引用任何本地资源
- 占位图统一使用 `https://picsum.photos/`

## 常见错误规避

| 错误 | 后果 | 规避 |
|---|---|---|
| 在 `<script>` 中误转义 `=>`、`&&` | JS 语法错误 | `<script>` 内部保持原始符号 |
| 多层 Mermaid `subgraph` 表达分层架构 | 各层宽度不一致 | 改用 Tailwind HTML 等宽方案 |
| 代码块中遗漏 HTML 转义 | 浏览器误解析为标签 | `<` `>` `&` 必须转义 |
| 删除 `@media print` CSS | PDF 导出排版混乱 | 永远保留 |
| 用 `useMaxWidth: false` 覆盖 Mermaid 配置 | SVG 不响应式 | 沿用示例默认配置 |

## 相关文件

- [example.html](example.html) — 完整可运行的 HTML 示例，仅作风格与最佳实践参考，不直接交付
- [reference.md](reference.md) — 复杂场景参考（多层架构图、Mermaid 进阶语法、Prism 扩展语言）
- [examples.md](examples.md) — 常见调用场景与提示词样例
