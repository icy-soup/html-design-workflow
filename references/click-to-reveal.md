# Click-to-Reveal (点击显示)

Interactive toggle for examples, answers, proofs, and memory items.  
**Skip this for open-book exams** — no need to hide content for open-book reviews.

## Structure

```html
<div class="reveal" onclick="toggleReveal(this)">
    <div class="reveal-label">点击显示答案</div>
    <div class="reveal-content">
        <!-- hidden content: answer, proof, example, etc. -->
    </div>
</div>
```

For inline memory cards:
```html
<div class="reveal reveal-memory" onclick="toggleReveal(this)">
    <div class="reveal-label">📝 点击显示记忆口诀</div>
    <div class="reveal-content">
        内容...
    </div>
</div>
```

## CSS

```css
.reveal {
    border: 1px dashed var(--border, #ccc);
    border-radius: 8px; margin: 10px 0; cursor: pointer;
    transition: all .2s;
}
.reveal:hover { border-color: var(--accent, #8b3a62); }

.reveal-label {
    padding: 10px 14px; font-size: 13.5px; font-weight: 600;
    color: var(--accent, #8b3a62); text-align: center;
    font-family: 'Inter', sans-serif; user-select: none;
}
.reveal .reveal-content {
    display: none; padding: 0 14px 10px; font-size: 14px;
}
.reveal.open .reveal-content { display: block; }
.reveal.open .reveal-label { display: none; }

/* Memory card variant */
.reveal-memory {
    background: #fffef5; border-color: #f0d89a;
}
.reveal-memory:hover { border-color: #d4a843; }
.reveal-memory .reveal-label { color: #856404; }
```

## JavaScript

```javascript
function toggleReveal(el) {
    el.classList.toggle('open');
}
```

Simple toggle function — no animation needed. The open state shows the content and hides the label.

## When to Use

| Content type | Use | Label text |
|---|---|---|
| Exercise answer | `.reveal` | "点击显示答案" |
| Proof step | `.reveal` | "点击显示证明" |
| Memory aid | `.reveal-memory` | "📝 点击显示口诀" |
| Example detail | `.reveal` | "点击展开示例" |

## Important: Skip for Open-Book Exams

If the user explicitly says the exam is open-book (开卷考), do NOT add click-to-reveal anywhere. Open-book reviews should show all content immediately with no toggles.
