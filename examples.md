# 调用示例

本文件提供 Skill 的典型调用场景与对应工作流，供智能体参考。

---

## 示例 1：生成新的技术方案文档

**用户输入**：
> 请用三位一体格式生成《订单系统重构技术方案》文档，包含：背景、整体架构图（4 层）、核心模块时序图、关键代码示例（Python/SQL）、上线步骤流程图。

**智能体应执行的工作流**：

1. 参考 [example.html](example.html) 的骨架与写法（不是复制该文件）
2. 生成顶部导航 `<h1>` → "订单系统重构技术方案"
3. 生成主标题区
4. 按章节生成内容：
    - 一、背景 → 富文本章节（`prose-custom`）
    - 二、整体架构 → **Tailwind HTML 多层架构图**（4 层等宽，参见 reference.md §1）
    - 三、核心模块 → Mermaid `sequenceDiagram`
    - 四、代码示例 → Prism.js `language-python` / `language-sql`
    - 五、上线流程 → Mermaid `flowchart TD` 含判断分支
5. 保留 CDN、`exportToPDF`、打印 CSS、Mermaid 居中规则
6. 输出完整单文件 HTML

---

## 示例 2：基于现有 ai_doc.HTML 增加新章节

**用户输入**：
> 在现有文档中追加一节"七、安全设计"，包含权限矩阵表格 + 鉴权流程时序图。

**智能体应执行的工作流**：

1. 用 `read_file` 读取 [ai_doc.HTML](file:///d:/Document/4.AIStudy/12.SKILLS/1.AI-Doc/ai_doc.HTML)
2. 在原"六、常见问题" 章节之前**插入**新 `<section>`：

```html
<section class="mb-12 bg-white rounded-xl shadow-sm p-8">
    <h2 class="text-2xl font-bold text-gray-900 mb-6">七、安全设计</h2>
    <h3 class="text-xl font-semibold text-gray-800 mb-4">7.1 权限矩阵</h3>
    <!-- 表格 -->
    <h3 class="text-xl font-semibold text-gray-800 mb-4 mt-6">7.2 鉴权流程</h3>
    <div class="border rounded-lg p-4 bg-gray-50">
        <pre class="mermaid">
sequenceDiagram
    ...
        </pre>
    </div>
</section>
```

3. 同步将原"六、常见问题"重新编号为"八、常见问题"（如有跨节引用，全部更新）
4. 用 `search_replace` 工具完成上述插入与重编号

---

## 示例 3：将复杂架构图从 Mermaid 改为 Tailwind HTML

**触发条件**：用户反馈"架构图各层宽度不对齐"或"层级间距不齐"。

**智能体应执行的工作流**：

1. 定位原 `<pre class="mermaid">` 块中的 `flowchart TB` + 多 `subgraph` 结构
2. 改写为 [reference.md](reference.md) §1 中的 Tailwind HTML 等宽方案
3. 关键检查：
    - 每层使用 `flex` + 左侧固定宽度 `w-10` 标签 + 右侧 `flex-1` 内容
    - 层级颜色使用同一色族的 `100`/`50` 浓度（标签栏深、内容区浅）
    - 使用 `style="writing-mode:vertical-rl;letter-spacing:0.4em;"` 实现纵向层名
    - 子模块用 `border-dashed` 区分嵌套
4. 在图下方补充提示框：说明为何使用 Tailwind HTML 而非 Mermaid

---

## 示例 4：添加交互式界面原型

**用户输入**：
> 在文档末尾加一个"管理后台原型"，含侧边栏导航 + 顶部搜索 + 数据表格。

**智能体应执行的工作流**：

1. 在合适章节内插入：

```html
<div class="border rounded-lg overflow-hidden shadow-lg">
    <!-- 标题栏（macOS 风格三色按钮） -->
    <div class="bg-gray-800 text-white px-4 py-2 flex items-center">
        <div class="flex space-x-2 mr-4">
            <div class="w-3 h-3 rounded-full bg-red-500"></div>
            <div class="w-3 h-3 rounded-full bg-yellow-500"></div>
            <div class="w-3 h-3 rounded-full bg-green-500"></div>
        </div>
        <span class="text-sm">管理后台 - 原型演示</span>
    </div>
    <!-- 内容区 -->
    <div class="flex h-[600px]">
        <!-- 侧边栏 -->
        <aside class="w-56 bg-gray-50 border-r p-4">...</aside>
        <!-- 主内容 -->
        <main class="flex-1 p-6">...</main>
    </div>
</div>
```

2. 使用真实 HTML 控件（`<input>`、`<button>`、`<select>`、`<table>`）保留交互性
3. 必要时添加内联 `onclick` 事件演示交互

---

## 示例 5：仅生成代码块片段嵌入

**用户输入**：
> 在 3.7 节追加一段 Go 代码示例。

**智能体应执行的工作流**：

1. 检查 head 是否已加载 Go Prism 组件，若无则补加：
    ```html
    <script src="https://cdn.jsdelivr.net/npm/prismjs@1.29.0/components/prism-go.min.js"></script>
    ```
2. 插入代码块：

```html
<h3 class="text-xl font-semibold text-gray-800 mb-3 mt-6">3.7 Go</h3>
<div class="code-block-wrapper">
    <div class="code-block-header">
        <span class="lang-tag"><span class="lang-dot"></span>go</span>
        <button class="code-copy-btn" onclick="copyCode(this)">复制</button>
    </div>
<pre class="language-go"><code class="language-go">package main

import "fmt"

func main() {
    fmt.Println("Hello")
}</code></pre>
</div>
```

3. 严格检查代码内的 `<` `>` `&` 已转义

---

## 提示词样例（给最终用户使用）

```
请使用 ai-doc-html Skill 生成一个完整的单文件 HTML 文档：

【文档标题】[填写]
【目标读者】[填写]
【核心章节】
1. [章节1标题及要点]
2. [章节2标题及要点]
...

【图表需求】
- 架构图：[简单/复杂多层]，描述 [...]
- 流程图：[是否含判断分支]，描述 [...]
- 时序图：[参与方与交互过程]

【代码示例语言】[js/python/java/...]

【界面原型】[是/否，描述]

【其他要求】[配色、风格、品牌等]
```

智能体收到后会自动按照 SKILL.md 工作流，参考 example.html 的写法重新生成一份独立的 HTML。
