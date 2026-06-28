---
name: html-design-workflow
description: >-
  CRITICAL: You MUST use this skill ANY time the user mentions anything related
  to HTML files or web pages. THIS INCLUDES modifying/editing/fixing existing HTML
  files, debugging formula rendering issues, and creating review materials —
  even if HTML is only part of a larger task. Trigger on: 做HTML, 做网页, 做页面,
  写HTML, 写网页, 修改html, html坏了, 做个页面, 做个HTML, 帮我写个HTML, 前端设计,
  做组件, 做仪表盘, 做幻灯片, 做PPT, 做演示. ALSO trigger on: 公式不显示, 公式乱码,
  公式渲染, KaTeX, 复习资料HTML, 数学公式, 显示不全, 数学分析, 期末复习, 课件HTML,
  review-ch, "$$"显示问题, 数学符号乱码. ALSO trigger when the user asks to:
  改公式, 加公式, 修公式, 公式炸了, 公式没了, 渲染不对, 页面空白. ALSO trigger on
  user showing an existing HTML file to emulate 学习这个风格, 参考这个页面, 提取风格.
  ALSO trigger when told to create/fix/build/modify any HTML file for any purpose,
  including as part of exam review, study materials, or documentation tasks.
  ANY request involving HTML output MUST trigger this skill without exception.
  When triggered, ALWAYS invoke this Skill tool BEFORE writing any code or
  responding to the user. Read the full SKILL.md and all references before
  starting. DO NOT skip this step — failing to invoke the skill before work
  has caused severe issues in the past, including file corruption and wasted hours.
---

# HTML Design Workflow

Route the user's HTML request to the right approach — then execute. Delegate to
specialist skills (frontend-design, html-presentation, theme-factory) when the
request exceeds this skill's scope.

## When to Use

- User asks for any HTML/CSS/JS artifact (page, doc, slide, dashboard, component)
- User shows an existing HTML file and wants to learn/emulate its style
- User describes a UI idea in Chinese (做网页, 做个页面, 帮我写个前端)

**When NOT to use:**
- Pure backend logic, data processing, or CLI scripts
- The user explicitly wants React/Vue/Next.js component libraries
- Simple CSS tweaks to existing pages (just do it directly)

## Quick Reference

| Type | Go-to approach | Specialist skill |
|------|---------------|------------------|
| 复习资料/章节总结/知识整理 | 分析布局：卡片式章节 + 侧边栏TOC + KaTeX (`references/analysis-layout.md`) | — |
| 复习资料/习题集/模拟考 | 模考布局：试题卡片 + 可展开答案 (`references/mock-exam-layout.md`) | — |
| 打印索引/知识点清单 | 索引布局：多列 + 可编辑下划线 (`references/printable-index-layout.md`) | — |
| 技术文档/手册 | Sidebar + content layout, deep code blocks | — |
| 演示文稿/幻灯片 | Full-screen pages, arrow key navigation | html-presentation |
| 落地页/组件 | Single page, brand-forward design | frontend-design |
| 不确定 | Ask user, then classify | — |

## Style Learning

When the user says 学习这个风格 / 参考这个页面 / 提取风格:
1. Read the file, extract: tone, color palette (hex), fonts, layout patterns, interactions
2. Save analysis to `styleref/<name>.md`
3. Apply the recorded style to the user's next HTML request
4. Check `styleref/` before starting — reuse a stored style if it matches.

---

## Workflow

### Step -1: Backup before modifying ANY existing HTML

Before editing any existing HTML file, create a timestamped backup:
```bash
mkdir -p backup/$(date +%Y-%m-%d) && cp file.html backup/$(date +%Y-%m-%d)/
```
File corruption during HTML editing has happened repeatedly — backups are the only recovery path.

### Step 0: Python LaTeX 转义检查（写 .py 脚本前**必须**读）

**⚠️ 在写任何 Python 脚本之前，先读 `references/python-latex-escape.md`。** 这篇 reference 解释了反斜杠在三个层面（JSON → Python → re.sub）被吃掉的机制和防范方法。

如果不读就直接写脚本，最常见的后果：
- `\frac` 变成 `rac`（JSON 把 `\f` 当换页符吃了）
- `\beta` 变成 `eta`（JSON 把 `\b` 当退格符吃了）
- Python raw string 写对了但忘了 JSON 层，公式全部损坏

