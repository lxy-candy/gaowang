---
name: js-visual-demo
description: Create interactive JS concept visualization pages for this HTML/CSS course. Use when the user asks to visualize or demo JS methods, operators, control flow, or language features as an HTML page with live DOM output. Do not use for Gitee-only pages or for pure documentation without interactive elements.
---

# JS Visual Demo Page Generator

为《高级网页设计》课程创建**可交互的 JavaScript 可视化演示页面**。每张方法卡 = 代码高亮 + 参数输入 + ▶ 运行按钮 + 实时 DOM 输出 + 原状态 → 新状态对比。

## 何时触发

用户说"做一个可视化 / 演示 / 形象展示 / 写一个 xx 页"，且涉及 JS 的**方法、操作符、控制流、内置对象**（数组/字符串/对象/Date/Number/Math/迭代器/Promise/模块化等）。

## 自动编号（必须）

扫描 `index.html` 里最后一张 `work-card` 的 `card-index` 数字部分，加 1 作为新文件的课次编号。若目录中已有同课次编号文件（如 `15-2.html` 已存在），则沿用。

示例：最后一张卡片是 `26 / JS ARRAY` → 新文件命名为 `16-1.html`。

## 硬性约定（来自 AGENTS.md）

1. **CSS 类名 / id / JS 变量**：一律以 `Lxy_` 开头
2. **`<head>` 注释块**（每页必须有）：
   ```html
   <!--
   姓名：刘馨月
   日期：YYYY-MM-DD
   功能：页面功能简述（如"JS 数组方法可视化"）
   -->
   ```
3. **纯原生 HTML / CSS / JS**，禁止任何 CDN 引用（包括 font-awesome、Lucide、Bootstrap、Vue/React）
4. **提示词留痕**：生成页面前，先把用户提示词**追加**到 `lxy_prompt.md`，格式：
   ```
   ## yyyy-mm-dd-hh-mm
   - 模型：opencode/mimo-v2.6-flash-free
   - 提示词：原文
   ```
   PowerShell 必须用 `Add-Content -Encoding UTF8`（默认 ANSI 会乱码）
5. **图标**：只用 `font/` 下的 iconfont（本项目暂未用图标，可忽略）

## 模板骨架（建议）

```
┌──────────────────────────────────────────┐
│  HEADER：渐变标题 + 课程名 + 刘馨月标签  │
├──────────────────────────────────────────┤
│  【关键】顶部可视化区                     │
│  · 数组 → 彩色方块条（push/pop 动画）     │
│  · 字符串 → 字符级方格（slice 黄高亮）     │
│  · 对象 → 键值卡（增删改同步）            │
│  · 函数 → 输入框 + 输出对比               │
├──────────────────────────────────────────┤
│  分组标题（按方法族分三组）                │
│  e.g. 🔴 Mutating / 🔵 Non-Mutating 🟡 迭代│
├──────────────────────────────────────────┤
│  方法卡网格（auto-fit minmax 330px）     │
│  ┌────────────────────────────────┐      │
│  │ 方法名  tag                     │      │
│  │ 一句话说明                      │      │
│  │ <pre class="Lxy_code">高亮代码</pre>│   │
│  │ 参数输入行                       │      │
│  │ ▶ 运行                          │      │
│  │ 对比输出（原→新 / 返回值）        │      │
│  └────────────────────────────────┘      │
└──────────────────────────────────────────┘
```

### 关键设计点

- **顶部可视化区**必须**双向绑定**到各方法卡：Mutating 类方法运行后立即在可视化区体现（数组条弹出一个新方块、对象卡多一行、字符串 slice 黄色高亮）
- **Non-mutating** 方法在结果框要明确标注"原数组未改"，快照演示新数组
- **对比输出框**三段式：
  - `原 state`（粉色 / 灰色）
  - `返回值`（黄色 — 注意数组方法几乎都有返回值）
  - `新 state`（青色 / 绿色）
- **可折叠参数**：有参数的方法给输入框（push 加什么、filter 阈值多少、slice start/end），没有就不给
- **一个状态源**：JS 里只维护一份 `Lxy_state` 对象，所有方法卡运行后都调 `Lxy_render()` 重绘顶部可视化区，不能每张卡自己偷偷维护一份副本

## 收录 index.html（生成后立刻同步）

1. `hero` 区 `span` 文案中的页面总数（当前为 `27`）+1
2. 在 `works .work-grid` 的最后一张卡片**后面**追加新卡片：
   ```html
   <article class="work-card">
       <div class="card-index">N / SHORT-TAG</div>
       <h3>完整标题</h3>
       <p>一句话描述页面里有什么（覆盖多少个方法 / 什么可视化手段）</p>
       <a class="card-link" href="NN-N.html">进入页面 <span aria-hidden="true">↗</span></a>
   </article>
   ```
   - `N` = 卡片序号 = 扫描当前卡片总数 + 1
   - `SHORT-TAG` = 3~5 个大写字母组成的标签（如 `JS ARR` / `JS STR` / `JS OBJ` / `JS PROM`）
   - 文件名中文和空格要 URL 编码（空格 → `%20`）

## commit + push（可选但推荐）

页面生成 + index 收录完成后，调用项目内的 **`git-commit-push-github`** skill（`.trae/skills/git-commit-push-github/SKILL.md`），把新页面 + index 修改 + lxy_prompt.md 留痕一起 commit 并 push 到 `github` remote。该 skill 内置了本项目的 443 端口绕过逻辑，**直接调用即可**，不要重复配置 SSH。

## 分组建议（按方法类别）

| 类型 | 典型分组 |
|---|---|
| 数组 | 🔴 Mutating / 🔵 Non-Mutating / 🟡 Iteration |
| 字符串 | 查找 / 截取 / 大小写 / 填充 / 替换 |
| 对象 | 静态方法 / 查询 / 修改 / 合并 |
| 函数 | 参数 / 返回值 / this / 闭包 / 箭头函数 |
| 异步 | Promise / async-await / 事件循环可视化（可能需要动画） |

## 反模式（禁止）

- 把所有方法堆一张大卡，不可交互（违反"可视化"要求）
- 把 `Lxy_state` 写死成不可变，Non-mutating 方法演示后不恢复原状态
- 忘记同步 index.html 或忘记改页面总数
- 用 CDN 引入 prism.js 或 highlight.js 做代码高亮（本项目纯原生，用手写 `.Lxy_code .kw` / `.fn` / `.str` / `.cm` 类）
- 生成完不 commit（会留在工作区变成"未提交改动"，下次 push 会混进去）
