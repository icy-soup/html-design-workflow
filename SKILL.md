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

## Overview

Route the user's HTML request to the right approach — document, slides, or
landing page — then execute with intentional design choices. Delegate to
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
| 复习资料/章节总结/知识整理 | **分析布局（默认）**：卡片式章节 + 侧边栏TOC + KaTeX (`references/analysis-layout.md`) | — |
| 复习资料/习题集/模拟考 | **模考布局**：试题卡片 + 可展开答案 + 深色渐变header + 白色侧边栏 (`references/mock-exam-layout.md`) | — |
| 打印索引/知识点清单 | **索引布局**：CSS多列自动左右流 + 可编辑页码下划线 + A4分页预览，不留硬编码页码 (`references/printable-index-layout.md`) | — |
| 技术文档/手册 | Sidebar + content layout, deep code blocks | — |
| 演示文稿/幻灯片 | Full-screen pages, arrow key navigation | html-presentation |
| 落地页/组件 | Single page, brand-forward design | frontend-design |
| 不确定 | Ask user, then classify | — |

## Style Learning (styleref)

When the user says 学习这个风格 / 参考这个页面 / 提取风格:

1. Read the file, extract: tone, color palette (hex), fonts, layout patterns, interactions
2. Save analysis to `styleref/<name>.md`
3. Apply the recorded style to the user's next HTML request

Check `styleref/` before starting — reuse a stored style if it matches.

## Workflow

### Step 0: Choose layout

For 复习资料/考试备考/知识整理/期末复习 HTML tasks, skip Steps 1-2 and use one of the three predefined layouts:

| If user wants... | Use layout | Reference |
|-----------------|------------|-----------|
| 章节总结/知识整理/概念梳理 | **分析布局（默认）**：卡片式章节，彩色章编号，公式卡片，定理框 | `references/analysis-layout.md` |
| 习题集/模拟题/试卷风格 | **模考布局**：深色渐变header，白色侧边栏，试题卡片，可展开答案 | `references/mock-exam-layout.md` |
| 打印索引/知识点清单/自查表 | **索引布局**：CSS多列自动左右流，contenteditable空白下划线（不留硬编码P编号），A4分页预览，适合打印填页码 | `references/printable-index-layout.md` |

**How to decide:** Ask yourself whether the content is structured as "knowledge sections with examples" (模考布局), "chapter-by-chapter reference cards" (分析布局), or "compact check-list with page references" (索引布局). Key signal: if the user says "模拟题", "习题集", "试卷", "mock exam", or wants click-to-reveal answers, use the mock-exam layout. If they want a printable index with editable page numbers to fill in while studying from PPT, use the 索引布局.

If the user explicitly asks for a different style, follow Steps 1-2 normally.

### Step 1: Choose aesthetic direction
Pick ONE clear tone before writing code: brutal minimalism, maximalist chaos,
retro-futuristic, organic, luxury, editorial, soft pastel, industrial, etc.
The choice must feel intentional, not generic.

### Step 2: Pick theme and fonts
Borrow from theme-factory's 8 preset themes if no preference. Avoid Inter,
Roboto, Arial, Space Grotesk. Prefer Outfit, Plus Jakarta Sans, DM Sans,
JetBrains Mono (code). For Chinese: Noto Sans SC or system hei.

### Step 3: Build layout
- **复习资料/考试备考/知识整理 documents (分析布局)**: use `references/analysis-layout.md` with sidebar TOC (`references/sidebar-toc.md`). Content max-width 880px, padding 28px 24px.
- **复习资料/习题集/模拟考 documents (模考布局)**: use `references/mock-exam-layout.md`. White sidebar (fixed 250px), centered content max-width 800px, dark gradient header, example cards with toggle answers. See the reference for full CSS.
- **打印索引/知识点清单 documents (索引布局)**: use `references/printable-index-layout.md`. Fixed-height A4 pages, CSS multi-column (`column-count: 2`), contenteditable blank underlines (no hardcoded P numbers!), localStorage persistence. Header: white with bottom border.
- **Other documents**: sidebar 260-290px + content max 780-920px, padding 40-56px
- Use CSS variables for design tokens
- Staggered fadeInUp on load, respect `prefers-reduced-motion`
- Sidebar TOC: **For multi-chapter docs, ALWAYS use chapter-based TOC** (iterate `.chapter[id]`, group `.chapter-title` + subsections). See `references/sidebar-toc.md`. Flat heading lists are NOT acceptable for multi-chapter documents — they produce unusable TOCs with no chapter grouping. For single-page docs or mock-exam layout, manual links in sidebar are fine.

