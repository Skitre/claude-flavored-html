---
name: claude-flavored-html
description: 制作与 Claude / Anthropic 风格一致的 HTML 网页演示文稿(web deck / 网页 PPT)。当用户想做一套新的 HTML 幻灯片、复刻现有那套米白暖色 + 深色双主题 slides 的风格·排版·动效、或为某个章节/主题(如 408 数据结构、考研、技术分享)生成网页演示时使用。自带完整可复用的 CSS/JS 引擎、组件库与骨架模板,无需从零搭建。
---

# claude-flavored-html · Claude 风格网页演示生成器

一套纯 **HTML / CSS / JS** 的演示框架(不是 PowerPoint)。特点:
- **双主题**,按 `T` 切换:`claude-warm`(米白纸感 + 陶土橙 + Fraunces 衬线)、`claude-dark`(曜石黑 + 紫蓝绿渐变)。
- **设计令牌驱动**:颜色/圆角/阴影/字体全是 CSS 变量,换肤自动适配。
- **零依赖引擎** `runtime.js` 自动接管:`←/→/空格`翻页、`#/N`定位、`T`换肤、`S`演讲者视图(逐字稿+计时器)、`O`总览、`F`全屏、进度条、**每次翻到某页自动重放该页入场动画**。
- 内置组件:渐变标题、圆角卡片、左边框 callout、胶囊 chip、数据表、算法迭代表、自绘 SVG 树/图、代码块。

## 何时使用
- 用户要"做一个 PPT / 演示 / slides / 讲稿",且接受**网页形式**。
- 用户要"复刻 / 沿用 / 套用这种风格"为**新章节或新主题**做演示。
- 用户给一份大纲或讲义,想要成品幻灯片。

## 资产清单(全部在本 skill 目录内)
- **`deck-template/`** —— 起点,**整个复制过去**当新 deck:
  - `assets/`(`base.css` 令牌+幻灯片系统、`animations.css` 动效库、`fonts.css` 网络字体、`runtime.js` 引擎、`themes/*.css` 两套主题)—— **原样复用,一字不改**。
  - `style.css` —— 组件库 —— **原样复用,一字不改**。
  - `index.html` —— 可直接打开的**骨架模板**,含封面/内容页/分隔页/迭代表页/背诵页/结尾共 7 张示例页,照它增删即可。
- **`reference/components.md`** —— 完整组件词汇表 + SVG 绘制约定 + 每页骨架。**开始写内容前必读**。

## 工作流程
1. **确认输入**:章节名、知识点大纲、素材(重点/公式/常考点/易错点)。缺则先问。
2. **建目录并复制基础设施**:在目标位置新建文件夹,把 `deck-template/` 整个内容复制过去。只改 `index.html` 的 `<title>` 和封面文字。
3. **读规范**:动手前先读 `reference/components.md` 与复制过来的 `style.css`、`assets/base.css`、`assets/themes/claude-warm.css`——把组件词汇和令牌内化,这是唯一风格基准。
4. **逐页写内容**:删掉模板里的示例 `<section>`,按 components.md 的"每页骨架"和"组件词汇",一页一个 `<section class="slide">`,把素材填进去。
5. **浏览器自检**:翻页正常、按 `T` 两套配色都好看、按 `O` 总览、每页内容**一屏内不溢出不滚动**。

## 不可违背的硬约束
1. 文档头照抄模板:`<html ... data-themes="claude-warm,claude-dark" data-theme="claude-warm">`;`<head>` 内按 **fonts → base → animations → theme-link(`id="theme-link"`) → style.css** 顺序引入;`<body class="tpl">`;外层 `<div class="deck">`。
2. 每页 = 一个 `<section class="slide" data-title="该页标题">`;**第一页**额外加 `is-active`。
3. 每页第一个子元素必须是 `<div class="slide-fx"></div>`(背景光晕)。
4. 内容页正文包在 `<div class="slide-inner">`;封面用 `<div class="cover">`、分隔页用 `<div class="divider2">`(这两类**不**套 slide-inner)。
5. 每页结尾必须有 `<aside class="notes"><p>逐字稿</p></aside>`;讲稿内 `<strong>` 标重点、`<em>` 标术语(演讲者视图会分别上色)。
6. `<script src="assets/runtime.js"></script>` 放在 `</body>` 前。**不要写任何 JS**——翻页/换肤/动画重放全由引擎接管。
7. 颜色/圆角/阴影/字体**只用 `var(--…)` 令牌**,禁止硬编码色值(个别强调可 `color:var(--accent)`)。
8. 设计分辨率 **1920×1080(16:9)**;留白充足,确保不溢出。

## 动效纪律(动态感来自克制)
- 默认全场只用 `data-anim="fade-up"`:给 `eyebrow2`、`s-title`、每个卡片/表格/callout/图逐个加上,按文档顺序自然形成层次。
- 列表逐条浮现:容器加 `class="anim-stagger-list"`。
- 分隔页大数字可用 `data-anim="rise-in"`。
- **不要**用 neon / glitch / confetti / marquee 等花哨动效——原作的高级感正来自"只用一种"。
- 调试时按 `A` 可在当前页循环预览所有动效。

## 自检清单(交付前逐项确认)
- [ ] 每页都有 `slide-fx`、`data-title`、`aside.notes`。
- [ ] 第一页有 `is-active`;`runtime.js` 在 `</body>` 前。
- [ ] 无硬编码颜色;按 `T` 暖/深两套都正常。
- [ ] 每页一屏内不溢出;`O` 总览缩略图排版正常。
- [ ] `assets/` 与 `style.css` 未被改动。
