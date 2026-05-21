# 彩色卡片风

来源: docs/任务进度.html (多模态疲劳检测大作业 - 任务进度追踪)
调性: 彩色卡片 + 白色圆角 + 柔和阴影 + 色块分区

## 色板
- 主背景: #f5f7fa (浅灰蓝)
- 卡片: #ffffff (纯白)
- 主要文字: #1a1a2e (深蓝黑)
- 柔和文字: #666666 / #999999
- 各阶段色块:
  - 阶段1 (数据准备): #4CAF50 (绿)
  - 阶段2 (信号预处理): #2196F3 (蓝)
  - 阶段3 (特征提取): #FF9800 (橙)
  - 阶段4 (模型构建): #9C27B0 (紫)
  - 阶段5 (优化): #f44336 (红)
  - 阶段6 (实验): #009688 (青)
  - 阶段7 (交付): #607D8B (灰蓝)
- 标签颜色:
  - 已完成: bg=#e3f2fd, text=#1565c0 (蓝)
  - 待开始: bg=#e8f5e9, text=#2e7d32 (绿)
  - 创新点: bg=#f3e5f5, text=#7b1fa2 (紫)
  - 改进: bg=#e0f2f1, text=#00695c (青)
  - 进阶: bg=#fff3e0, text=#e65100 (橙)
  - 待开始: bg=#f5f5f5, text=#616161 (灰)

## 字体
- 通用: -apple-system, 'Segoe UI', 'Microsoft YaHei', sans-serif
- 标题字号: 26px (h1), 18px (阶段标题)
- 正文字号: 14px
- 标签字号: 11-12px
- 统计数字: 28px bold

## 布局
- max-width: 900px, margin: 0 auto 居中
- 卡片式分段: .phase { background: white; border-radius: 12px; box-shadow: 0 2px 8px rgba(0,0,0,0.08) }
- 每个卡片顶部有 4px 高的渐变彩色条 (.color-bar)
- 卡片左侧有带颜色的圆形编号 (28px, 50% radius)
- 底部有进度条 (.progress-bar)
- 顶部有统计卡片行 (.stat-box)

## 交互
- 可折叠面板: togglePhase(), 点击切换 ▼/▶
- 勾选框: 自动更新进度统计 (updateStats)
- 已完成任务自动删除线样式

## 关键 CSS 模式
```css
.phase { background: white; border-radius: 12px; margin-bottom: 20px; box-shadow: 0 2px 8px rgba(0,0,0,0.08); }
.phase-header { padding: 16px 24px; font-size: 18px; font-weight: 600; cursor: pointer; display: flex; justify-content: space-between; }
.phase-num { width: 28px; height: 28px; border-radius: 50%; color: white; text-align: center; line-height: 28px; margin-right: 12px; }
.tag { font-size: 11px; padding: 2px 8px; border-radius: 10px; }
.stat-box { background: white; padding: 16px 24px; border-radius: 10px; box-shadow: 0 2px 8px rgba(0,0,0,0.08); text-align: center; }
.stat-box .num { font-size: 28px; font-weight: 700; }
.color-bar { height: 4px; border-radius: 2px; margin: 0 24px 12px; }
.progress-bar { height: 6px; background: #e0e0e0; border-radius: 3px; }
.progress-fill { height: 100%; background: linear-gradient(90deg, #4CAF50, #8BC34A); border-radius: 3px; transition: width 0.5s; }
```