### Step 4: Diagrams — use HTML/CSS/SVG only
**Iron rule: zero ASCII art.** No `┌┐└┘├┤─│╲╱═║` in final HTML.

| Need | Use |
|------|-----|
| Flow/process | `.flow-diagram` (flex + arrow spans) |
| Bar chart | `.barchart` (div + width%) |
| Architecture/multi-branch | Inline SVG (rect + line + text) |
| Scatter/timeline | Inline SVG (circle + path) |
| Before/after | `.compare-split` (flex side-by-side) |

Detailed diagram component specs: see `references/diagram-components.md`.

### Step 5: Educational doc extras (if applicable)
For 复习资料/考试备考/知识整理 documents using the **分析布局**:
- Use `references/analysis-layout.md` for all styled components (formula cards, theorem boxes, tip boxes, error boxes, mnemonic badges, frequency tags, styled tables).
- 三明治 structure: plain-language metaphor → technical detail → visualization
- Key concepts get `.info-card` (colored left border, light background)
- Comparisons get `.compare-split` (red left / green right)

For **模考布局** documents: all styled components are defined in `references/mock-exam-layout.md` (example cards, priority tags, topic badges, tip boxes, tables). No formula cards or theorem boxes — knowledge is embedded in section cards directly.

### Step 6: Click-to-reveal / answer toggles (optional)
For 复习/备考 documents that are NOT open-book (开卷考), add toggles for answers, proofs, and memory items.

- **分析布局 (analysis-layout)**: use `references/click-to-reveal.md` for the generic click-to-reveal interaction.
- **模考布局 (mock-exam-layout)**: use the built-in toggle mechanism documented in `references/mock-exam-layout.md` — the `.toggle-btn` + `.answer-box` pattern inside `.example-card` cards. This is purpose-built for exam-style question/answer pairs.

If the user says the exam is open-book (开卷考), skip all toggles — show all content immediately.

### Step 7: Deliver
See the delivery checklist below. **Check before claiming done.**

### Step -1: Backup before modifying ANY existing HTML
Before editing any existing HTML file, create a timestamped backup:
```bash
mkdir -p backup/$(date +%Y-%m-%d) && cp file.html backup/$(date +%Y-%m-%d)/
```
This is not optional — file corruption during HTML editing has happened repeatedly and backups are the only recovery path.

## Core Principles Behind This Skill

This skill exists because past HTML work repeatedly had the same failures:
formulas not rendering, user frustration, wasted time. Understanding *why*
each rule exists helps you apply them correctly instead of robotically.

### Why you must invoke this skill before writing any HTML

Every time this skill was skipped, the output had problems: wrong KaTeX
approach, broken script loading, missing sidebar/layout. The skill bakes in
solutions to problems you won't see until the user opens the file. Invoking
it first is not overhead — it's what prevents the need for rewrites.

### Why KaTeX needs `renderToString()` not `renderMathInElement()`

The `renderToString()` approach (documented below) targets only elements
containing `$...$` and uses `throwOnError: false`. This means: one bad formula
can't crash the entire page, HTML entities inside formulas are handled
correctly, and there's no script-load-order dependency beyond loading
`katex.min.js`. The `auto-render.min.js` approach failed repeatedly because
it introduces an extra script dependency, has no error isolation, and breaks
when scripts load out of order.

### Why you should never question the user's environment

