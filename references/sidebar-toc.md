# Sidebar Table of Contents (侧边栏目录)

Sidebar with TOC navigation is the DEFAULT layout for 复习资料/考试备考/知识整理 HTML documents.

## Structure

```html
<body>
    <nav class="sidebar" id="sidebar">
        <div class="sidebar-header">
            <div class="sidebar-title">目录</div>
            <button class="sidebar-close" id="sidebarClose">&times;</button>
        </div>
        <div class="toc" id="toc">
            <!-- populated by JS -->
        </div>
    </nav>
    <div class="sidebar-overlay" id="sidebarOverlay"></div>
    <button class="hamburger" id="hamburger">&#9776;</button>
    <div class="content-wrapper">
        <!-- ALL page content here -->
    </div>
</body>
```

## CSS

```css
/* Layout containers */
body {
    display: flex; margin: 0; min-height: 100vh;
}
.sidebar {
    position: fixed; top: 0; left: 0; width: 270px; height: 100vh;
    background: #1a1a2e; color: #ccc; overflow-y: auto; z-index: 100;
    transition: transform .3s ease;
}
.content-wrapper {
    flex: 1; margin-left: 270px; min-height: 100vh;
    background: var(--bg, #f8f7f4);
}

/* Sidebar header */
.sidebar-header {
    display: flex; justify-content: space-between; align-items: center;
    padding: 20px 18px 12px; border-bottom: 1px solid #333;
}
.sidebar-title {
    font-family: 'Inter', sans-serif; font-size: 13px; font-weight: 700;
    color: #aaa; letter-spacing: 2px; text-transform: uppercase;
}

/* TOC links */
.toc { padding: 8px 0; }
.toc a {
    display: block; padding: 6px 18px 6px 20px;
    font-size: 13.5px; color: #bbb; text-decoration: none;
    border-left: 2px solid transparent; transition: all .15s;
    font-family: 'Noto Serif SC', serif; line-height: 1.6;
}
.toc a:hover { color: #fff; background: rgba(255,255,255,.04); }
.toc a.active {
    color: #fff; border-left-color: var(--accent, #8b3a62);
    background: rgba(255,255,255,.06);
}
.toc .toc-h2 { padding-left: 20px; font-size: 13px; }
.toc .toc-h3 { padding-left: 34px; font-size: 12.5px; color: #888; }

/* Hamburger */
.hamburger {
    display: none; position: fixed; top: 12px; left: 12px; z-index: 200;
    background: #1a1a2e; color: #fff; border: none; border-radius: 6px;
    width: 38px; height: 38px; font-size: 20px; cursor: pointer;
}
.sidebar-close { display: none; }
.sidebar-overlay { display: none; }

/* Mobile */
@media (max-width: 768px) {
    .sidebar { transform: translateX(-100%); }
    .sidebar.open { transform: translateX(0); }
    .hamburger { display: flex; align-items: center; justify-content: center; }
    .sidebar-close { display: block; background: none; border: none; color: #aaa; font-size: 24px; cursor: pointer; }
    .sidebar-overlay {
        display: none; position: fixed; top: 0; left: 0; right: 0; bottom: 0;
        background: rgba(0,0,0,.4); z-index: 99;
    }
    .sidebar-overlay.open { display: block; }
    .content-wrapper { margin-left: 0; }
}
```

## JavaScript (chapter-based TOC — ONLY approach for multi-chapter docs)

For multi-chapter documents with `.chapter` cards, the TOC MUST iterate by chapter
rather than collecting all headings flat. A flat list mixes all sections together
with no chapter grouping, making the sidebar useless for navigation.

```javascript
document.addEventListener('DOMContentLoaded', function() {
    var toc = document.getElementById('toc');
    var chapters = document.querySelectorAll('.chapter[id]');
    var items = []; // {el: a, target: element}

    function cleanText(t) {
        return t.textContent.replace(/[◆◇▶→⭐💡]/g,'').replace(/\s+/g,' ').trim();
    }

    chapters.forEach(function(ch) {
        var title = ch.querySelector('.chapter-title');
        if (!title) return;
        var txt = cleanText(title);
        if (txt.length < 2) return;

        // Chapter title as top-level TOC entry
        var a = document.createElement('a');
        a.href = '#' + ch.id;
        a.textContent = txt;
        a.className = 'toc-h2';
        toc.appendChild(a);
        items.push({el: a, target: ch});

        // Sub-sections within this chapter
        var subs = ch.querySelectorAll('.sn, h2, h3, h4');
        var si = 0;
        subs.forEach(function(h) {
            if (h === title || h.closest('.chapter-title')) return;
            if (h.closest('.ans-wrap')) return; // skip hidden answer content
            var stxt = cleanText(h);
            if (stxt.length < 2) return;
            if (!h.id) h.id = ch.id + '-s' + (si++);
            var a2 = document.createElement('a');
            a2.href = '#' + h.id;
            a2.textContent = stxt;
            a2.className = 'toc-h3';
            toc.appendChild(a2);
            items.push({el: a2, target: h});
        });
    });

    // Active section highlight
    function onScroll() {
        var y = window.scrollY + 100;
        var active = null;
        items.forEach(function(item, i) { if (item.target.offsetTop <= y) active = i; });
        items.forEach(function(item, i) { item.el.classList.toggle('active', i === active); });
    }
    window.addEventListener('scroll', onScroll);

    // Mobile hamburger (same as before)
    document.getElementById('hamburger').addEventListener('click', function() {
        document.getElementById('sidebar').classList.add('open');
        document.getElementById('sidebarOverlay').classList.add('open');
    });
    var closeSidebar = function() {
        document.getElementById('sidebar').classList.remove('open');
        document.getElementById('sidebarOverlay').classList.remove('open');
    };
    document.getElementById('sidebarClose').addEventListener('click', closeSidebar);
    document.getElementById('sidebarOverlay').addEventListener('click', closeSidebar);
});
```

## When to Add Sidebar

Add sidebar to any document that has 3+ major sections. Skip it for:
- Single-page landing pages
- Slides/presentations (use html-presentation instead)
- Very short content (< 3 sections)

## Required Heading IDs

Every `<h2>` and `<h3>` inside the content wrapper MUST have an `id` attribute for TOC linking. Auto-generate with `h.id = 'heading-' + i` in JS, but also add manual IDs for important sections when you know the name (e.g., `<h2 id="conditional-expectation">`).
