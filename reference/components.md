# 组件词汇表 · SVG 约定 · 每页骨架

写任何一页内容前读这份。**排版只能用下列 class,不要自造样式。** 一切颜色走 `var(--…)` 令牌。

---

## 一、每页骨架

### 内容页(最常用)
```html
<section class="slide" data-title="二叉排序树">
  <div class="slide-fx"></div>
  <div class="slide-inner">
    <div class="eyebrow2" data-anim="fade-up">3 · 查找</div>   <!-- 序号·小节名,序号随小节递增 -->
    <h2 class="s-title" data-anim="fade-up">本页大标题</h2>     <!-- 标题长可加 class="s-title sm" -->
    <div class="rule"></div>                                   <!-- 渐变小横线 -->

    <div class="wrap-2" style="gap:30px">                      <!-- 布局网格 -->
      <div class="stack14"> …左栏… </div>
      <div class="stack14"> …右栏… </div>
    </div>
  </div>
  <aside class="notes"><p>逐字稿:用 <strong>重点</strong> 与 <em>术语</em>。</p></aside>
</section>
```

### 封面(第一页,加 is-active)
```html
<section class="slide is-active" data-title="封面">
  <div class="slide-fx"></div>
  <div class="cover">
    <span class="badge" data-anim="fade-down">● 副标题 / 课程线</span>
    <h1 data-anim="fade-up">主标题 <span class="g">渐变词</span></h1>
    <p class="sub" data-anim="fade-up">一两句副说明。<br>可换行。</p>
    <div class="chips" style="margin-top:30px" data-anim="fade-up">
      <span class="chip"><span class="d"></span>关键词</span>
      <span class="chip">关键词</span>
    </div>
    <p class="mut3" style="margin-top:34px;font-size:14px">按 <span class="kbd">T</span> 换肤 · <span class="kbd">S</span> 演讲者视图 · <span class="kbd">←</span> <span class="kbd">→</span> 翻页</p>
  </div>
  <aside class="notes"><p>开场逐字稿。</p></aside>
</section>
```

### 章节分隔页
```html
<section class="slide" data-title="第二部分 · 散列">
  <div class="slide-fx"></div>
  <div class="divider2">
    <div class="pn" data-anim="rise-in">II</div>      <!-- 巨大罗马数字 -->
    <h2 data-anim="fade-up">散 列 表</h2>
    <p class="sub" data-anim="fade-up">用关键字直接算出存储位置</p>
    <div class="chips" style="justify-content:center;margin-top:24px" data-anim="fade-up">
      <span class="chip">散列函数</span><span class="chip">冲突处理</span><span class="chip">查找效率</span>
    </div>
  </div>
  <aside class="notes"><p>过渡逐字稿。</p></aside>
</section>
```

### 结尾页
```html
<section class="slide" data-title="结尾">
  <div class="slide-fx"></div>
  <div class="cover tcenter" style="max-width:900px;text-align:center;margin:0 auto">
    <span class="badge" data-anim="fade-down" style="margin:0 auto">● 课程线 · 一轮复习</span>
    <h1 data-anim="fade-up" style="font-size:76px">一句收束的<span class="g">金句</span>。</h1>
    <p class="sub" data-anim="fade-up" style="margin-left:auto;margin-right:auto">两句总结主线。</p>
  </div>
  <aside class="notes"><p>收尾逐字稿。</p></aside>
</section>
```

> 整份文件最底部、`</div>`(关闭 .deck)之前,放一次页脚:
> ```html
> <div class="deck-foot"><span>课程 · 章节名</span><span class="slide-number" data-current="1" data-total="N"></span></div>
> ```

---

## 二、布局网格(外层容器)

| class | 含义 |
|---|---|
| `wrap-2` | 等宽两栏 |
| `wrap-12` | 1.1 : 0.9 两栏(图文搭配,图略大) |
| `wrap-21` | 1 : 1 两栏(垂直居中对齐) |
| `wrap-3` | 三栏 |
| `stack8` / `stack14` / `stack20` | 子元素之间留 8/14/20px 竖直间距 |
| `tcenter` | 文本居中 |

---

## 三、内容组件

### 卡片
```html
<div class="cardx card-top">       <!-- card-top=顶部强调线;flat=无阴影灰底 -->
  <h4>小标题</h4>
  <p>正文。</p>
</div>
```

### 强调框 callout(最常用的"重点框")
```html
<div class="callout key" data-anim="fade-up"><span class="lab">标签</span>正文,可用 <b>加粗</b>。</div>
```
变体:无修饰=蓝紫(中性)、`key`=绿(重点结论)、`warn`=红(易错)、`tip`=橙(提示)。`<span class="lab">` 是小标签,必放第一个。

### 胶囊 chip
```html
<div class="chips">
  <span class="chip on">高亮态</span>
  <span class="chip">普通</span>
  <span class="chip"><span class="d"></span>带圆点</span>
</div>
```

### 列表
```html
<ul class="bul">     <!-- 菱形要点;加 sm 变小 -->
  <li>要点,可 <b>加粗</b></li>
</ul>
<ol class="num">     <!-- 渐变圆圈编号 -->
  <li>第一步</li>
</ol>
```

