---
name: html-design-workflow
description: >-
  Use when the user asks to create any HTML artifact — pages, slides,
  documents, dashboards, landing pages, or web UI components. Triggers on
  Chinese phrases like 做HTML, 做网页, 做幻灯片, 做PPT, 做演示, 做页面, 做组件,
  做仪表盘, 前端设计, UI设计, or any HTML/CSS/JS creation request no matter how
  casually phrased. Also use when the user says 学习这个风格, 参考这个页面,
  提取风格, or shows an HTML file they want to emulate.
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

### Step 1: Choose aesthetic direction
Pick ONE clear tone before writing code: brutal minimalism, maximalist chaos,
retro-futuristic, organic, luxury, editorial, soft pastel, industrial, etc.
The choice must feel intentional, not generic.

### Step 2: Pick theme and fonts
Borrow from theme-factory's 8 preset themes if no preference. Avoid Inter,
Roboto, Arial, Space Grotesk. Prefer Outfit, Plus Jakarta Sans, DM Sans,
JetBrains Mono (code). For Chinese: Noto Sans SC or system hei.

### Step 3: Build layout
- Document: sidebar 260-290px + content max 780-920px, padding 40-56px
- Use CSS variables for design tokens
- Staggered fadeInUp on load, respect `prefers-reduced-motion`

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
- 三明治 structure: plain-language metaphor → technical detail → visualization
- Key concepts get `.info-card` (colored left border, light background)
- Comparisons get `.compare-split` (red left / green right)

### Step 6: Deliver
See the delivery checklist below. **Check before claiming done.**

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
