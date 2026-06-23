---
name: html-design-workflow
description: >-
  CRITICAL: You MUST use this skill ANY time the user mentions anything related
  to HTML files or web pages. Trigger on: 做HTML, 做网页, 做页面, 写HTML,
  写网页, 修改html, html坏了, 做个页面, 做个HTML, 帮我写个HTML, 前端设计, 做组件,
  做仪表盘, 做幻灯片, 做PPT, 做演示. ALSO trigger on: 公式不显示, 公式乱码,
  公式渲染, KaTeX, 复习资料HTML, 数学公式, 显示不全, 数学分析, 期末复习, 课件HTML,
  review-ch, "$$"显示问题, 数学符号乱码. ALSO trigger on user showing an existing
  HTML file to emulate 学习这个风格, 参考这个页面, 提取风格. ANY request involving
  HTML output MUST trigger this skill without exception.
  When triggered, ALWAYS invoke this Skill tool BEFORE writing any code or
  responding to the user. Read the full SKILL.md and all references before
  starting. DO NOT skip this step — failing to invoke the skill before work
  has caused severe issues in the past.
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
| 复习资料/考试备考/知识整理 | **分析布局（默认）**：卡片式章节 + 侧边栏TOC + KaTeX | — |
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

### Step 0: Check if analysis layout applies
For 复习资料/考试备考/知识整理/期末复习 HTML tasks, skip Steps 1-2 and use the **default analysis layout** directly (see `references/analysis-layout.md`). This layout provides card-based chapters, color-coded sections, formula cards, theorem boxes, tip boxes, and styled tables.

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
- **复习资料/考试备考/知识整理 documents**: use the default analysis layout (`references/analysis-layout.md`) with sidebar TOC (`references/sidebar-toc.md`). Content max-width 880px, padding 28px 24px.
- **Other documents**: sidebar 260-290px + content max 780-920px, padding 40-56px
- Use CSS variables for design tokens
- Staggered fadeInUp on load, respect `prefers-reduced-motion`
- Sidebar TOC auto-generates from `<h2>`/`<h3>` headings via JS — add heading IDs

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
For 复习资料/考试备考/知识整理 type documents, use `references/analysis-layout.md` for all styled components (formula cards, theorem boxes, tip boxes, error boxes, mnemonic badges, frequency tags, styled tables).

- 三明治 structure: plain-language metaphor → technical detail → visualization
- Key concepts get `.info-card` (colored left border, light background)
- Comparisons get `.compare-split` (red left / green right)

### Step 6: Click-to-reveal (optional)
For 复习/备考 documents that are NOT open-book (开卷考), add click-to-reveal toggles for answers, proofs, and memory items. See `references/click-to-reveal.md` for implementation.

If the user says the exam is open-book (开卷考), skip click-to-reveal entirely — show all content immediately.

### Step 7: Deliver
See the delivery checklist below. **Check before claiming done.**

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

Use the proven approach from `F:\In-class Learning Materials\Aai大二\下\最优化\最优化期末复习参考.html`:

```html
<!-- CSS in <head> -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.16.9/katex.min.css">

<!-- At end of <body>, load katex.min.js THEN run render -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.16.9/katex.min.js"></script>
<script>
document.querySelectorAll('.fc, .tb, .tip, .content p, .content li, .tbl td, .tbl th, .di p').forEach(function(el) {
  var html = el.innerHTML;
  if (html.indexOf('$') !== -1) {
    try {
      html = html.replace(/\$\$(.*?)\$\$/g, function(_, code) {
        return katex.renderToString(code.replace(/&lt;/g,'<').replace(/&gt;/g,'>').trim(), { displayMode: true, throwOnError: false });
      });
      html = html.replace(/\$(.*?)\$/g, function(_, code) {
        return katex.renderToString(code.replace(/&lt;/g,'<').replace(/&gt;/g,'>').trim(), { displayMode: false, throwOnError: false });
      });
      el.innerHTML = html;
    } catch(e) {}
  }
});
</script>
```

Key details:
- **`throwOnError: false`** — CRITICAL. Without this, a single bad formula crashes everything
- **`&lt;`/`&gt;` replacement** — HTML entities inside `$...$` must be decoded before KaTeX processes them
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
- [ ] **Click-to-reveal**: present only if NOT open-book exam
- [ ] **Chip diagrams + truth tables**: include SVG chip pin diagrams AND truth tables for all covered chips (for 复习资料 type)
- [ ] **Before claiming done**: open the file in browser and visually verify formulas render, layout is correct, no errors in console
