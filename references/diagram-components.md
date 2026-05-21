# Diagram Component Reference

## Iron Rule: Zero ASCII Art

ASCII box-drawing chars (`┌┐└┘├┤─│╲╱═║██`) never align across fonts. Every
diagram must use HTML/CSS or inline SVG.

## Component Catalog

| Component | CSS class | Use case | Implementation |
|-----------|-----------|----------|---------------|
| Flow arrows | `.flow-diagram` | Preprocessing pipeline, step chain | flex + span nodes + arrows |
| Step flow | `.steps-flow` | GA evolution, algorithm flow | flex + step-item + arrow |
| Bar chart | `.barchart` | Power spectrum, SHAP, accuracy | div + width% |
| Architecture | inline SVG | Fusion strategy, model arch | SVG rect + line + text |
| Scatter | inline SVG | Poincaré plot, feature distribution | SVG circle + ellipse + axis |
| Sequence | inline SVG | Timeline, pipeline | SVG rect + line + arrow |
| Tree | `.tree-flow` or SVG | RF decision tree, hierarchy | flex + tree-node or SVG |
| Feature cube | `.diag-cube` | 3D concept display | flex column + cube-layer |
| Compare split | `.compare-split` | Before/after, awake vs fatigue | flex side-by-side |
| Info card | `.info-card` | Key concept, definition, formula | Rounded card + icon |
| Matrix table | `.matrix-table` | Feature matrix | Standard `<table>` |
| Signal waveform | inline SVG | EEG/ECG signal | SVG `<path>` |
| Confusion matrix | CSS grid | TP/TN/FP/FN | CSS grid + color blocks |
| Pipeline | inline SVG | Optimization pipeline | SVG rect + line + arrow |

## Styling

All diagram components share:
- Background: white
- Border: 1px solid #e2e8f0
- Border-radius: 8-12px
- Padding: 16-24px
- Box-shadow: 0 1px 3px rgba(0,0,0,.06)

## SVG Examples

### Architecture diagram (multi-branch)
```html
<svg viewBox="0 0 620 240" style="max-width:620px;">
  <rect x="10" y="10" width="80" height="30" rx="6" fill="#dbeafe" stroke="#3b82f6"/>
  <text x="50" y="30" fill="#1e40af" text-anchor="middle" font-size="12">输入A</text>
  <line x1="90" y1="25" x2="130" y2="25" stroke="#94a3b8" stroke-width="1.5"/>
  <polygon points="130,20 140,25 130,30" fill="#94a3b8"/>
</svg>
```

### Scatter plot
```html
<svg viewBox="0 0 300 180" style="max-width:300px;">
  <line x1="40" y1="140" x2="280" y2="140" stroke="#94a3b8" stroke-width="1.5"/>
  <line x1="40" y1="140" x2="40" y2="20" stroke="#94a3b8" stroke-width="1.5"/>
  <circle cx="120" cy="90" r="3" fill="#3b82f6" opacity="0.6"/>
  <text x="150" y="170" font-size="11" fill="#64748b" text-anchor="middle">X轴</text>
</svg>
```

### Sequence/timeline
```html
<svg viewBox="0 0 400 80" style="max-width:400px;">
  <rect x="10" y="10" width="70" height="30" rx="6" fill="#dbeafe" stroke="#3b82f6"/>
  <text x="45" y="30" font-size="11" fill="#1e40af" text-anchor="middle">步骤1</text>
  <line x1="80" y1="25" x2="120" y2="25" stroke="#94a3b8"/>
  <polygon points="120,20 130,25 120,30" fill="#94a3b8"/>
  <rect x="130" y="10" width="70" height="30" rx="6" fill="#fef3c7" stroke="#f59e0b"/>
  <text x="165" y="30" font-size="11" fill="#b45309" text-anchor="middle">步骤2</text>
</svg>
```

## Code Block Text

If placing descriptive text inside a code block (algorithm steps, param docs):
- Use `<span>` not `<div class="diagram-line">`
- Color: `#cbd5e1` or brighter against dark bg (`#1a1b2e`)
- Replace `├── └──` with `-` `*` standard chars
