# Mock Exam Layout (模考布局)

Alternative layout for 复习资料/习题集/模拟考 HTML documents. Use when the user wants a **mock-exam style** (试题卡片 + 可展开答案 + 深色渐变header), as opposed to the chapter-card style of `analysis-layout.md`.

**Design principle:** Clean, warm, paper-like feel with a dramatic dark gradient header that gives visual weight. White sidebar for table of contents. Mock-exam structure: knowledge sections followed by example cards with collapsible answers.

## When to use

- User says "模拟题", "习题集", "试卷", "试题", "mock exam", "practice problems"
- User wants "考试风格" or "试卷风格" layout with questions and hidden answers
- User shows an existing review-ch*.html as a style reference

## Core Design Tokens

```css
:root {
    --bg: #f8f7f4;           /* warm beige page background */
    --card-bg: #fff;          /* white card */
    --text: #2c2c2c;         /* near-black text */
    --text-secondary: #555;
    --accent: #8b3a62;        /* deep rose primary accent */
    --accent-dark: #5c2a3e;   /* darker rose for gradient */
    --accent-deep: #2c1810;   /* darkest for gradient base */
    --accent-light: #f0e6ed;
    --border: #e8e4e0;        /* warm light border */
    --gold: #c9a87c;          /* gold decorative accent */
    --sidebar-width: 250px;
    --content-max-width: 800px;
}
```

Typography: `'Noto Serif SC'` for body (study-note feel), `'Noto Sans SC'` for UI elements (sidebar, tags, buttons). Line-height 1.8 for readability.

## Layout Structure

Fixed white sidebar (left) + centered main content area:

```html
<body>
  <!-- Sidebar -->
  <nav class="sidebar">
    <h2>📖 目&ensp;录</h2>
    <a href="#sec1">§1 Section Title</a>
    <a href="#sec1-1" class="sub">Sub-section</a>
    <div class="divider"></div>
    <!-- more sections -->
  </nav>

  <!-- Main content -->
  <div class="wrapper">
    <div class="main">
      <!-- Header -->
      <div class="header">
        <h1>Chapter Title</h1>
        <p>Subtitle / description</p>
      </div>

      <!-- Section cards -->
      <div class="section-card" id="sec1">
        <h3>§1 Section Title</h3>
        <!-- content -->

        <!-- Example cards with toggle -->
        <div class="example-card">
          <div class="q">
            <span class="badge tag-medium">Tag</span><span class="pri-high">🔴必看</span>
            Question text
          </div>
          <button class="toggle-btn" onclick="toggleAnswer(this)">查看答案</button>
          <div class="answer-box">
            Answer content
          </div>
        </div>
      </div>
    </div>
  </div>
</body>
```

## Header

Dark gradient header with decorative gold bottom line. The gradient uses three stops for depth. Bottom decorative line uses a gold-rose-gold horizontal gradient.

```css
.header {
    background: linear-gradient(135deg, #2c1810 0%, #5c2a3e 50%, #8b3a62 100%);
    color: #fff;
    padding: 40px 36px;
    border-radius: 0 0 20px 20px;
    margin-bottom: 32px;
    position: relative;
}
.header::after {
    content: '';
    position: absolute;
    bottom: 0; left: 0; right: 0;
    height: 4px;
    background: linear-gradient(90deg, #c9a87c, #8b3a62, #c9a87c);
    border-radius: 0 0 20px 20px;
}
.header h1 { font-size: 26px; font-weight: 700; margin-bottom: 6px; }
.header p { font-size: 14px; opacity: .85; font-family: 'Noto Sans SC', sans-serif; }
```

Optionally include a legend bar inside the header showing priority levels:
```html
<div style="font-family:'Noto Sans SC',sans-serif; font-size:12px; margin-top:6px; display:flex; gap:12px; flex-wrap:wrap; background:rgba(255,255,255,0.15); padding:5px 12px; border-radius:6px;">
  <span><span style="display:inline-block;width:10px;height:10px;border-radius:50%;background:#fde8e8;border:1px solid #e8b4b4;"></span> 🔴 必看</span>
  <span><span style="display:inline-block;width:10px;height:10px;border-radius:50%;background:#fef5e0;border:1px solid #e8d5a0;"></span> 🟡 选看</span>
  <span><span style="display:inline-block;width:10px;height:10px;border-radius:50%;background:#e8e8e8;border:1px solid #ddd;"></span> ⚪ 可略过</span>
</div>
```

## Sidebar (White Variant)