When a user says "it doesn't work" or formulas don't render, the root cause
has always been code error — script order, wrong KaTeX approach, missing
elements. Blaming CDN availability or the user's network is not only wrong
(working reference files prove CDN works fine), it wastes time on wild goose
chases. Debug your code first, always.

### Why every chapter needs correct opening and closing divs

In multi-chapter HTML with `display: flex` layout, every `.chapter` div must be
properly closed. An extra `</div>` after a chapter close will close the parent
`.container` prematurely, pushing ALL subsequent chapters outside the container.
Without the sidebar offset (`margin-left` on `.cw`), those chapters appear
**horizontally "横着排"** instead of stacked vertically as cards.

Every chapter boundary MUST be verified:
- Ch1 ends: `</div></div>\n</div>` (ct+content close, chapter close) — NO extra
- After removing/inserting chapters, grep to confirm `<div` count matches `</div>` count

**Check with:** `python3 -c "import re; c=open('file.html').read(); print(len(re.findall(r'<div[\s>]',c)) - len(re.findall(r'</div>',c)))"`
Expect `0`. Any non-zero means unbalanced divs = broken layout.

### Why click-to-reveal needs both CSS and a toggle function

Answer toggle buttons with `onclick="toggle(this)"` require the `toggle()` function
to exist in the JavaScript. Forgetting to define it means clicking does nothing.

The pattern requires THREE things, all must be present:
1. **CSS**: `.ans-wrap .ans-content { display: none; }` and `.ans-wrap.open .ans-content { display: block; }`
2. **CSS**: Keep button visible when open: `.ans-wrap.open .ans-toggle { display: inline-block; }` (not `display: none` — user needs to collapse again)
3. **JS**: A `toggle()` function that toggles `.open` class and updates button text:
```javascript
function toggle(btn) {
  btn.closest('.ans-wrap').classList.toggle('open');
  btn.textContent = btn.closest('.ans-wrap').classList.contains('open') ? '收起答案' : '显示答案';
}
```

Also add bottom margin to `.ans-wrap` and padding to `.ans-content` so expanded
answers don't stick to the next section:
```css
.ans-wrap { margin-bottom: 14px; }
.ans-wrap .ans-content { padding: 10px 14px 14px; }
```

### Why KaTeX selectors must cover ALL formula-containing elements

Formulas can appear in many element types: headings, chapter titles, tip boxes,
error boxes, concept boxes, table cells, etc. If the KaTeX `querySelectorAll`
list misses any of these, those formulas stay as raw `$...$` text.

**Golden rule:** every custom box/component class you add to the HTML must also
be added to the KaTeX selector. Common ones that get forgotten:

- `.err-box` — error boxes with `$...$` in examples
- `.cb-item`, `.chapter-concept` — concept lists with inline math
- `.tip-box` — tip boxes can contain formula references
- `.section-name` — section headings may have math

Also include heading tags for formulas in titles:

```javascript
document.querySelectorAll('.formula-card, .theorem, .tip-box, .err-box, .content p, .content li, .tbl td, .tbl th, .section-name, .cb-item, .chapter-concept, .chapter-title, h2, h3, h4')
```

### Why sidebar TOC must be organized by chapter (not flat)

Using `querySelectorAll('.sn, h2, h3, h4')` produces a flat list — all section
names mixed together with no chapter grouping. This makes the sidebar useless
for navigation in multi-chapter documents.

**Correct approach:** iterate over `.chapter[id]` elements, add `.chapter-title`
as a top-level `.toc-h2` entry, then within each chapter add `.sn, h2, h3, h4`
elements as `.toc-h3` sub-entries. Skip elements inside `.ans-wrap` (hidden answers).

### Why chip pin diagrams must be verified from datasheets

Chip pin configurations (pin names, numbers, functions) are fixed physical
standards. Drawing them from memory WILL produce wrong results — address pins
get wrong names (A0/A1/A2 vs A/B/C), enable pins get wrong labels (S1 vs G1),
and pin numbers are completely invented. This wastes user time and destroys
trust.

