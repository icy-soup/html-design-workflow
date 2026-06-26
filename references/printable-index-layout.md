# Printable Index Layout (打印考点索引布局)

A compact two-column index layout with editable page-number underlines, designed for printing and manual annotation. Each "page" is a fixed A4-sized block. Uses CSS multi-column (`column-count: 2`) so content flows naturally: left column fills top→bottom, then spills to right column. Contenteditable underlines let the user fill in PPT/reference page numbers, saved to localStorage.

## When to use

- **打印考点索引/知识点清单** — the user wants a chapter index they can print and fill in page numbers
- **复习自查表** — compact knowledge-point checklist with page references
- **大纲式整理** — hierarchical topic listing, not full-text review content
- This is the format the user approved after iterative feedback. ALWAYS use this layout for index-type content.

**Do NOT use** for full-text review documents with formulas, code, or diagrams — use `analysis-layout.md` instead.

## Core Design Tokens

```css
:root {
    --body-bg: #e8e4df;        /* warm gray page background */
    --paper-bg: #fff;           /* white paper */
    --text: #1a1a2e;            /* near-black text */
    --text2: #555;              /* secondary text */
    --border: #ccc;             /* card borders */
    --accent: #8b3a62;          /* deep rose accent for focus */

    /* chapter number colors */
    --c1: #8b3a62; --c2: #3a6b8b; --c3: #4a7c59; --c4: #b8860b; --c5: #b13e3e;
}
```

Typography: `'Noto Serif SC'` (body), `'Inter'` (page numbers and tags).

Font sizes (compact, to fill A4 page):
- Body: 11.5–12px, line-height 1.5
- Block title (`.bt`): 11.5px
- `.ss` sub-items: 10px, line-height 1.45
- Tags: 7.5px
- Editable underline (`.ul`): 9.5px

## Structure

```
body (warm gray #e8e4df background)
  └─ .paper (white, box-shadow, 720px max-width, centered)
       ├─ .header (column-span: all, centered title)
       ├─ .page (1020–1050px fixed height, column-count: 2)
       │    ├─ .block (break-inside: avoid, bordered card for each section)
       │    │    ├─ .bt (block title with colored number badge)
       │    │    ├─ .t → .tl + .ul[contenteditable] (topic + blank underline)
       │    │    └─ .ss (sub-item, indented, with .ul underline)
       │    └─ .footer (page number, column-span: all)
       ├─ .page-break (dashed line with "— 续下页 —")  // only when multiple pages
       └─ .page (next page...)
```

### Key differences from old float layout

| Old (float) | New (CSS columns) |
|-------------|-------------------|
| Manual `.col-left` / `.col-right` divs | No column divs — browser handles flow |
| Content manually split left/right | Content flows left→right automatically |
| Uneven column fill = wasted space | Natural column fill |
| Header/footer in each column | `column-span: all` spans full width |
| `overflow: hidden` clips excess | Blocks with `break-inside: avoid` stay intact |

## Key Components

### Paper container
```css
.paper {
    max-width: 720px;
    margin: 0 auto;
    background: #fff;
    box-shadow: 0 1px 8px rgba(0,0,0,.1);
}
```

### A4 page block with CSS columns
```css
.page {
    padding: 10px 14px;
    height: 1020px;             /* A4 height at 720px width */
    column-count: 2;
    column-fill: auto;
    column-gap: 14px;
    overflow: hidden;           /* clips content that would spill */
    position: relative;
}
```

### Page break separator (for multi-page)
```css
.page-break {
    border-top: 2px dashed #bbb;
    margin: 0 18px;
    position: relative;
}
.page-break::after {
    content: "— 续下页 —";
    position: absolute; top: -9px; left: 50%; transform: translateX(-50%);
    background: #fff; padding: 0 16px; font-size: 10px; color: #999;
}
```

### Header spanning both columns
```css
.header {
    text-align: center; padding: 6px 0 5px;
    border-bottom: 2px solid #ccc; margin-bottom: 6px;
    column-span: all;
}
```

### Footer spanning both columns
```css
.footer {
    text-align: center; color: #999; font-size: 8.5px;
    padding: 3px 0; border-top: 1px solid #ddd; margin-top: 3px;
    column-span: all;
}
```

### Section card / block
```css
.block {
    border: 1px solid #ccc;
    padding: 3px 6px;
    margin-bottom: 5px;
    break-inside: avoid;                    /* prevent splitting across columns */
    -webkit-column-break-inside: avoid;
}
.bt {
    font-size: 11.5px; font-weight: 700;
    padding-bottom: 2px; margin-bottom: 2px;
    border-bottom: 1px solid #ddd;
}
.bt .num {      /* colored number badge */
    display: inline-block; width: 15px; height: 15px; line-height: 15px;
    text-align: center; font-size: 8.5px; font-weight: 700;
    color: #fff; border-radius: 50%; margin-right: 3px;
}
.n1 { background: #8b3a62; } .n2 { background: #3a6b8b; }
.n3 { background: #4a7c59; } .n4 { background: #b8860b; } .n5 { background: #b13e3e; }
```

