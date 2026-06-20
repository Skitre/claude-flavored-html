# BUG · `center` 工具类破坏居中表格（`<table class="tbl ... center">` 表头错位）

> 一句话：`base.css` 的 `.center{display:flex}` 工具类与 `style.css` 文档里推荐的 `tbl ... center` 居中表格写法**撞名**，把 `<table>` 变成了 flex 容器，导致 `thead`/`tbody` 塌成左右并排，表头与数据列对不齐。

- **严重程度**：中（视觉错位，不影响数据；但极易被忽视，且 `?audit` 抓不到）
- **影响范围**：所有写 `class="tbl ... center"` 或 `class="mtx ... center"` 的表格
- **发现来源**：用本 skill 生成的「考研408 计组 · 计算机系统概述」37 页 deck，用户反馈第 4 页表头错位

---

## 1. 症状

带 `center` 的表格，表头行（thead）整体偏到表格左侧、数据行（tbody）偏到右侧，二者列不再上下对齐。**没有溢出**，所以 `?audit` 不会报警。

## 2. 根因（CSS 撞名 + 特异性）

两条规则同时命中同一个 `<table>` 元素：

**`assets/base.css`（约第 91 行）—— 通用居中工具类**
```css
.center{display:flex;align-items:center;justify-content:center;text-align:center}
```

**`style.css`（约第 83 行）—— 表格组件类（未显式声明 display）**
```css
.tbl{width:100%;border-collapse:collapse;font-size:15.5px;background:var(--surface);
  border:1px solid var(--border);border-radius:var(--radius-sm);overflow:hidden}
```

对一个 `<table class="tbl sm center">`：
- `.tbl`（特异性 `0,1,0`）没设 `display`；
- `.center`（特异性 `0,1,0`）设了 `display:flex`；
- 二者特异性相同，**按源码顺序后者胜** —— 但这里 `.center` 设了值而 `.tbl` 没设，所以表格最终 `display:flex`。

表格一旦变成 flex 容器，`thead`/`tbody` 的 `display:table-header-group` / `table-row-group` 失去表格上下文，**计算值降级为 `block`**，于是 thead 与 tbody 变成两个并排的 flex 子项 → 表头错位。

> 注：`style.css` 里的 `.tbl.center th,.tbl.center td{text-align:center}`（约第 90 行）只作用于单元格、本身没问题；坏的是**表格元素自身被 flex 化**。

## 3. 为什么 `?audit` 没抓到

`runtime.js` 的 `runAudit()` 只比较 `inner.scrollHeight/scrollWidth` 是否超出安全区。错位的表格仍然在边界内，所以它报告「✓ 全部不溢出」。**这类 display 腐败不在 audit 检测范围内**，需要额外校验。

## 4. 复现

最小复现（套用 `components.md` 自己的示例写法即可）：
```html
<section class="slide" data-title="repro">
  <div class="slide-fx"></div>
  <div class="slide-inner">
    <table class="tbl sm center">
      <thead><tr><th>A</th><th>B</th><th>C</th></tr></thead>
      <tbody>
        <tr><td>1</td><td>2</td><td>3</td></tr>
        <tr><td>4</td><td>5</td><td>6</td></tr>
      </tbody>
    </table>
  </div>
  <aside class="notes"><p>repro</p></aside>
</section>
```

## 5. 证据（headless Chrome 实测）

对上述 deck 用 `getComputedStyle` + `getBoundingClientRect` 逐表测量：

```
slide4  [1.1 四代划分]      table.display=flex  thead=block  tbody=block  H0left=517 B0left=889  <<< MISALIGNED
slide30 [计算专题 例1+例2]   table.display=flex  thead=block  tbody=block  H0left=541 B0left=700  <<< MISALIGNED
slide32 [6 计算机的分类]     table.display=flex  thead=block  tbody=block  H0left=415 B0left=642  <<< MISALIGNED
slide9  [2.3 翻译与解释]     table.display=table thead=table-header-group tbody=table-row-group H0left=414 B0left=414  (正常, 用的是 tbl sm 无 center)
```

可见：凡是带 `center` 的表 → `display=flex` + thead/tbody=block + 表头/数据 left 坐标不一致；不带 `center` 的表 → 一切正常。