**Always reference an actual datasheet or a verified source** (e.g., a known
working HTML file, a textbook diagram, a manufacturer PDF) for every chip's
pin configuration. Do not guess. Do not draw from memory.

For 复习资料 HTML, check the working reference HTML files in the user's project
directory for verified pin diagrams. Use those as ground truth.

### Why layout must strictly follow its chosen reference

The two layout references (analysis-layout and mock-exam-layout) exist because past HTML documents had inconsistent spacing, wrong font sizes, and poor centering. Each reference specifies exact values for its style:

**分析布局 (`references/analysis-layout.md`):**
- Body font: 14px, line-height 1.9
- Container max-width: 880px, padding 28px 24px
- Header: white/cream card background with bottom border (not dark gradient)
- Chapter cards: proper padding and color-coded numbering

**模考布局 (`references/mock-exam-layout.md`):**
- Body: Noto Serif SC 16px, line-height 1.8
- Container max-width: 800px (sidebar offset 250px)
- Header: dark gradient (3-stop), decorative gold bottom bar
- Example cards: left accent border with toggle answers

Deviating from the chosen reference's values produces "ugly" results that users immediately notice. Apply the reference values exactly, then customize only if the user explicitly asks. **The two layouts are not interchangeable** — pick the right one in Step 0, then follow it precisely.

### Why file editing must use .py files (not Edit tool / heredoc)

File editing mistakes have cost hours of wasted time. The root cause is always the same: trying to edit files with Chinese text using tools that don't handle encoding correctly.

**Problem 1: Edit tool + Chinese text.** The `Edit` tool's `old_string` parameter cannot reliably match Chinese characters. Even when the text looks identical, encoding mismatches cause `String to replace not found` errors. Every time.

**Problem 2: Bash heredoc + Python + backslashes.** Passing Python code via bash heredoc (`<< 'PYEOF'`) causes escape sequence corruption. `\\begin` becomes a backspace character (`\\b` → `\\x08`), `\\quad` gets mangled, and the HTML file ends up with control characters that break KaTeX rendering. These bugs are invisible until the user opens the file and sees formula errors.

**The fix:** Never use `Edit` for Chinese text replacement. Never use heredoc to pass Python code. Instead:
1. Write the Python script to a `.py` file using the `Write` tool
2. Run it with `python3 file.py`
3. Verify with `Read` or grep afterward

See delivery checklist items below for specific verification steps.

### Why all exam points must be included

When creating review HTML, the user expects EVERY topic and chip the teacher
mentioned to appear in the document, not just the ones you remember or think
are important. Omitting topics forces the user to point out missing content,
wasting time and eroding trust. The teacher's review class transcript is the
ground truth for what to cover. If a teacher says "these 5 chips might be
tested," all 5 must appear with pin diagrams and truth tables.

### Why contenteditable must use JS DOM, never Python HTML manipulation

Adding contenteditable to text content by modifying HTML source files with Python
scripts/regex is DANGEROUS and has caused severe corruption:

**Problem:** Python regex cannot reliably handle Chinese text + nested HTML tags.
Matching `class="(?:ul|tag)"` inside HTML with Chinese content produces wrong
boundaries. A single wrong regex (e.g., `class=""` instead of `class="`) wraps
child elements inside the wrong parent, creating cross-element corruption.
Once the HTML is damaged, recovery is extremely difficult without a backup.

**The correct approach — JS DOM at runtime:** Write a script in the HTML's
`<script>` tag that runs on page load:

```javascript
document.querySelectorAll('.tl, .ss').forEach(function(el) {
  var walk = document.createTreeWalker(el, NodeFilter.SHOW_TEXT, null, false);
  var nodes = [];
  while (walk.nextNode()) nodes.push(walk.currentNode);
  nodes.forEach(function(node) {
    if (node.textContent.trim()) {
      var span = document.createElement('span');
      span.contentEditable = 'true';
      span.className = 'tx';
      span.textContent = node.textContent;
      node.parentNode.replaceChild(span, node);
    }
  });
});
```