**写脚本时的检查清单：**
- [ ] 所有在 JSON 敏感字符 `bfnr\tu` 开头的 LaTeX 命令已加倍（`\\frac` ✅，`\\beta` ✅）
- [ ] Python 字符串用了 raw string（`r'''`）
- [ ] `re.sub` replacement 也用 raw string（`r'\\alpha'`）
- [ ] Python 脚本的输入和输出是不同文件路径，不会读写同文件
- [ ] 写完后用 reference 里的验证脚本检查控制字符

### Step 0: Choose layout

For 复习资料/考试备考 HTML tasks, pick from the three predefined layouts:

| If user wants... | Use layout | Reference |
|-----------------|------------|-----------|
| 章节总结/概念梳理 | 分析布局（默认）：卡片式章节，彩色章编号，公式卡片，定理框 | `references/analysis-layout.md` |
| 习题集/模拟题/试卷 | 模考布局：深色渐变header，白色侧边栏，试题卡片，可展开答案 | `references/mock-exam-layout.md` |
| 打印索引/自查表（填页码） | 索引布局：CSS多列，可编辑下划线，A4分页预览 | `references/printable-index-layout.md` |

Key signal: "模拟题"/"习题集"/"试卷" → 模考布局；"索引"/"自查"/"填页码" → 索引布局。

> ⚠️ **不混用布局。** 选定了就严格按对应 reference 的 CSS token 值执行（font-size、line-height、max-width、padding、header 样式）。两个布局不可互换。

### Step 1-2: 确定视觉风格

**复习资料文档**：风格选择已在 Step 0 完成（选 分析布局/模考布局/索引布局 即选定了配色、字体、间距），直接执行 Step 3。

**落地页/组件/品牌页面等**：
1. 选一个明确的视觉方向（brutal minimalism / editorial / industrial 等），不要 generic
2. 选字体：优先 Outfit、Plus Jakarta Sans、DM Sans，避免 Inter、Roboto、Arial
3. 中文用 Noto Sans SC 或系统黑体

### Step 3: Build layout

根据 Step 0 的选择引用对应 reference 构建布局：

- **分析布局**: 用 `references/analysis-layout.md` + `references/sidebar-toc.md`。content max-width 880px, padding 28px 24px
- **模考布局**: 用 `references/mock-exam-layout.md`。white sidebar 250px, content max-width 800px
- **索引布局**: 用 `references/printable-index-layout.md`。CSS multi-column, contenteditable 用 JS DOM 实现
- **其他**: sidebar 260-290px + content 780-920px, padding 40-56px

**CSS 规范：**
- 用 CSS variables 管理所有设计 token（颜色、间距、字体）
- Sidebar TOC 必须按 chapter 分组（用 `.chapter[id]` 迭代），不能用 flat heading list
- 移动端响应式：768px breakpoint, hamburger menu, sidebar hidden
- 内容要居中：`margin: 0 auto` + 正确 max-width

> **为什么 sidebar TOC 不能用 flat list：** `querySelectorAll('.sn, h2, h3, h4')` 产生的是没有分章结构的扁平列表，多章文档里根本没法导航。

**✅ 此时检查：**
- `<div>` 和 `</div>` 数量是否平衡（`python3 -c "import re; c=open('file.html').read(); print(len(re.findall(r'<div[\s>]',c)) - len(re.findall(r'</div>',c)))"` → 必须为 0）
- 每个 chapter 边界是否正确闭合
- Sidebar TOC 是否按 chapter 分组
- 移动端 768px 断点是否 ok

### KaTeX: Formula rendering

**唯一正确方式：`katex.renderToString()`**，不要用 `auto-render.min.js` 或 `renderMathInElement()`。（踩坑历史：auto-render 多了额外脚本依赖、无错误隔离、加载顺序容易出问题。）

**步骤：**

1. 注册所有含公式的元素类型到 `querySelectorAll` 里。**在做 JS 之前先列出来**：`.formula-card`、`.theorem`、`.tip-box`、`.err-box`、`.content p`、`.content li`、`td, th`、`.section-name`、`.cb-item`、`.chapter-title`、`h2, h3, h4` + 你新增的任何自定义组件类

