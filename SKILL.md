---
name: html-design-workflow
description: >-
  CRITICAL: You MUST use this skill ANY time the user mentions HTML files,
  especially when they involve KaTeX math formulas, review materials, or
  formula display issues. Trigger on: 做HTML, 做网页, 做页面, 修改html, html坏了,
  公式不显示, 公式乱码, 公式渲染, KaTeX, 复习资料HTML, 数学公式, 显示不全,
  数学分析, 期末复习, 课件HTML, review-ch, "$$"显示问题, 数学符号乱码.
  Also triggers on: 前端设计, UI设计, 做组件, 做仪表盘, 做幻灯片, 做PPT, 做演示.
  Also use when user says 学习这个风格, 参考这个页面, 提取风格, or shows an HTML
  file to emulate. When triggered, ALWAYS follow the backup rule and math
  formula checks in this skill.
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

## KaTeX Math Rendering (数学公式)

When HTML files contain KaTeX-rendered math, follow these rules:

### Backup First
Before modifying ANY HTML file, create a backup:
```bash
mkdir -p backup/$(date +%Y-%m-%d) && cp file.html backup/$(date +%Y-%m-d)/
```
Store backups in a separate `backup/` folder, NOT alongside working files.

### `<` in Inline Math (`$...$`)
In HTML, `<` is always parsed as a tag start by the browser **before** KaTeX runs.
So `$b<a<c$` → browser sees `<a` as an HTML tag → DOM corruption.
**Fix:** Always use `&lt;` when `<` is followed by a letter inside `$...$`:
- `$b<a<c$` → `$b&lt;a&lt;c$`
- `$0<a<1$` → `$0&lt;a&lt;1$`
- `$a_n<a_{n+1}$` → `$a_n&lt;a_{n+1}$`
- `$b<a_n<c$` → `$b&lt;a_n&lt;c$`
Numbers after `<` (like `$x<0$`) are safe — they don't form HTML tags.

### `$$` Display Math Pairing
Each `$$` must open AND close, and content between must be PURE LaTeX:
```
$$                    ← opening
f(x) = x^2           ← only LaTeX here, NO Chinese text, NO HTML
$$                    ← closing
```
If Chinese text follows the math, close `$$` first, then use `$...$` for inline:
```
$$                    ← correct
f(x) = x^2
$$
所以 $f(x) \ge 0$   ← Chinese outside display math
```

### CDN for Chinese Users
`cdn.jsdelivr.net` is often slow/blocked in China. Use `cdnjs.cloudflare.com` instead:
```
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.16.9/katex.min.css">
```

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
- [ ] **Sidebar works**: nav links jump correctly, hierarchy correct
- [ ] **Code blocks**: dark background + syntax highlighting
- [ ] **Mobile**: hamburger menu + responsive
- [ ] **Color consistency**: CSS variables, chapter colors unified
- [ ] **Readability**: line-height ≥ 1.65, sufficient contrast
- [ ] **Motion**: respect `prefers-reduced-motion`
- [ ] **No empty code blocks**
- [ ] **KaTeX $$ even**: count `$$` occurrences — must be even (all paired)
- [ ] **KaTeX < check**: grep for `\$[^$]*<[a-z]` in `$...$` — replace `<` with `&lt;` if found
- [ ] **KaTeX CDN**: use `cdnjs.cloudflare.com/ajax/libs/KaTeX/` for Chinese users
- [ ] **Backup**: verify backup exists in `backup/YYYY-MM-DD/` before modifying
- [ ] **Sidebar TOC**: auto-generated from headings, mobile hamburger works (for docs with 3+ sections)
- [ ] **Analysis layout**: card-based chapters, color-coded sections, formula cards (for 复习资料 type)
- [ ] **Click-to-reveal**: present only if NOT open-book exam