### Topic line + editable blank underline
```css
.t { line-height: 1.55; }
.tl { display: inline; }
.tl .ul {
    display: inline-block; width: 32px;
    border-bottom: 1px dashed #bbb; margin-left: 2px;
    outline: none; cursor: text; text-align: center;
    font-size: 9.5px; color: #333; font-family: 'Inter', sans-serif;
}
.tl .ul:focus {
    border-bottom-color: #8b3a62; border-bottom-style: solid;
    background: #f8f0f4;
}
```

### Sub-items (indented, smaller)
```css
.ss {
    padding-left: 11px; line-height: 1.45;
    font-size: 10px; color: #555;
}
.ss .ul {   /* same pattern as .tl .ul but font-size: 9px */
    display: inline-block; width: 32px;
    border-bottom: 1px dashed #bbb; margin-left: 2px;
    outline: none; cursor: text; text-align: center;
    font-size: 9px; color: #333; font-family: 'Inter', sans-serif;
}
.ss .ul:focus {
    border-bottom-color: #8b3a62; border-bottom-style: solid;
    background: #f8f0f4;
}
```

### Exam-frequency tags
```css
.tag { display: inline; font-size: 7.5px; padding: 0 3px;
       border-radius: 5px; font-family: 'Inter', sans-serif;
       font-weight: 500; margin-left: 1px; }
.tag.high { background: #fde8e8; color: #c0392b; border: 1px solid #f5c6c6; }
.tag.mid  { background: #fef3d5; color: #b8860b; border: 1px solid #f5e0a0; }
.tag.info { background: #e3f0f7; color: #2c6b8a; border: 1px solid #bdd7e8; }
```

## CRITICAL: No hardcoded page numbers

The content from the source (e.g., first AI's output) includes reference PPT page numbers like "P2", "P8" etc. These are for the AI's reference only.

**DO NOT** put these page numbers into the HTML text content. Every topic/sub-item should end with a blank `contenteditable` underline (`.ul`), and the user fills in the page numbers themselves while studying.

✅ Correct:
```html
<div class="ss">GPIO定义<span class="ul" contenteditable="true"></span></div>
```

❌ Wrong (do NOT include "P2" in text):
```html
<div class="ss">GPIO定义    P2<span class="ul" contenteditable="true"></span></div>
```

## Editable persistence script

Add at end of `<body>` before `</body>`:
```html
<script>
(function() {
  var els = document.querySelectorAll('[contenteditable]');
  var key = 'gpio_';  // change per chapter
  els.forEach(function(el, i) {
    var k = key + i;
    var v = localStorage.getItem(k);
    if (v) el.textContent = v;
    el.addEventListener('input', function() {
      localStorage.setItem(k, el.textContent);
    });
  });
})();
</script>
```

## Print adaptations

```css
@page { margin: 10mm 8mm; }

@media print {
    body { background: #fff; font-size: 11px; }
    .paper { max-width: 100%; margin: 0; box-shadow: none; }
    .page { height: auto; overflow: visible; padding: 6px 10px; page-break-after: always; column-fill: auto; }
    .page-break { display: none; }
    .page-break::after { display: none; }
    .block { border: 0.5px solid #aaa; margin-bottom: 3px; padding: 2px 5px; }
    .header { margin-bottom: 3px; }
    .header h1 { font-size: 13px; }
    .ul { color: #ccc !important; }
    .tag { background: none !important; border: 0.5px solid currentColor; }
}
```

## Page splitting strategy

When content exceeds one A4 page, manually split into multiple `.page` divs with `.page-break` in between. Each `.page` is an independent CSS columns container — content does NOT flow across pages automatically.

Estimate capacity: at 11.5px font / 1.5 line-height, each A4 page fits roughly 6–8 blocks (~50–60 lines of compact content across both columns).

**Working with source content containing page references:**
1. Source format usually: `topic    description    PXX`
2. Remove the `PXX` from text
3. Append `<span class="ul" contenteditable="true"></span>` to the description text instead
4. Each sub-item or topic line gets its own blank underline for the user to fill in

## Differences from analysis-layout

| Aspect | analysis-layout | printable-index-layout |
|--------|----------------|----------------------|
| Purpose | Full review content with formulas/diagrams | Compact index with page references |
| Columns | Sidebar + single content column | CSS `column-count: 2` (auto-flow) |
| Page simulation | No | Fixed A4 height blocks |
| Editable fields | No | contenteditable underlines with localStorage |
| Print target | Screen-first, print secondary | Print-first with screen preview |
| KaTeX formulas | Yes | No |
| Content flow | Manual section placement | Left→right auto flow within columns |
| Hardcoded P numbers | N/A | **NO** — all page refs are blank fields for user |

## Delivery Checklist Additions

In addition to the main html-design-workflow delivery checklist, for index-layout HTML specifically:

- [ ] **No hardcoded P numbers**: grep for `P\d+` or `P\d+-\d+` in body content — should be ZERO results (only in header/title, not in topic text)
- [ ] **CSS columns used**: confirm `column-count: 2` on `.page`, NOT float left/right column divs
- [ ] **`break-inside: avoid`** on `.block` to prevent blocks splitting across columns
- [ ] **`column-span: all`** on header and footer
- [ ] **Content fits in page height**: content should fill both columns without being clipped by `overflow: hidden`
- [ ] **Dense layout**: font 11.5–12px, minimal padding, compact spacing
- [ ] **localStorage key** uses a unique prefix per chapter (e.g., `gpio_`, `uart_`, `tim_`)