2. 在 `</body>` 前加载：
```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.16.9/katex.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.16.9/katex.min.js"></script>
<script>
document.querySelectorAll('.formula-card, .theorem, .tip-box, .err-box, .content p, .content li, td, th, .section-name, .cb-item, .chapter-title, h2, h3, h4').forEach(function(el) {
  var html = el.innerHTML;
  if (html.indexOf('$') !== -1) {
    try {
      html = html.replace(/\$\$([\s\S]*?)\$\$/g, function(_, code) {
        return katex.renderToString(code.replace(/&amp;/g,'&').replace(/&lt;/g,'<').replace(/&gt;/g,'>').trim(), { displayMode: true, throwOnError: false });
      });
      html = html.replace(/\$(.*?)\$/g, function(_, code) {
        return katex.renderToString(code.replace(/&amp;/g,'&').replace(/&lt;/g,'<').replace(/&gt;/g,'>').trim(), { displayMode: false, throwOnError: false });
      });
      el.innerHTML = html;
    } catch(e) {}
  }
});
</script>
```

**关键细节：**
- **`throwOnError: false`** — 一条坏公式不会崩掉整个页面
- **`&amp;` → `&` 解码** — `\begin{bmatrix}` 里的列分隔符 `&` 会被 innerHTML 编码为 `&amp;`，必须恢复
- **`$$` 正则用 `[\s\S]*?` 不是 `.*?`** — `.` 不匹配换行，矩阵等跨行公式会不渲染
- **`<` 后跟字母时用 `&lt;`** — 如 `$a_n<a_{n+1}$` 写成 `$a_n&lt;a_{n+1}$`，否则浏览器会把 `<a` 当 HTML 标签
- **中文不要放到 `$...$` 里面** — KaTeX 不识别 CJK，要么移出，要么包 `\text{}`
- **Python 字符串转义陷阱** — 用 `.py` 脚本生成含 KaTeX 的 HTML 时，Python 会把 `\frac` 的 `\f` 解释为换页符、`\alpha` 的 `\a` 解释为响铃符。**必须用 raw string（`r'''...'''`）或双写反斜杠（`\\frac`）**，详见 Step 0

> **SVG 标签不渲染 KaTeX：** SVG `<text>` 元素不在 HTML DOM 树里，KaTeX 不处理。SVG 里的数学标签用纯文本/Unicode（如 `f(t)` 用斜体 + 括号）。

**✅ 此时检查：**
- 用 `katex.renderToString()`，没有 `auto-render.min.js`
- `katex.min.js` 加载在自己的 `<script src>` 里，早于调用代码
- `querySelectorAll` 包含了所有含 `$...$` 的元素类和标签
- CDN 用 `cdnjs.cloudflare.com`，不用 `cdn.jsdelivr.net`
- 无中文在 `$...$` 内
- 控制台无 KaTeX 错误

### Step 4: Diagrams — use HTML/CSS/SVG only

**零 ASCII art。** 不要出现 `┌┐└┘├┤─│╲╱═║`。

| 需要 | 方案 |
|------|------|
| 流程图 | `.flow-diagram` (flex + arrow spans) |
| 柱状图 | `.barchart` (div + width%) |
| 架构图 | Inline SVG (rect + line + text) |
| 时序图 | Inline SVG (circle + path) |
| 对比图 | `.compare-split` (flex side-by-side) |

详细组件规格：`references/diagram-components.md`。

### Step 5: Educational extras (复习资料专用)

**分析布局**用 `references/analysis-layout.md` 里的样式组件（formula card、theorem box、tip box、error box、badge、table）。

思维框架：三明治结构 — 直觉理解 → 技术细节 → 可视化。

**模考布局**所有样式在 `references/mock-exam-layout.md` 里（example card、priority tag、topic badge）。没有 formula card，知识嵌入 section card 中。

### Step 6: Click-to-reveal (非开卷考时使用)

开卷考就全部直接显示，不加 toggle。

**分析布局**用 `references/click-to-reveal.md`。**模考布局**有内置 toggle 机制（`.toggle-btn` + `.answer-box` 在 `.example-card` 内）。

Toggle 需要三要素同时存在：
1. CSS：`.ans-wrap .ans-content { display: none; }` 和 `.ans-wrap.open .ans-content { display: block; }`
2. CSS：按钮在展开时仍可见 `.ans-wrap.open .ans-toggle { display: inline-block; }`
3. JS：`toggle()` 函数（切换 `.open` class + 更新按钮文字）
4. 间距：`.ans-wrap` 加 `margin-bottom`，`.ans-content` 加 `padding`

### Step 7: Verify before deliver

每一步的检查点已在对应步骤列出了。交付前做最终验证：

**布局验证：**
- [ ] 内容在浏览器中居中，不是左偏
- [ ] 移动端 768px 正常（sidebar 隐藏、内容全宽）
- [ ] `<div>` / `</div>` 平衡（`python3 -c "..."` 为 0）
- [ ] 无浮动的旧版/重复章节内容

