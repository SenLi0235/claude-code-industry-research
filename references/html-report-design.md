# HTML 报告视觉规范（Boardroom Print）

> 固定设计系统，subagent 生成 HTML 报告时严格遵守，确保输出风格一致。

## 设计理念

**"可以印出来递进董事会的报告"**——阅读优先、数据清晰、克制专业。避免花哨动效，追求印刷品质感。浅色模式下像一份排版精良的 PDF，深色模式下适合屏幕长时间阅读。

## 色彩系统

```
┌─ 浅色模式 ─────────────────────────────┐
│ 背景        #fafaf8  暖白（护眼）        │
│ 卡片/表底    #ffffff  纯白               │
│ 一级标题     #1e293b  深灰蓝             │
│ 正文         #334155  灰蓝               │
│ 次级文字     #64748b  中灰               │
│ 强调/链接    #0f766e  墨绿（非蓝，区隔于超链接）│
│ 数据高亮     #b45309  琥珀棕（KPI 数字）  │
│ 表头底色     #f1f5f9  浅灰蓝             │
│ 分割线       #e2e8f0  淡灰               │
│ [UNSOURCED]  #fef3c7  淡琥珀底 + #92400e 字│
│ 核心结论框   #f0fdfa  淡墨绿底 + 左边框  │
└────────────────────────────────────────┘

┌─ 深色模式 ─────────────────────────────┐
│ 背景        #0f172a  深蓝灰             │
│ 卡片/表底    #1e293b  中蓝灰            │
│ 正文         #cbd5e1  浅灰蓝            │
│ 标题         #f1f5f9  近白              │
│ 强调         #2dd4bf  亮墨绿            │
│ 数据高亮     #fbbf24  亮琥珀            │
│ [UNSOURCED]  #422006  深琥珀底          │
└────────────────────────────────────────┘
```

## 字体

```css
--font-heading: 'Noto Serif SC', 'Source Han Serif SC', 'STSong', Georgia, serif;
--font-body: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'PingFang SC', 'Microsoft YaHei', sans-serif;
--font-mono: 'JetBrains Mono', 'Cascadia Code', 'SF Mono', Consolas, monospace;
--font-data: 'Inter', -apple-system, sans-serif;  /* 数字用西文字体，tabular-nums */;
```

规则：
- 标题用衬线体，正文用无衬线体，形成"权威 vs 易读"对比
- 数据表格中的数字用 `font-data` + `font-feature-settings: "tnum"` 保证对齐
- 所有字号用 `rem`，基准 16px

## 排版层级

| 层级 | 字号 | 字重 | 用途 |
|---|---|---|---|
| H1（报告标题） | 2.25rem | 700 | 封面标题 |
| H2（章节） | 1.5rem | 600 | 一、二、三... |
| H3（小节） | 1.15rem | 600 | 2.1, 2.2... |
| H4（微节） | 1rem | 600 | 细分节 |
| 正文 | 1rem | 400 | 段落 |
| 小字/脚注 | 0.8rem | 400 | 脚注、来源 |
| 数据大数 | 2rem | 700 | KPI 卡片 |

行高：正文 1.75，标题 1.3，表格 1.5。
段落间距：1.5rem。
最大段落宽度：720px（防过长行）。

## 布局结构

```
┌──────────────────────────────────────────┐
│  Top Bar（固定顶栏）                       │
│  [行业名 行业研究报告]    [浅/深] [打印]   │
├────────┬─────────────────────────────────┤
│        │                                 │
│  TOC   │  Main Content                  │
│  侧边栏 │  封面标题 + 日期 + 口径          │
│  240px  │  核心结论（3-5 条卡片）          │
│        │  正文（7 章）                    │
│  固定   │  附录 A / B / C                │
│  滚动   │  页脚                          │
│  高亮   │                                 │
│        │                                 │
├────────┴─────────────────────────────────┤
│  Footer: 生成时间 + "AI 辅助研究" 声明     │
└──────────────────────────────────────────┘
```

响应式：屏幕 < 1024px 时 TOC 收起到顶部汉堡菜单。打印时 TOC 隐藏。

## 核心组件

### KPI 卡片

用于核心结论区的关键数字：

```html
<div class="kpi-card">
  <span class="kpi-value">216 GW</span>
  <span class="kpi-label">2024 年光伏新增装机</span>
  <span class="kpi-source">[来源: 国家能源局, 2025]</span>
</div>
```

样式：白色卡片、微妙阴影、数值用琥珀棕大号字、底部来源灰色小字。3-5 个卡片横排（移动端堆叠）。

### 数据表格

```html
<table class="data-table">
  <thead><tr><th>指标</th><th>2022</th><th>2023</th><th>2024E</th></tr></thead>
  <tbody>
    <tr><td>市场规模</td><td class="num">1,234</td>...</tr>
  </tbody>
</table>
```

特征：表头 sticky、隔行浅底纹、数字右对齐 tabular-nums、鼠标悬停整行高亮、`[UNSOURCED]` 格用淡琥珀标记。

### 结论框

```html
<blockquote class="finding">
  <p>核心发现：光伏行业已进入 N 型技术替代周期，TOPCon 市占率从 2023 年的 23% 跃升至 2024 年的 65%。</p>
</blockquote>
```

样式：左边框 4px 主题色、淡色背景、与正文区分。

### 引用标记

文中 `[N]` 标记链接到附录 C 对应条目，点击跳转。`[UNSOURCED]` 用虚线底划线 + 琥珀色，hover 显示提示。

### 可视化占位

竞争格局部分的定位图（2×2 矩阵/雷达图等）用内联 SVG 绘制，数据来自报告正文，不依赖外部库。

## 交互

| 功能 | 实现 |
|---|---|
| TOC 高亮当前位置 | IntersectionObserver |
| 深色/浅色切换 | CSS 变量 + localStorage 记忆 |
| 打印 | @media print 样式（隐藏 TOC/顶栏，A4 适配） |
| 引用跳转 | id 锚点 + smooth scroll |
| 回到顶部 | 右下角浮动按钮，滚动超过 300px 出现 |

## 技术约束

- **单个 HTML 文件**：所有 CSS/JS 内联，无外部依赖
- **零框架**：纯 HTML + CSS + 原生 JS
- **必须可打印**：`@media print` 完整适配 A4
- **文件命名**：`<行业名>-行业研究报告.html`
- **编码**：UTF-8，`<meta charset="UTF-8">`

## 生成流程

1. 主 agent 确认 Window C 审完
2. AskUserQuestion："是否生成 HTML 版报告？"
3. 派 subagent，注入 `frontend-design` skill + 本视觉规范
4. subagent: Read 主报告 MD → 按规范生成单 HTML 文件 → Write 落盘
5. 主 agent 告知用户文件路径
