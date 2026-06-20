# claude-flavored-html —— Claude 风格网页演示生成 Skill

把"复刻那套米白暖色 + 深色双主题 HTML 演示文稿"的能力封装成了一个 Claude Code Skill。
装好后,直接对 Claude 说「用 claude-flavored-html 给『查找』这一章做一套演示」即可。

## 目录结构
```
claude-flavored-html/
├── SKILL.md                  ← skill 主文件(Claude 自动读取)
├── README.md                 ← 本文件
├── reference/
│   └── components.md         ← 组件词汇表 + SVG 约定 + 每页骨架(写内容前读)
└── deck-template/            ← 起点:整个复制过去当新 deck
    ├── index.html            ← 含 7 种版式的空骨架,照它增删
    ├── style.css             ← 组件库(原样复用)
    └── assets/               ← 引擎/令牌/字体/动效/双主题(原样复用)
        ├── base.css  animations.css  fonts.css  runtime.js
        └── themes/  claude-warm.css  claude-dark.css
```

## 安装(二选一)

**A. 全局(所有项目可用,推荐)**
```powershell
Copy-Item ".\claude-flavored-html" "$HOME\.claude\skills\claude-flavored-html" -Recurse -Force
```

**B. 仅某个项目**
```powershell
# 在项目根目录下
New-Item -ItemType Directory -Force ".\.claude\skills" | Out-Null
Copy-Item ".\claude-flavored-html" ".\.claude\skills\claude-flavored-html" -Recurse -Force
```

装好后 `/claude-flavored-html` 应出现在可用 skill 列表里(可能需重启 Claude Code 会话)。

## 用法
- 自然语言触发:「**用 claude-flavored-html 给 X 主题做一套网页演示**」。
- 或显式调用:`/claude-flavored-html` 后接你的章节名、大纲、素材。
- Claude 会:建新文件夹 → 复制 `deck-template/` → 读 `components.md` → 逐页填内容 → 让你浏览器自检。

## 放映
双击生成的 `index.html` 即可。画面是 1920×1080 舞台,会**自动等比缩放**适配你的屏幕(留黑边),换屏不错位。
快捷键:`← →`翻页、`T`换肤、`S`演讲者视图(逐字稿+计时器)、`O`总览、`F`全屏。
排版自检:用 `index.html?audit` 打开,溢出的页会描红并在左上角给出统计。
(网络字体需联网;离线也能用,只是回退到系统字体。)