**KaTeX 验证：**
- [ ] 所有公式渲染，没有裸 `$...$` 文本
- [ ] SVG 里没有残留的 `$` 分隔符
- [ ] `&amp;` 解码正常
- [ ] 无中文在 `$...$` 内

**文件完整性验证：**
- [ ] 备份已存在
- [ ] 没有用 Edit tool 改中文、没有用 heredoc 传 Python
- [ ] 无控制字符（`grep` for bytes 0x01-0x08 和 0x0c，确认 `\frac`、`\alpha`、`\beta` 等 LaTeX 命令未被 Python 转义吃掉）

**内容验证（复习资料专用）：**
- [ ] 老师提到的所有考点都在文档里
- [ ] 芯片/引脚图已验证 datasheet（不凭记忆画）
- [ ] 复习资料无 ASCII art

---

## Tables Appendix

### KaTeX Anti-Patterns & Common Mistakes

| 问题 | 原因 | 正确做法 |
|------|------|----------|
| 用 `auto-render.min.js` | 多一个脚本依赖，无错误隔离 | `katex.renderToString()` |
| `renderMathInElement(document.body)` | 扫描全部 text node，无 throwOnError | 精确 selector 定位 |
| `throwOnError: true`（默认） | 一条坏公式崩掉全部渲染 | 永远设 `throwOnError: false` |
| `$$` 正则用 `.*?` | `.` 不匹配换行 → 矩阵不渲染 | `[\s\S]*?` |
| 忘了 `&amp;` → `&` 解码 | `\begin{bmatrix}` 的 `&` 变成 `&amp;` | `code.replace(/&amp;/g,'&')` |
| 遗漏自定义组件类在 KaTeX selector 里 | 公式显示为裸 `$...$` | 把所有含公式的类加入 selector |
| SVG `<text>` 里写 `$...$` | KaTeX 不处理 SVG 标签 | SVG 里用纯文本/Unicode |
| 用 Edit tool 改中文 | Encoding 不匹配 → "String to replace not found" | 写 `.py` 文件来改 |
| Bash heredoc 传 Python | 转义序列损坏 | 先 Write `.py`，再 `python3 file.py` |
| 用 Python regex 加 contenteditable | 中文+嵌套 HTML 导致 regex 边界错乱 | JS DOM TreeWalker 运行时处理 |
| Python 普通字符串写 LaTeX | `\f`=换页、`\a`=响铃、`\b`=退格 | 用 raw string；JSON 层需加倍，详见 `references/python-latex-escape.md` |
| `re.sub` replacement 再次吃掉转义 | `\a` 再被转为响铃 | replacement 也用 raw string `r'\\alpha'` |
| 误判 `\frac` 完整性 | `\frac` = `\f`+`rac{`，`rac{` 前是 `f` | grep 控制字符或 `[^\\]rac{` |
| Python 脚本读写同文件 | 二次运行时章节标记重复 | 源文件和输出文件用不同路径 |
| 凭记忆画芯片引脚 | 引脚名/编号全是错的 | 引用 datasheet 或已验证文件 |
| 多章文档 sidebar 用 flat heading list | 各章标题混在一起无法导航 | 按 `.chapter[id]` 分组 |
| toggle 按钮点了没反应 | 忘了定义 `toggle()` 函数 | 见 Step 6 |
| 展开答案贴住下一节 | 忘了 `margin-bottom` 和 `padding` | `.ans-wrap` 加 margin |
| div 不平衡 | 多了一个或少了一个 `</div>` → 卡片横着排 | 用 python 脚本验证平衡 |

---

## Reference Files

| 文件 | 用途 |
|------|------|
| `references/analysis-layout.md` | 分析布局的完整 CSS 和结构 |
| `references/mock-exam-layout.md` | 模考布局的完整 CSS 和结构 |
| `references/printable-index-layout.md` | 索引布局的完整 CSS 和结构 |
| `references/sidebar-toc.md` | Sidebar TOC 的自动生成方法 |
| `references/diagram-components.md` | 流程图/柱状图/SVG 组件规格 |
| `references/click-to-reveal.md` | 点击展开答案的通用方案 |
| `references/python-latex-escape.md` | **写 .py 脚本前必读**：三层转义详解（JSON → Python → re.sub） |
| `references/incidents.md` | 历史踩坑记录（contenteditable 损坏、heredoc 转义等） |
