# Default Analysis Layout (分析布局)

Default visual style for 复习资料/考试备考/知识整理/技术文档 HTML output. Skip the "choose aesthetic direction" step for these types — use this layout directly.

## Core Design Tokens

```css
:root {
    --bg: #f8f7f4;           /* soft beige page background */
    --card-bg: #fff;          /* white card */
    --text: #1a1a2e;          /* near-black text */
    --text-secondary: #555;
    --accent: #8b3a62;        /* deep rose primary accent */
    --accent-light: #f0e6ed;
    --border: #e0d8d0;        /* warm light border */

    /* Chapter colors */
    --c1: #8b3a62;  --c2: #3a6b8b;
    --c3: #4a7c59;  --c4: #b8860b;  --c5: #b13e3e;
}
```

Typography: `'Noto Serif SC'` (Chinese) + `'Inter'` (code/meta). Serif for body gives a study-note feel.

## Header

```css
.header {
    background: linear-gradient(135deg, #1a1a2e, #3a2a4e, #5c3a6a);
    color: white; padding: 44px 0 32px; text-align: center;
}
.header::after {
    content: ''; position: absolute; bottom: 0; left: 0; right: 0;
    height: 3px; background: linear-gradient(90deg, #c9a27b, var(--accent), #c9a27b);
}
```

Structure: `<h1>` title, `.subtitle` (smaller, opacity .8), `.meta` (exam info, Inter font).

## Chapter Cards

Each major section is a `.chapter` card:

```html
<div class="chapter ch1">
    <div class="chapter-title"><span class="num">一</span>标题 <span class="freq high">标签</span></div>
    <div class="section">...</div>
</div>
```

```css
.chapter {
    background: var(--card-bg); border: 1px solid var(--border);
    border-radius: 12px; padding: 24px 28px 20px; margin-bottom: 18px;
}
.chapter-title {
    font-size: 20px; font-weight: 900; display: flex; align-items: center; gap: 10px;
}
.chapter-title .num {
    display: inline-flex; align-items: center; justify-content: center;
    width: 32px; height: 32px; border-radius: 50%;
    color: white; font-size: 14px; flex-shrink: 0;
}
/* Per-chapter colors */
.ch1 .num { background: var(--c1); } .ch1 .chapter-title { color: var(--c1); }
.ch2 .num { background: var(--c2); } .ch2 .chapter-title { color: var(--c2); }
/* ... etc */
```

`.ch1` through `.ch5` give each chapter a distinct color identity.

## Section Blocks

Inside each chapter, sub-sections separated by `<hr class="divider">`:

```html
<div class="section">
    <div class="section-name">标题</div>
    <div class="content">内容</div>
</div>
<hr class="divider">
```

## Special Boxes

### Formula Card (公式卡片)
Light purple background, bordered. Has an optional `.tag` label:
```html
<div class="formula-card">
    <span class="tag">期望</span>
    $E[X] = \sum x_k p_k$
</div>
```
```css
.formula-card {
    background: #f8f6ff; border: 1px solid #e0d8f0;
    border-radius: 8px; padding: 10px 16px; margin: 6px 0; font-size: 14px;
}
.formula-card .tag {
    display: inline-block; font-family: 'Inter', sans-serif;
    font-size: 11px; font-weight: 600; background: #e0d8f0;
    color: #5a3a7a; padding: 0 7px; border-radius: 4px; margin-right: 6px;
}
```

### Theorem Box (定理框)
Left accent border, light background:
```html
<div class="theorem">
    <span class="label">定理名：</span>
    内容...
</div>
```
```css
.theorem {
    background: #faf8f5; border-left: 3px solid var(--accent);
    border-radius: 0 6px 6px 0; padding: 8px 14px; margin: 6px 0; font-size: 14px;
}
.theorem .label { font-weight: 700; color: var(--accent); }
```

### Tip Box (提示框)
Purple-ish background, left accent border:
```html
<div class="tip-box">
    <strong>💡 提示：</strong>内容...
</div>
```
```css
.tip-box {
    background: #f0eaf0; border-left: 3px solid var(--accent);
    border-radius: 0 8px 8px 0; padding: 10px 14px; margin: 10px 0 6px;
    font-size: 13.5px; color: #4a2a3a;
}
```

### Error Box (易错警示)
Red-tinted, for common mistakes:
```html
<div class="err-box">
    ❌ 错误写法<br>
    <span class="ok">✅ 正确写法</span>
</div>
```
```css
.err-box {
    background: #fdf0f0; border-left: 3px solid #c0392b;
    border-radius: 0 8px 8px 0; padding: 10px 14px; margin: 10px 0 6px;
    font-size: 13.5px; color: #6b2a2a;
}
.err-box .ok { color: #27ae60; font-weight: 700; }
.err-box .bad { color: #c0392b; font-weight: 700; }
```

### Mnemonic (记忆口诀)
Yellow pill badge for memory aids:
```html
<span class="mnemonic">📝 记忆口诀：内容</span>
```
```css
.mnemonic {
    display: inline-block; font-family: 'Inter', sans-serif;
    background: #fff3cd; border: 1px solid #f0d89a;
    border-radius: 6px; padding: 4px 12px; font-size: 13px; font-weight: 600;
    color: #856404; margin: 4px 0;
}
```

## Tables

```css
.method-table {
    width: 100%; border-collapse: collapse; font-size: 14px; margin: 8px 0;
}
.method-table th {
    background: #f0e6ed; color: var(--accent); font-weight: 700;
    padding: 8px 12px; text-align: left; border: 1px solid var(--border);
}
.method-table td {
    padding: 7px 12px; border: 1px solid var(--border);
}
.method-table tr:nth-child(even) { background: #faf9f7; }
```

## Badges (频率标签)

```html
<span class="freq high">高频</span>
<span class="freq mid">中频</span>
<span class="freq low">低频</span>
```
```css
.freq {
    display: inline-block; font-family: 'Inter', sans-serif;
    font-size: 11px; font-weight: 600; padding: 1px 8px; border-radius: 10px;
    margin-left: 4px; vertical-align: middle;
}
.freq.high { background: #fce8e8; color: #b13e3e; }
.freq.mid { background: #fef5e0; color: #b8860b; }
.freq.low { background: #e8f0ea; color: #4a7c59; }
```

## Info Bar

Top exam-info strip:
```html
<div class="exam-info">
    <span>📅 时间：<span class="hl">2026.6.22</span></span>
    <span>📖 开卷考 · 范围：Ch1~Ch3/Ch5/Ch7/Ch8</span>
</div>
```
```css
.exam-info {
    display: flex; flex-wrap: wrap; gap: 10px; justify-content: center;
    background: var(--card-bg); border: 1px solid var(--border); border-radius: 10px;
    padding: 14px 22px; margin-bottom: 24px; font-size: 14px;
}
.exam-info .hl { color: var(--accent); font-weight: 600; }
```

## Part Badges (部分标记)

```css
.part-badge {
    display: inline-block; font-family: 'Inter', sans-serif;
    font-size: 12px; font-weight: 700; padding: 3px 12px; border-radius: 12px;
}
.part1 { background: #fce8e8; color: #b13e3e; }
.part2 { background: #e8f0ea; color: #4a7c59; }
```

## Divider

```css
hr.divider {
    border: none; border-top: 1px solid var(--border); margin: 14px 0;
}
```

## Layout Rules

- Container max-width: 880px, padding 28px 24px
- Line-height: 1.9 for readability
- Font size: 14px for content, 15px for section names, 20px for chapter titles
- Responsive breakpoint at 600px: reduce header/container padding, chapter padding