Unlike the dark sidebar in `sidebar-toc.md`, this layout uses a **white sidebar** with accent-colored heading and subtle dividers. The sidebar is fixed and not auto-generated from headings (manual links), suitable for exam review docs with stable structure.

```css
.sidebar {
    position: fixed;
    top: 0; left: 0;
    width: 250px;
    height: 100vh;
    overflow-y: auto;
    background: #fff;
    border-right: 1px solid #e8e4e0;
    padding: 28px 14px;
    z-index: 100;
    font-family: 'Noto Sans SC', sans-serif;
}
.sidebar h2 {
    font-size: 14px;
    color: #8b3a62;
    margin-bottom: 14px;
    padding-bottom: 8px;
    border-bottom: 2px solid #8b3a62;
    letter-spacing: 2px;
}
.sidebar a {
    display: block;
    font-size: 13px;
    color: #555;
    text-decoration: none;
    padding: 4px 8px;
    border-radius: 4px;
    transition: all .2s;
    margin-bottom: 1px;
}
.sidebar a:hover { background: #f3eeea; color: #8b3a62; }
.sidebar a.sub { padding-left: 18px; font-size: 12px; color: #888; }
.sidebar .divider {
    height: 1px; background: #e8e4e0; margin: 6px 0;
}
.sidebar::-webkit-scrollbar { width: 4px; }
.sidebar::-webkit-scrollbar-thumb { background: #ccc; border-radius: 2px; }
```

The sidebar is hidden on mobile (see Responsive section). The main content shifts via `margin-left: 250px`:

```css
.wrapper {
    margin-left: 250px;
    display: flex;
    justify-content: center;
    min-height: 100vh;
}
.main {
    width: 100%;
    max-width: 800px;
    padding: 0 24px 60px;
}
```

## Section Cards

White rounded cards containing knowledge blocks. Each section is a major topic area.

```css
.section-card {
    background: #fff;
    border-radius: 12px;
    padding: 28px 30px;
    margin-bottom: 24px;
    box-shadow: 0 1px 4px rgba(0,0,0,.06);
    transition: box-shadow .25s;
}
.section-card:hover { box-shadow: 0 4px 16px rgba(0,0,0,.08); }

.section-card h3 {
    font-size: 20px;
    color: #8b3a62;
    margin-bottom: 16px;
    padding-bottom: 8px;
    border-bottom: 2px solid #f0e8e4;
}
.section-card h4 {
    font-size: 16px;
    color: #4a3038;
    margin: 16px 0 8px;
}
.section-card ul, .section-card ol { padding-left: 22px; }
.section-card li { margin-bottom: 4px; font-size: 15px; }
```

## Example Cards (Question + Toggle Answer)

Each question is a `.example-card` with a left accent border, containing a `.q` header, a toggle button, and a hidden `.answer-box`. This is the core interaction of the mock exam layout.

```css
.example-card {
    background: #faf8f6;
    border-left: 4px solid #8b3a62;
    border-radius: 0 8px 8px 0;
    padding: 16px 20px;
    margin: 12px 0;
}
.example-card .q {
    font-weight: 600;
    margin-bottom: 6px;
    font-size: 15px;
}
.example-card .q .badge {
    display: inline-block;
    font-family: 'Noto Sans SC', sans-serif;
    font-size: 11px;
    font-weight: 600;
    padding: 2px 10px;
    border-radius: 10px;
    margin-right: 8px;
}
.example-card .answer-box {
    margin-top: 10px;
    display: none;
    background: #f3eeea;
    border-radius: 8px;
    padding: 14px 18px;
    font-size: 14.5px;
}
.example-card .answer-box hr {
    border: none;
    border-top: 1px dashed #ccc;
    margin: 10px 0;
}
.example-card .toggle-btn {
    font-family: 'Noto Sans SC', sans-serif;
    font-size: 13px;
    color: #8b3a62;
    background: none;
    border: 1px solid #8b3a62;
    border-radius: 6px;
    padding: 3px 14px;
    cursor: pointer;
    margin-top: 6px;
    transition: all .2s;
}
.example-card .toggle-btn:hover { background: #8b3a62; color: #fff; }
```

JavaScript for toggle:

```javascript
function toggleAnswer(btn) {
    var box = btn.nextElementSibling;
    if (box.style.display === 'block') {
        box.style.display = 'none';
        btn.textContent = '查看答案';
    } else {
        box.style.display = 'block';
        btn.textContent = '收起答案';
    }
}
```

### Self-test variant