This approach:
- Never touches the HTML source file — zero risk of corruption
- Uses `TreeWalker` to safely extract only text nodes (ignores `.tag`, `.ul` children)
- If something goes wrong, refresh the page to restore original state
- localStorage persistence uses separate key prefixes per class: `gpio_tx_` for text, `gpio_ul_` for page numbers

**Golden rule:** NEVER use Python/regex to add contenteditable to existing HTML.
Always use JS DOM in the browser at runtime.

**If you ALREADY corrupted the file (recovery procedure):**

The typical failure modes and fixes:

1. **Regex `class=""` vs `class="` typo** — double quotes in regex pattern `class=""(?:ul|tag)` won't match any span, causing the regex to consume content across element boundaries. Fix: carefully check regex patterns for quote errors before running.

2. **`</span>` truncated to `<`** — character-by-character HTML processing that does `result.append(html[i])` then `i += 7` for `</span>` only saves the `<` and skips `/span>`. Fix: always append the full 7-char string `result.append(html[i:i+7])`, not just `html[i]`.

3. **Recovery from bare `<` corruption** — if `</span>` tags were truncated to bare `<`:
   - Use a tag-whitelist approach: scan for `<` followed by known tag names (span, div, b, /span, etc.). Any `<` NOT followed by a known tag name is a corrupted `</span>` → restore it.
   - Then rebalance span depth: remove extra `</span>` that don't match any open `<span>`.
   - Finally fix nesting: move orphaned `.ul` back inside `.tl` if they became siblings.

   See `C:/Users/Karen Lee/.claude/projects/F--In-class-Learning-Materials-Aai--------/memory/feedback_html_edit.md` for the full incident details.

## KaTeX Math Rendering (数学公式)

### Backup First
Before modifying ANY HTML file, create a backup:
```bash
mkdir -p backup/$(date +%Y-%m-%d) && cp file.html backup/$(date +%Y-%m-%d)/
```

### CORRECT APPROACH: `katex.renderToString()` (proven working)

**DO NOT use `auto-render.min.js` or `renderMathInElement()`.** These have caused:
- Script loading order bugs (ReferenceError on all formulas)
- CDN loading failures (auto-render.js is a separate file that may not load)
- No error handling (formulas fail silently)

Use the proven approach from a working reference HTML file in the project directory:

```html
<!-- CSS in <head> -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.16.9/katex.min.css">

<!-- At end of <body>, load katex.min.js THEN run render -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.16.9/katex.min.js"></script>
<script>
document.querySelectorAll('.formula-card, .theorem, .tip-box, .err-box, .content p, .content li, .tbl td, .tbl th, .section-name, .cb-item, .chapter-concept, .chapter-title, h2, h3, h4').forEach(function(el) {
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

Key details:
- **`throwOnError: false`** — CRITICAL. Without this, a single bad formula crashes everything
- **`&lt;`/`&gt;`/`&amp;` replacement** — HTML entities inside `$...$` must be decoded before KaTeX processes them. `innerHTML` serialization re-encodes `&` as `&amp;` and `<` as `&lt;`, so KaTeX would see `&amp;` instead of `&` (breaking `\begin{bmatrix}` column separators) and `&lt;` instead of `<` (breaking comparison operators).
- **`[\s\S]` in `$$` regex** — display math (`$$...$$`) often spans multiple lines (e.g., matrices). Use `[\s\S]*?` instead of `.*?` because `.` does NOT match newlines in JavaScript regex.
- **Selector-based** — target specific elements (`.fc`, `.tbl td`, etc.), NOT `document.body`
- **`.innerHTML` approach** — replaces `$...$` patterns in existing HTML rather than scanning DOM text nodes
- **Only `katex.min.js` needed** — no auto-render, no extra script dependencies

### `<` in Inline Math (`$...$`)
In HTML, `<` is always parsed as a tag start by the browser **before** KaTeX runs.
So `$b<a<c$` → browser sees `<a` as an HTML tag → DOM corruption.
**Fix:** Always use `&lt;` when `<` is followed by a letter inside `$...$`:
- `$b<a<c$` → `$b&lt;a&lt;c$`
- `$a_n<a_{n+1}$` → `$a_n&lt;a_{n+1}$`
Numbers after `<` (like `$x<0$`) are safe — they don't form HTML tags.

### Chinese Characters Inside `$...$` (中文在公式内)
Chinese characters MUST NOT appear inside `$...$` delimiters.
KaTeX cannot render CJK characters as-is. Move them outside the math
delimiters or wrap in `\text{}`:

**Wrong:** `$A - B = A + (-B)_补$`
**Right:** `$A - B = A + (-B)_\text{补}$`

**Check:** grep for `\$[^$]*[一-鿿][^$]*\$` — if found, fix immediately.

### `$$` Display Math Pairing
Each `$$` must open AND close, and content between must be PURE LaTeX:
```
$$                    ← opening
f(x) = x^2           ← only LaTeX here, NO Chinese text, NO HTML
$$                    ← closing
```

### CDN for Chinese Users
`cdn.jsdelivr.net` is often slow/blocked in China. Use `cdnjs.cloudflare.com` instead:
```
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.16.9/katex.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.16.9/katex.min.js"></script>
```

### KaTeX Anti-Patterns (NEVER do these)

| Anti-pattern | Why it's wrong | Correct way |
|-------------|----------------|-------------|
| Using `auto-render.min.js` | Extra dependency, script order bugs, no error control | `katex.renderToString()` directly |
| `renderMathInElement(document.body, ...)` | Scans ALL text nodes, can pick up HTML tags, no throwOnError | Target specific elements with selectors |
| Script loading `renderMathInElement()` before katex.min.js | ReferenceError → all formulas fail | Load katex.min.js first, THEN run inline script |
| `throwOnError: true` (default) | One bad formula crashes all rendering | Always set `throwOnError: false` |
| `.*?` in `$$` regex (instead of `[\s\S]*?`) | `.` doesn't match newlines → multi-line matrices don't render | `/\$\$([\s\S]*?)\$\$/g` |
| Forgetting `&amp;` → `&` decoding | `\begin{bmatrix}` column separators become `&amp;` → matrix breaks | `code.replace(/&amp;/g,'&')` before `&lt;`/`&gt;` replacement |
| Missing custom element classes in KaTeX selector | Formulas in `.err-box`, `.cb-item`, `.chapter-concept` stay raw | Add all formula-containing element classes to `querySelectorAll` |
| Expecting CDN to work without verification | User's network may not reach CDN | Test locally or use proven CDN (cloudflare) |
| Questioning the user's environment | Your code is wrong, not the user's | Debug your own code first |

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| ASCII art for diagrams | Replace with HTML/CSS/SVG components |
| Generic fonts (Inter, Arial) | Pick a distinctive pair |
| No mobile layout | Hamburger menu + responsive sidebar |
| Code blocks without syntax highlight | Dark bg (`#1a1b2e`) + keyword spans |
| Deep code blocks in light-themed text | Use `<span style="color:#cbd5e1;">` |
| Empty `<div class="codehilite">` | Remove or add content |
| Chip pins drawn from memory (wrong names/numbers) | Reference datasheet or verified source file — never guess |
| Layout doesn't match the chosen reference (wrong font/line-height/padding/header style) | Apply exact values from the selected reference (`analysis-layout.md` or `mock-exam-layout.md`) |
| Missing exam topics the teacher mentioned | Cross-check against teacher's review text; include all mentioned chips and concepts |
| Content not visually centered | Use `margin: 0 auto` on container with proper max-width; verify in browser |
| **Edit tool with Chinese text** (`old_string` matching fails) | **Write a `.py` file with `Write` tool, then run it** — never use Edit for Chinese |
| **Bash heredoc for Python code** (`<< 'PYEOF'` corrupts `\\b`, `\\t`, `\\begin`) | **Write Python script to `.py` file first, then execute** — no heredoc |
| **Extra or missing `</div>` at chapter boundaries** (cards go horizontal "横着排") | Every chapter needs exactly one close per open. Grep to verify `<div` count = `</div>` count. Check: Ch1/Ch2 commonly have extra `</div>`; Ch3 often misses its chapter close |
| **Python regex corrupts HTML when adding contenteditable** (`class=""` typo mismatches nested spans) | Never use Python/regex to modify HTML structure for contenteditable — use JS DOM TreeWalker at runtime instead |
| **`</span>` truncated to `<` in character-by-character processing** (`result.append(html[i])` + `i+=7` only saves `<`) | Use `result.append(html[i:i+7])` to save the full `</span>`, not just `html[i]` |
| **Bare `<` from corrupted `</span>` breaks all HTML tags in the file** | Run tag-whitelist recovery: known tag names stay, bare `<` → restore to `</span>`, then rebalance span depth |
| **KaTeX formulas in headings not rendering** (`$V_I=$` in `<h4>` shows raw text) | Add `h2, h3, h4, .chapter-title` to the KaTeX `querySelectorAll` list |
| **KaTeX `$$` display math multi-line not rendering** (matrix spans 2 lines) | Use `[\s\S]*?` instead of `.*?` in the `$$` regex — `.` doesn't match newlines |
| **`&amp;` not decoded in KaTeX** (`\begin{bmatrix}` column separator `&` shows as `&amp;`) | Add `.replace(/&amp;/g,'&')` to the KaTeX `renderToString` call chain, BEFORE `&lt;`/`&gt;` replacement |
| **KaTeX selector misses custom component classes** (`.err-box`, `.cb-item`, `.chapter-concept` formulas stay raw) | Add every custom element class that contains `$...$` to the KaTeX `querySelectorAll` list |
| **Answer toggle button does nothing** (`onclick="toggle(this)"` no-op) | Define `toggle()` function in JS AND add CSS for `.ans-wrap .ans-content` |
| **Expanded answer sticks to next section** | Add `margin-bottom` to `.ans-wrap` and `padding` to `.ans-content` |
| **Sidebar TOC shows flat list, not organized by chapter** | Iterate over `.chapter[id]`, group subsections under chapter title entries |
| **Section names are unstyled** (using `<div class="sn">` without CSS) | Either use `<h2>/<h3>` tags, or define `.sn { font-size: 15px; font-weight: 700; }` in CSS |
| **Floating/duplicate chapter content** (old + new versions of same chapter) | When adding new chapter sections, remove any old/unstyled versions of that content |

