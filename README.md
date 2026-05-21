# html-design-workflow

用中文一句话，让 Claude 帮你生成高质量 HTML 页面、技术文档、幻灯片或落地页。

## 功能

- **分类路由**：技术文档 → 左侧导航 + 内容区；幻灯片 → 全屏翻页；落地页 → 品牌聚焦
- **风格学习**：给 Claude 看一个 HTML 文件，它能提取色调/字体/布局并复用到下一个需求
- **零 ASCII 图**：所有流程图/图表用 HTML/CSS/SVG 渲染，禁止 `┌┐└┘├┤─│` 等字符
- **规范交付**：内置交付清单

## 目录结构

```
├── SKILL.md                          # 主 skill 定义
├── references/diagram-components.md  # 图组件详细参考
└── styleref/                         # 已学习的风格记录
```

## 触发条件

当你说「做 HTML / 做网页 / 做文档 / 做手册 / 做幻灯片 / 做 PPT / 做页面 / 做组件 / 前端设计 / UI 设计 / 学习这个风格 / 参考这个页面 / 提取风格」时自动触发。

## 依赖

- `frontend-design` skill（设计美学）
- `html-presentation` skill（幻灯片引擎）
- `theme-factory` skill（配色主题）