For the final self-test section, use green left border instead of rose:
```css
.test-section .example-card { border-left-color: #2a7a2a; }
.test-section .example-card .q .badge { background: #e6f4e6; color: #2a7a2a; }
```

## Priority Tags

Three levels for marking question importance — placed inline after the topic badge:

```css
.pri-high {
    display: inline-block; font-family: 'Noto Sans SC', sans-serif;
    font-size: 11px; font-weight: 700; padding: 1px 8px; border-radius: 8px;
    margin-left: 6px; vertical-align: middle;
    background: #fde8e8; color: #c0392b; border: 1px solid #e8b4b4;
}
.pri-mid {
    display: inline-block; font-family: 'Noto Sans SC', sans-serif;
    font-size: 11px; font-weight: 600; padding: 1px 8px; border-radius: 8px;
    margin-left: 6px; vertical-align: middle;
    background: #fef5e0; color: #b8860b; border: 1px solid #e8d5a0;
}
.pri-skip {
    display: inline-block; font-family: 'Noto Sans SC', sans-serif;
    font-size: 11px; font-weight: 400; padding: 1px 8px; border-radius: 8px;
    margin-left: 6px; vertical-align: middle;
    background: #e8e8e8; color: #999; border: 1px solid #ddd;
}
```

Usage: `<span class="pri-high">🔴必看</span>` / `<span class="pri-mid">🟡选看</span>` / `<span class="pri-skip">⚪略过</span>`

## Topic Badges

Color-coded badges for question topic type:

```css
.tag-box {
    display: inline-block; font-family: 'Noto Sans SC', sans-serif;
    font-size: 11px; font-weight: 600; padding: 2px 10px;
    border-radius: 10px; margin-right: 6px;
}
.tag-easy { background: #e6f4e6; color: #2a7a2a; }
.tag-medium { background: #fef5e0; color: #b8860b; }
.tag-hard { background: #fde8e8; color: #c0392b; }
.tag-star { background: #f0e6f6; color: #7b2d8b; }
```

## Theorem Highlights

```css
.theorem { font-weight: 600; color: #5c2a3e; }
```

## Tip Box

```css
.tip-box {
    background: #f8f3ef;
    border-left: 4px solid #c9a87c;
    border-radius: 0 8px 8px 0;
    padding: 10px 16px;
    margin: 10px 0;
    font-size: 14px;
    font-family: 'Noto Sans SC', sans-serif;
}
```

## Tables

```css
.kv-table { width: 100%; border-collapse: collapse; margin: 10px 0; font-size: 14px; }
.kv-table td { padding: 6px 12px; border: 1px solid #e8e4e0; }
.kv-table tr:first-child td { background: #f3eeea; font-weight: 600; }
```

## Definition Highlight Box

For key definitions, use a subtle gray rounded box:
```css
/* applied inline via style attribute or a dedicated class */
.def-box {
    background: #f3eeea; border-radius: 6px; padding: 8px 14px; font-size: 15px;
}
```

## Responsive Design

At 768px breakpoint, hide sidebar and remove margin:

```css
@media (max-width: 768px) {
    .sidebar { display: none; }
    .wrapper { margin-left: 0; }
    .main { padding: 0 12px 40px; }
    .header { padding: 28px 20px; }
    .section-card { padding: 20px 16px; }
    .example-card { padding: 14px 14px; }
}
```

## Footer

```css
/* centered footer text */
.footer {
    text-align: center; padding: 20px 0 40px;
    font-family: 'Noto Sans SC', sans-serif; font-size: 13px; color: #999;
}
```

## KaTeX

Use the standard approach from SKILL.md (katex.renderToString). For this layout, target these selectors for formula rendering:
- `.section-card li`, `.section-card p`, `.section-card td`
- `.example-card .q`, `.example-card .answer-box`
- `.tip-box`

See SKILL.md KaTeX section for implementation details and error handling.

## Delivery Checklist

- [ ] **Sidebar**: white background, accent heading, subtle dividers, hidden on mobile
- [ ] **Header**: dark gradient (3-stop), decorative gold-rose-gold bottom line
- [ ] **Section cards**: white, rounded, hover shadow
- [ ] **Example cards**: left accent border, toggle button + hidden answer box
- [ ] **Priority tags**: pri-high / pri-mid / pri-skip correctly colored
- [ ] **Topic badges**: tag-easy / tag-medium / tag-hard / tag-star
- [ ] **KaTeX**: follows SKILL.md approach (renderToString, not auto-render)
- [ ] **Mobile**: sidebar hidden, content full-width
- [ ] **No Edit/heredoc issues**: follow skill's file editing rules