### 数据表
```html
<table class="tbl sm center" data-anim="fade-up">  <!-- sm=小字 center=居中 -->
  <thead><tr><th>列</th><th>列</th></tr></thead>
  <tbody>
    <tr><td class="hi">高亮格</td><td class="mut">弱化格</td></tr>
  </tbody>
</table>
```

### 算法迭代表(手算过程,Dijkstra/Prim/拓扑等)
```html
<table class="mtx" data-anim="fade-up" style="font-size:13.5px">
  <thead><tr><th>步骤</th><th>B</th><th>C</th><th>并入</th></tr></thead>
  <tbody>
    <tr><th>S={A}</th><td class="pick">3</td><td>∞</td><td>C</td></tr>  <!-- pick=本轮选中 -->
    <tr><th>并入 C</th><td class="done">—</td><td class="upd">4</td><td>B</td></tr> <!-- done=已处理 upd=被更新 -->
  </tbody>
</table>
```
单元格状态:`pick`(选中,橙底)、`upd`(更新,绿底)、`done`(已处理,灰)。

### 行内元素
| 写法 | 效果 |
|---|---|
| `<span class="k">O(n)</span>` | 行内代码/记号(橙底圆角) |
| `<div class="fml">f(x)=<b>x²</b></div>` | 等宽公式块 |
| `<span class="acc">文字</span>` | 强调色文字 |
| `<span class="kbd">T</span>` | 键帽 |

### 大数字统计
```html
<div class="stat"><span class="n">99%</span><span class="l">说明文字</span></div>
```

### 代码块(语法高亮靠手动套 span)
```html
<pre class="codeblock sm"><span class="c-kw">void</span> <span class="c-fn">dfs</span>(int v){  <span class="c-cm">// 注释</span>
  visited[v] = <span class="c-nu">1</span>;
  printf(<span class="c-st">"%d"</span>, v);
}</pre>
```
着色 class:`c-kw`关键字、`c-cm`注释、`c-st`字符串、`c-fn`函数名、`c-nu`数字。`sm`=小字。

---

## 四、SVG 图(树 / 查找树 / 图 / 散列冲突链,全部自绘)

```html
<svg class="dg" viewBox="0 0 300 175" width="320" data-anim="fade-up">
  <!-- 边:先画,压在结点下 -->
  <line class="dg-edge" x1="150" y1="28" x2="80" y2="95"/>     <!-- hl=高亮 dim=虚线 -->
  <!-- 结点 -->
  <circle class="dg-node hl" cx="150" cy="28" r="19"/>          <!-- hl实心强调 alt次色 gh幽灵态 -->
  <text class="dg-t on" x="150" y="28">A</text>                 <!-- on=白字(配实心结点) -->
  <circle class="dg-node" cx="80" cy="95" r="18"/>
  <text class="dg-t" x="80" y="95">B</text>
  <!-- 边权(可选) -->
  <text class="dg-w box" x="115" y="60">5</text>                <!-- box=带底色描边,压线上更清晰 -->
</svg>
<div class="dg-cap" style="font-size:13px">图下说明文字</div>
```

约定:
- **先画 `line`(边),后画 `circle`+`text`(结点)**,否则边会盖住结点。
- 坐标随意定,`viewBox` 会自适应缩放;`width` 控制实际显示宽度。
- 结点 class:`dg-node`(空心)、`.hl`(实心强调色)、`.alt`(次强调色)、`.gh`(幽灵/未访问)。
- 结点文字配实心点时加 `.on` 变白字。
- 边 class:`dg-edge`、`.hl`(加粗高亮)、`.dim`(虚线表示舍弃/备选)。
- 文字基类:`dg-t`(结点名)、`dg-w`(边权)、`dg-lbl`(标签)、`dg-cap`(图注)。

---

## 五、动效

| 用法 | 效果 |
|---|---|
| `data-anim="fade-up"` | **默认全用这个**:向上淡入。给每个 eyebrow2/标题/卡片/表格/图加 |
| `data-anim="fade-down"` | 向下淡入(封面 badge) |
| `data-anim="rise-in"` | 模糊上浮(分隔页大数字) |
| `class="anim-stagger-list"` | 容器加它,子项逐条错峰浮现 |

切到某页时引擎自动重放该页所有 `data-anim`。**克制是关键**:全场基本只用 `fade-up`,统一感就来自这里。动效库里还有 20+ 种(zoom-pop/blur-in/path-draw/counter-up…),但非必要不用。

---

## 六、令牌速查(改配色只动 `themes/*.css`,不要硬编码)

| 令牌 | 含义 |
|---|---|
| `--accent` / `--accent-2` / `--accent-3` | 主/次/第三强调色 |
| `--text-1/2/3` | 主/次/弱文字 |
| `--surface` / `--surface-2` | 卡片面 / 次级面 |
| `--border` / `--border-strong` | 边框 |
| `--good` / `--warn` / `--bad` | 绿 / 黄 / 红语义色 |
| `--grad` | 主渐变(标题渐变字、num 序号) |
| `--radius` / `--radius-sm` | 圆角 |
| `--font-display` | 标题字体(暖色=Fraunces 衬线) |

渐变文字写法:`<span class="g">`(封面/结尾 h1 内)或 `class="gradient-text"`。