## Delivery Checklist

- [ ] **Zero ASCII art**: grep for `┌┐└┘├┤─│╲╱═║` — replace all
- [ ] **No `diagram-line` divs** in body (only in CSS definitions)
- [ ] **No frame-drawing chars** in code blocks — use `-` `*` instead
- [ ] **Sidebar TOC**: auto-generated from headings, mobile hamburger works (for docs with 3+ sections)
- [ ] **Mobile**: hamburger menu + responsive
- [ ] **Color consistency**: CSS variables, chapter colors unified
- [ ] **Readability**: line-height ≥ 1.65, sufficient contrast
- [ ] **Motion**: respect `prefers-reduced-motion`
- [ ] **No empty code blocks**
- [ ] **KaTeX uses renderToString**: confirm NO `auto-render.min.js` or `renderMathInElement()`. Must use `katex.renderToString()` with `throwOnError: false`
- [ ] **KaTeX script order**: confirm `<script src="katex.min.js">` loads BEFORE the inline script that calls `katex.renderToString()`
- [ ] **KaTeX Chinese check**: grep for `\$[^$]*[一-鿿][^$]*\$` — move Chinese outside `$...$` or wrap in `\text{}`
- [ ] **KaTeX < check**: grep for `\$[^$]*<[a-z]` in `$...$` — replace `<` with `&lt;` if found
- [ ] **KaTeX CDN**: use `cdnjs.cloudflare.com/ajax/libs/KaTeX/` not `cdn.jsdelivr.net`
- [ ] **Backup**: verify backup exists in `backup/YYYY-MM-DD/` before modifying
- [ ] **Analysis layout**: card-based chapters, color-coded sections, formula cards, chip SVG diagrams, truth tables (for 复习资料 type)
- [ ] **Mock-exam layout** (if chosen): dark gradient header with gold decorative line, white sidebar, example cards with toggle-btn + answer-box, priority tags (pri-high/mid/skip), topic badges
- [ ] **Printable-index layout** (if chosen): A4 page blocks (`.page`), float two-column, contenteditable underlines with localStorage persistence, dashed page-break separators, print mode hides breaks and resets page height
- [ ] **Click-to-reveal**: present only if NOT open-book exam
- [ ] **Chip diagrams + truth tables**: include SVG chip pin diagrams AND truth tables for all covered chips (for 复习资料 type)
- [ ] **Before claiming done**: open the file in browser and visually verify formulas render, layout is correct, no errors in console
- [ ] **All teacher-mentioned topics included**: cross-check against review class transcript; every chip and concept the teacher said "might be tested" must appear
- [ ] **Chip pins verified from datasheet**: every pin name, number, and function in chip diagrams must match a verified source — never from memory
- [ ] **Layout matches chosen reference**: check the values in either `references/analysis-layout.md` or `references/mock-exam-layout.md` depending on which layout was chosen. Verify font, line-height, container max-width, padding, header style.
- [ ] **Content centered in browser**: verify page layout is not left-heavy; container should have `margin: 0 auto` and correct max-width on wide screens
- [ ] **File editing method**: confirm NO `Edit` tool used with Chinese `old_string` and NO `<< 'PYEOF'` heredoc — use `.py` file instead
- [ ] **No control characters**: grep for bytes `0x01`-`0x08` in the final HTML (check `\b` wasn't corrupted to backspace)
- [ ] **`$$` display math markers verify**: confirm each `$$` has matching open/close, especially around `\begin{aligned}`
- [ ] **Div balance**: run `python3 -c "import re; c=open('file.html').read(); print(len(re.findall(r'<div[\s>]',c)) - len(re.findall(r'</div>',c)))"` — must be `0`
- [ ] **KaTeX heading selector**: confirm `h2, h3, h4, .chapter-title` are in the KaTeX `querySelectorAll` (not just `.content p`)
- [ ] **KaTeX selector covers custom elements**: confirm all custom box classes (`.err-box`, `.cb-item`, `.chapter-concept`, `.tip-box`, etc.) that contain `$...$` are in the KaTeX `querySelectorAll`
- [ ] **KaTeX `$$` regex uses `[\s\S]`**: confirm `/\$\$([\s\S]*?)\$\$/g` not `/\$\$(.*?)\$\$/g` — test by putting a multi-line matrix in `$$...$$`
- [ ] **KaTeX `&amp;` decoding**: confirm `.replace(/&amp;/g,'&')` is in both display and inline `renderToString` calls
- [ ] **Toggle button works**: confirm `toggle()` JS function exists AND CSS for `.ans-wrap .ans-content` is defined; verify button stays visible when answer is open
- [ ] **Answer spacing**: expanded content has `margin-bottom` on `.ans-wrap` and `padding` on `.ans-content` so answer doesn't stick to next section
- [ ] **TOC organized by chapter**: sidebar TOC iterates over `.chapter[id]` elements, not just a flat `querySelectorAll` of headings — verify chapter titles appear as top-level entries with subsections nested under them
- [ ] **No duplicate content**: grep for duplicate chapter sections that might be leftover from previous versions
- [ ] **Section name styling**: if using `.sn` divs instead of `<h2>/<h3>`, confirm `.sn { }` is defined in CSS