## 6. 推荐修复（首选，改 `style.css`）

给表格组件类**显式锁定 `display:table`**。由于本 skill 硬约束规定 `<head>` 加载顺序为 `fonts → base → animations → theme-link → style.css`（`style.css` 永远在 `base.css` 之后），同特异性下 `style.css` 必胜，最稳。

**`style.css` —— 在 `.tbl` 规则里加 `display:table`：**
```diff
- .tbl{width:100%;border-collapse:collapse;font-size:15.5px;background:var(--surface);
+ .tbl{display:table;width:100%;border-collapse:collapse;font-size:15.5px;background:var(--surface);
    border:1px solid var(--border);border-radius:var(--radius-sm);overflow:hidden}
```

**同理给 `.mtx` 也补上（防 `mtx ... center` 同样翻车）：**
```diff
- .mtx{border-collapse:collapse;font-family:var(--font-mono);font-size:15px;background:var(--surface);
+ .mtx{display:table;border-collapse:collapse;font-family:var(--font-mono);font-size:15px;background:var(--surface);
    border:1px solid var(--border);border-radius:var(--radius-sm);overflow:hidden}
```

这样：带不带 `center` 的表格都恒为 `display:table`，`.center` 的 `text-align:center` 仍正常继承，`.tbl.center th/td{text-align:center}` 居中不受影响，其它元素用 `.center` 做 flex 居中也完全不受影响（`.tbl`/`.mtx` 只匹配表格）。

### 备选方案（任选其一，效果等价）

- **B. 显式覆盖居中表**（更精准，不依赖加载顺序）：
  ```css
  .tbl.center,.mtx.center{display:table}
  ```
  特异性 `0,2,0` > `.center` 的 `0,1,0`，稳。
- **C. 在 `base.css` 排除表格**（治本于工具类一侧）：
  ```diff
  - .center{display:flex;align-items:center;justify-content:center;text-align:center}
  + .center:not(table){display:flex;align-items:center;justify-content:center;text-align:center}
  ```
  让 `.center` 永不作用于 `<table>`。代价：`:not(table)` 略隐晦。
- **D. 改文档词汇**：把 `components.md` 里 `tbl sm center` 换成不撞名的类（如 `tbl sm tcenter`，并新增 `.tbl.tcenter th,.tbl.tcenter td{text-align:center}`）。改动面大，不推荐作为唯一手段。

> 个人推荐 **A**（最小、最稳、与现有文档完全兼容）。若想根治「工具类不应作用到表格」的语义，可同时叠加 **C**。

## 7. 建议同步处理

1. **`reference/components.md`（约第 131 行）**：示例 `<table class="tbl sm center" ...>` 现在会被本 bug 影响——修复 `style.css` 后该示例即可正常；如采用方案 D 则需改示例。
2. **`SKILL.md` 自检清单**：可加一条「带 `center` 的表格需保证 `display:table`」。
3. **`runtime.js` 的 `runAudit()`**（可选增强）：除溢出外，顺便断言 `getComputedStyle(table).display === 'table'`，能在 audit 阶段就暴露此类 display 腐败。

## 8. 验证方法

修复后用 headless Chrome 跑一遍（Node 驱动 Chrome `--dump-dom`，注入下述片段，读取 `.deck > .slide` 里每张表的 `getComputedStyle(table).display` 与 thead 首格/数据首格的 `getBoundingClientRect().left`），要求全部 `display=table` 且两 left 相等：

```js
document.querySelectorAll('.deck > .slide').forEach((s,i)=>{
  s.querySelectorAll('table').forEach((t,j)=>{
    const d = getComputedStyle(t).display;
    const th = t.querySelector('thead'), tb = t.querySelector('tbody');
    const hl = th?.rows[0]?.cells[0]?.getBoundingClientRect().left;
    const bl = tb?.rows[0]?.cells[0]?.getBoundingClientRect().left;
    console.log(`slide${i+1}-t${j} display=${d} H0=${Math.round(hl)} B0=${Math.round(bl)} ${hl!==bl?'MISALIGNED':''}`);
  });
});
```

期望：所有行 `display=table`，无 `MISALIGNED`。
