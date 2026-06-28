# 现代 CSS 参考教程
> **基于 [MDN Web Docs](https://developer.mozilla.org/zh-CN/docs/Learn_web_development/Core/Styling_basics) 编写**
> 从零开始，系统掌握 CSS —— 让网页从「能看」到「好看」。

---

# 第一章 CSS 是什么？

**CSS**（Cascading Style Sheets，层叠样式表）是用于控制网页外观的样式语言。HTML 定义内容的**结构和含义**，CSS 定义内容的**呈现方式**。

## 1.1 浏览器默认样式

即使你没有写任何 CSS，浏览器也会给 HTML 应用一套内置的默认样式——标题会比正文大，段落之间有间距，链接带有颜色和下划线。这些默认样式确保页面在没有 CSS 的情况下仍然可读。

CSS 的作用就是让你**接管这些样式的控制权**，按你想要的方式呈现页面。

## 1.2 CSS 能做什么

| 能力 | 示例 |
|------|------|
| 文本样式 | 字体、颜色、大小、行高、对齐 |
| 布局 | Flexbox、Grid、多列布局 |
| 装饰 | 背景、边框、阴影、圆角 |
| 动画 | 过渡效果、关键帧动画 |
| 响应式 | 让同一页面适配手机、平板、桌面 |

## 1.3 核心理念

> **HTML 管「是什么」，CSS 管「长什么样」。**

不要用 HTML 控制外观（如 `<font>` 标签），也不要用 CSS 表达内容含义。职责分离是前端开发的基本原则。

---

# 第二章 CSS 语法基础

## 2.1 规则结构

一条 CSS 规则由**选择器**和**声明块**组成：

```css
选择器 {
  属性: 值;
  属性: 值;
}
```

具体示例：

```css
h1 {
  color: red;
  font-size: 2.5em;
}
```

- **选择器**（`h1`）：指定要样式化的 HTML 元素
- **声明**（`color: red;`）：由属性和值组成，以冒号分隔，分号结尾
- **声明块**：大括号内的所有声明

## 2.2 多条规则

一个样式表通常包含许多规则：

```css
h1 {
  color: red;
  font-size: 2.5em;
}

p {
  color: #333;
  line-height: 1.6;
}

a {
  color: steelblue;
  text-decoration: none;
}
```

## 2.3 注释

```css
/* 这是一条 CSS 注释 */

/* 
   多行注释
   也是这样写
*/
```

---

# 第三章 如何将 CSS 应用到 HTML

有三种方式将 CSS 应用到 HTML 文档：

## 3.1 外部样式表（推荐）

创建独立的 `.css` 文件，用 `<link>` 标签引入：

```html
<head>
  <link rel="stylesheet" href="styles.css" />
</head>
```

**优点**：样式与结构分离，多个页面可共享同一样式表，便于维护。

## 3.2 内部样式表

在 `<style>` 标签中直接写 CSS：

```html
<head>
  <style>
    h1 {
      color: red;
    }
  </style>
</head>
```

**适用场景**：单页面应用或快速原型。

## 3.3 行内样式（尽量避免）

直接写在元素的 `style` 属性中：

```html
<p style="color: red; font-size: 18px;">这段文字是红色的。</p>
```

**缺点**：无法复用，难以维护，优先级过高，打破了结构与样式的分离。

---

# 第四章 选择器

选择器是 CSS 的寻址系统——它告诉浏览器「这条规则要应用到哪些元素上」。

## 4.1 基础选择器

### 类型选择器（元素选择器）

选中所有指定类型的 HTML 元素：

```css
p {
  color: green;
}

h1 {
  font-size: 2em;
}
```

### 类选择器

选中所有带有指定 `class` 属性的元素，以 `.` 开头：

```css
.highlight {
  background-color: yellow;
}

.btn-primary {
  background-color: steelblue;
  color: white;
  padding: 8px 16px;
}
```

```html
<p class="highlight">这段文字有黄色背景。</p>
<button class="btn-primary">点击我</button>
```

### ID 选择器

选中具有指定 `id` 的唯一元素，以 `#` 开头：

```css
#main-title {
  font-size: 3em;
}
```

```html
<h1 id="main-title">页面标题</h1>
```

> 每个 `id` 在页面中必须唯一。优先使用类选择器，ID 选择器优先级很高，不利于样式覆盖。

### 通配选择器

选中所有元素：

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
```

## 4.2 组合选择器

| 选择器 | 语法 | 含义 |
|--------|------|------|
| 后代选择器 | `A B` | A 内部的所有 B（任意层级） |
| 子选择器 | `A > B` | A 的直接子元素 B |
| 相邻兄弟选择器 | `A + B` | 紧跟在 A 后面的 B |
| 通用兄弟选择器 | `A ~ B` | A 之后的所有兄弟 B |

```css
/* 列表项中的 em 元素 */
li em {
  color: rebeccapurple;
}

/* 紧跟在 h1 后面的段落 */
h1 + p {
  font-size: 1.2em;
}

/* article 的直接子段落 */
article > p {
  line-height: 1.8;
}
```

## 4.3 属性选择器

根据元素的属性或属性值来选择：

```css
/* 有 title 属性的链接 */
a[title] {
  color: orange;
}

/* href 值完全匹配的链接 */
a[href="https://example.com"] {
  font-weight: bold;
}

/* href 值以 https 开头的链接 */
a[href^="https"] {
  color: green;
}

/* href 值以 .pdf 结尾的链接 */
a[href$=".pdf"] {
  color: red;
}

/* href 值包含 example 的链接 */
a[href*="example"] {
  text-decoration: underline;
}
```

## 4.4 伪类选择器

选择元素的**特定状态**，以 `:` 开头：

```css
/* 未访问的链接 */
a:link { color: blue; }

/* 已访问的链接 */
a:visited { color: purple; }

/* 鼠标悬停 */
a:hover { text-decoration: none; }

/* 正在点击 */
a:active { color: red; }

/* 获得焦点（键盘导航） */
input:focus { border-color: steelblue; }

/* 第一个子元素 */
li:first-child { font-weight: bold; }

/* 最后一个子元素 */
li:last-child { border-bottom: none; }

/* 第偶数个子元素 */
tr:nth-child(even) { background-color: #f5f5f5; }
```

## 4.5 伪元素选择器

选择元素的**某个部分**，以 `::` 开头：

```css
/* 段落的第一行 */
p::first-line {
  font-weight: bold;
}

/* 段落的第一个字母 */
p::first-letter {
  font-size: 2em;
  float: left;
}

/* 在元素内容前插入 */
.quote::before {
  content: "「";
}

/* 在元素内容后插入 */
.quote::after {
  content: "」";
}

/* 用户选中的文本 */
::selection {
  background-color: steelblue;
  color: white;
}
```

## 4.6 选择器列表

用逗号将多个选择器合并，共享同一组声明：

```css
h1,
h2,
h3 {
  font-family: "Georgia", serif;
  color: #333;
}
```

> ⚠️ 如果选择器列表中有一个无效选择器，整条规则都会被忽略。

---

# 第五章 盒模型

CSS 中的每个元素都被视为一个「盒子」。理解盒模型是掌握 CSS 布局的关键。

## 5.1 盒子的四层结构

```
┌──────────────── margin（外边距）────────────────┐
│                                                  │
│  ┌──────────── border（边框）────────────────┐   │
│  │                                            │   │
│  │  ┌──────── padding（内边距）────────────┐  │   │
│  │  │                                      │  │   │
│  │  │        content（内容区）              │  │   │
│  │  │                                      │  │   │
│  │  └──────────────────────────────────────┘  │   │
│  │                                            │   │
│  └────────────────────────────────────────────┘   │
│                                                  │
└──────────────────────────────────────────────────┘
```

| 层 | 属性 | 作用 |
|----|------|------|
| 内容区 | `width` / `height` | 显示文本、图片等实际内容 |
| 内边距 | `padding` | 内容与边框之间的空间 |
| 边框 | `border` | 盒子的可见边界 |
| 外边距 | `margin` | 盒子与其他元素之间的空间 |

## 5.2 标准盒模型 vs 替代盒模型

**标准盒模型**（默认）：`width` / `height` 只设置**内容区**的大小。

```css
.box {
  width: 350px;
  padding: 25px;
  border: 5px solid black;
}
/* 实际占据宽度 = 350 + 25×2 + 5×2 = 410px */
```

**替代盒模型**（`border-box`）：`width` / `height` 设置的是**包含边框和内边距的总大小**。

```css
.box {
  box-sizing: border-box;
  width: 350px;
  padding: 25px;
  border: 5px solid black;
}
/* 实际占据宽度 = 350px（内容区自动收缩） */
```

**最佳实践**——全局启用 `border-box`，让尺寸计算更直观：

```css
*,
*::before,
*::after {
  box-sizing: border-box;
}
```

## 5.3 区块盒子与行内盒子

| 特性 | 区块盒子（Block） | 行内盒子（Inline） |
|------|------------------|-------------------|
| 换行 | 独占一行 | 不换行，与其他行内元素并排 |
| 宽度 | 默认撑满父容器 | 由内容决定 |
| `width`/`height` | 生效 | 不生效 |
| 垂直方向 margin/padding | 推开其他元素 | 不推开其他元素 |
| 典型元素 | `<div>` `<p>` `<h1>` `<section>` | `<span>` `<a>` `<em>` `<strong>` |

使用 `display` 属性可以切换盒子类型：

```css
/* 让行内元素表现为块级 */
span.block { display: block; }

/* 让块级元素表现为行内 */
p.inline { display: inline; }

/* 行内块：不换行，但 width/height 生效 */
.badge { display: inline-block; }
```

## 5.4 外边距折叠

当两个垂直方向的外边距相遇时，它们会合并为一个（取较大值）：

```css
.box-a { margin-bottom: 30px; }
.box-b { margin-top: 20px; }
/* 两者之间的实际间距 = 30px，而非 50px */
```

这只发生在**垂直方向**的**区块盒子**之间。

---

# 第六章 层叠、优先级与继承

这三个概念决定了「当多条规则作用于同一元素时，最终生效的是哪条」。

## 6.1 层叠（Cascade）

当两条规则优先级相同时，**后出现的规则覆盖先出现的**：

```css
p { color: red; }
p { color: blue; }
/* 段落最终是蓝色 */
```

## 6.2 优先级（Specificity）

当选择器不同时，**更具体的选择器胜出**：

```
行内样式(1000) > ID选择器(100) > 类/属性/伪类选择器(10) > 元素/伪元素选择器(1)
```

| 选择器 | 优先级计算 | 得分 |
|--------|-----------|------|
| `p` | 0-0-1 | 1 |
| `.intro` | 0-1-0 | 10 |
| `p.intro` | 0-1-1 | 11 |
| `#hero` | 1-0-0 | 100 |
| `#hero .title` | 1-1-0 | 110 |

```css
/* 0-0-1：元素选择器 */
h1 { color: blue; }

/* 0-1-0：类选择器，优先级更高 */
.main-title { color: red; }

/* h1.main-title 的元素最终显示红色 */
```

## 6.3 继承（Inheritance）

某些 CSS 属性会从父元素传递到子元素：

| 会继承的属性 | 不会继承的属性 |
|-------------|--------------|
| `color` | `width` / `height` |
| `font-family` | `margin` / `padding` |
| `font-size` | `border` |
| `line-height` | `background` |
| `text-align` | `display` |
| `visibility` | `position` |

```css
body {
  color: #333;
  font-family: "Microsoft YaHei", sans-serif;
  line-height: 1.6;
}
/* 所有子元素都会继承这些文本属性 */
```

## 6.4 控制继承的特殊值

| 值 | 作用 |
|----|------|
| `inherit` | 强制继承父元素的值 |
| `initial` | 恢复为属性的初始默认值 |
| `unset` | 可继承属性相当于 `inherit`，否则相当于 `initial` |
| `revert` | 恢复为浏览器默认样式 |

```css
.reset {
  all: unset;  /* 重置所有属性 */
}
```

## 6.5 `!important`

`!important` 可以覆盖所有优先级规则，但应**尽量避免使用**：

```css
p { color: red !important; }
/* 即使有更高优先级的选择器，也会强制应用红色 */
```

> ⚠️ 过度使用 `!important` 会让样式表变得难以调试和维护。

---

# 第七章 值与单位

## 7.1 长度单位

### 绝对单位

| 单位 | 含义 |
|------|------|
| `px` | 像素，最常用的绝对单位 |
| `cm` / `mm` | 厘米/毫米，主要用于打印样式 |
| `pt` | 磅（1pt = 1/72 英寸），常见于字号 |

### 相对单位

| 单位 | 相对于 | 典型用途 |
|------|--------|---------|
| `em` | 父元素的字号 | 组件内部间距 |
| `rem` | 根元素（`<html>`）的字号 | 全局统一的字号和间距 |
| `%` | 父元素的对应属性 | 宽度、容器比例 |
| `vw` | 视口宽度的 1% | 全屏布局 |
| `vh` | 视口高度的 1% | 全屏区域 |
| `vmin` | `vw` 和 `vh` 中较小的 | 自适应正方形 |
| `vmax` | `vw` 和 `vh` 中较大的 | — |

```css
html { font-size: 16px; }

h1 { font-size: 2rem; }       /* = 32px */
p  { font-size: 1rem; }       /* = 16px */
.container { width: 80%; }     /* 父元素宽度的 80% */
.hero { height: 100vh; }       /* 视口满高 */
```

## 7.2 颜色值

```css
/* 关键字 */
color: red;
color: steelblue;
color: transparent;

/* 十六进制 */
color: #ff0000;
color: #333;          /* #333333 的简写 */
color: #ff000080;     /* 最后两位是透明度 */

/* RGB / RGBA */
color: rgb(255, 0, 0);
color: rgba(255, 0, 0, 0.5);  /* 50% 透明 */

/* HSL / HSLA（色相、饱和度、亮度） */
color: hsl(0, 100%, 50%);     /* 红色 */
color: hsla(210, 50%, 50%, 0.8);
```

## 7.3 函数值

```css
/* 计算 */
width: calc(100% - 60px);

/* 最小/最大值 */
width: min(100%, 800px);
width: max(300px, 50%);
width: clamp(300px, 50%, 800px);  /* 在最小值和最大值之间取中间值 */

/* 渐变 */
background: linear-gradient(to right, #667eea, #764ba2);

/* 自定义属性（CSS 变量） */
:root {
  --primary-color: #3498db;
  --spacing: 16px;
}

.button {
  background-color: var(--primary-color);
  padding: var(--spacing);
}
```

---

# 第八章 调整大小

## 8.1 固有尺寸与外部尺寸

- **固有尺寸**：元素未被 CSS 设置大小时的自然尺寸（如图片的原始分辨率）
- **外部尺寸**：通过 CSS 明确指定的大小

```css
/* 外部尺寸 */
.box {
  width: 200px;
  height: 100px;
}
```

## 8.2 百分比

百分比相对于**父元素**的对应属性：

```css
.child {
  width: 50%;    /* 父容器宽度的 50% */
}
```

> ⚠️ `margin` 和 `padding` 的百分比始终相对于父元素的**宽度**（包括上下方向），这常常令人困惑。

## 8.3 min- 和 max- 约束

```css
.container {
  width: 90%;
  max-width: 1200px;  /* 最大不超过 1200px */
  min-width: 320px;   /* 最小不低于 320px */
}

/* 响应式图片的经典写法 */
img {
  max-width: 100%;    /* 不超过容器宽度 */
  height: auto;       /* 保持宽高比 */
}
```

## 8.4 视口单位

```css
.hero-section {
  height: 100vh;   /* 占满整个视口高度 */
  width: 100vw;    /* 占满整个视口宽度 */
}
```

---

# 第九章 背景与边框

## 9.1 背景颜色

```css
.box {
  background-color: #f0f0f0;
}
```

## 9.2 背景图片

```css
.hero {
  background-image: url("images/bg.jpg");
  background-repeat: no-repeat;      /* 不平铺 */
  background-size: cover;            /* 覆盖整个容器 */
  background-position: center;       /* 居中 */
  background-attachment: fixed;      /* 滚动时固定 */
}

/* 简写 */
.hero {
  background: url("images/bg.jpg") no-repeat center / cover;
}
```

## 9.3 渐变背景

```css
/* 线性渐变 */
.gradient-1 {
  background: linear-gradient(to right, #667eea, #764ba2);
}

/* 径向渐变 */
.gradient-2 {
  background: radial-gradient(circle, #667eea, #764ba2);
}
```

## 9.4 多重背景

```css
.box {
  background:
    url("icon.png") no-repeat top right,
    linear-gradient(to bottom, #fff, #f0f0f0);
}
```

## 9.5 边框

```css
.box {
  /* 简写 */
  border: 2px solid #333;

  /* 分别设置 */
  border-width: 2px;
  border-style: solid;    /* solid / dashed / dotted / double / none */
  border-color: #333;

  /* 单独设置某一边 */
  border-top: 3px dashed red;
  border-bottom: none;
}
```

## 9.6 圆角

```css
.box {
  border-radius: 8px;            /* 四个角相同 */
  border-radius: 50%;            /* 正方形变圆形 */
  border-radius: 8px 0 0 8px;    /* 左上 右上 右下 左下 */
}
```

## 9.7 阴影

```css
.box {
  /* x偏移 y偏移 模糊半径 扩展半径 颜色 */
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);

  /* 内阴影 */
  box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.1);

  /* 多重阴影 */
  box-shadow:
    0 1px 3px rgba(0, 0, 0, 0.12),
    0 1px 2px rgba(0, 0, 0, 0.24);
}
```

---

# 第十章 溢出处理

当盒子内容超出其设定大小时，就会发生**溢出**。

## 10.1 `overflow` 属性

```css
.box {
  width: 300px;
  height: 200px;

  overflow: visible;   /* 默认：内容溢出可见 */
  overflow: hidden;    /* 裁剪溢出内容 */
  overflow: scroll;    /* 始终显示滚动条 */
  overflow: auto;      /* 仅在需要时显示滚动条 */
}

/* 分别控制水平和垂直方向 */
.box {
  overflow-x: hidden;
  overflow-y: auto;
}
```

## 10.2 最佳实践

- 尽量避免给元素设置固定高度，让内容自然撑开
- 如果必须限制高度，使用 `min-height` 代替 `height`
- 需要滚动时，优先使用 `overflow: auto` 而非 `overflow: scroll`

---

# 第十一章 文本样式

## 11.1 字体

```css
body {
  /* 字体族：优先使用前面的，依次回退 */
  font-family: "Helvetica Neue", Arial, "PingFang SC", "Microsoft YaHei", sans-serif;

  font-size: 16px;
  font-weight: 400;       /* 100-900，400=normal，700=bold */
  font-style: italic;     /* normal / italic / oblique */
}

/* 简写 */
body {
  font: italic 400 16px/1.6 "Helvetica Neue", sans-serif;
  /*    样式  粗细  大小/行高  字体族 */
}
```

## 11.2 文本属性

```css
p {
  color: #333;
  text-align: center;           /* left / right / center / justify */
  text-decoration: underline;   /* none / underline / line-through / overline */
  text-transform: uppercase;    /* none / uppercase / lowercase / capitalize */
  text-indent: 2em;             /* 首行缩进 */
  letter-spacing: 0.05em;       /* 字间距 */
  word-spacing: 0.1em;          /* 词间距 */
  line-height: 1.6;             /* 行高 */
  white-space: nowrap;          /* 禁止换行 */
  text-overflow: ellipsis;      /* 溢出显示省略号（需配合 overflow: hidden） */
}
```

## 11.3 Web 字体

```css
@font-face {
  font-family: "MyCustomFont";
  src: url("fonts/custom.woff2") format("woff2"),
       url("fonts/custom.woff") format("woff");
  font-weight: normal;
  font-style: normal;
  font-display: swap;  /* 先显示后备字体，加载完成后切换 */
}

body {
  font-family: "MyCustomFont", sans-serif;
}
```

## 11.4 列表样式

```css
ul {
  list-style-type: disc;         /* disc / circle / square / none */
  list-style-position: inside;   /* inside / outside */
}

/* 自定义标记 */
ul {
  list-style: none;
  padding-left: 1.5em;
}
ul li::before {
  content: "→ ";
}
```

---

# 第十二章 布局基础

## 12.1 常规流

默认情况下，块级元素从上到下排列，行内元素从左到右排列。这就是**常规流**（Normal Flow）。

## 12.2 Flexbox（弹性布局）

一维布局方案，适合沿一个方向排列元素：

```css
.container {
  display: flex;
  justify-content: center;     /* 主轴对齐 */
  align-items: center;         /* 交叉轴对齐 */
  gap: 16px;                   /* 子元素间距 */
}
```

**主轴方向**：

```css
.container {
  flex-direction: row;            /* 默认：从左到右 */
  flex-direction: row-reverse;    /* 从右到左 */
  flex-direction: column;         /* 从上到下 */
  flex-direction: column-reverse; /* 从下到上 */
}
```

**换行**：

```css
.container {
  flex-wrap: wrap;    /* 允许换行 */
}
```

**子元素属性**：

```css
.item {
  flex: 1;            /* 等分剩余空间 */
  flex: 0 0 200px;    /* 不伸缩，固定 200px 宽 */
  /* flex: flex-grow flex-shrink flex-basis */
  align-self: flex-end; /* 单独控制交叉轴对齐 */
  order: -1;            /* 调整排列顺序 */
}
```

**经典居中**：

```css
.center {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

## 12.3 Grid（网格布局）

二维布局方案，同时控制行和列：

```css
.container {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr;  /* 三列，中间列是两侧的两倍宽 */
  grid-template-rows: auto 1fr auto;   /* 三行 */
  gap: 16px;
}
```

**常用模式**：

```css
/* 等宽三列 */
.grid-3 {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
}

/* 自适应列数（响应式） */
.grid-auto {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 20px;
}

/* 指定区域 */
.layout {
  display: grid;
  grid-template-areas:
    "header header"
    "sidebar main"
    "footer footer";
  grid-template-columns: 250px 1fr;
}

.header  { grid-area: header; }
.sidebar { grid-area: sidebar; }
.main    { grid-area: main; }
.footer  { grid-area: footer; }
```

## 12.4 定位（Position）

| 值 | 行为 |
|----|------|
| `static` | 默认，遵循常规流 |
| `relative` | 相对于自身原始位置偏移，不脱离常规流 |
| `absolute` | 相对于最近的已定位祖先元素偏移，脱离常规流 |
| `fixed` | 相对于视口偏移，滚动时不动 |
| `sticky` | 正常滚动，到达阈值后「粘住」 |

```css
.tooltip {
  position: absolute;
  top: 100%;
  left: 50%;
  transform: translateX(-50%);
}

.navbar {
  position: sticky;
  top: 0;
  z-index: 100;
}
```

## 12.5 何时用 Flex，何时用 Grid

| 场景 | 推荐方案 |
|------|---------|
| 导航栏、工具栏 | Flexbox |
| 一行卡片 | Flexbox |
| 水平/垂直居中 | Flexbox |
| 整体页面骨架 | Grid |
| 仪表盘、瀑布流 | Grid |
| 同时控制行列 | Grid |

---

# 第十三章 响应式设计

## 13.1 视口 meta 标签

在 HTML `<head>` 中添加，让页面正确适配移动设备：

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```

## 13.2 媒体查询

根据屏幕特征应用不同的样式：

```css
/* 基础样式（移动端优先） */
.container {
  padding: 16px;
}

/* 平板 */
@media (min-width: 768px) {
  .container {
    padding: 24px;
    max-width: 720px;
    margin: 0 auto;
  }
}

/* 桌面 */
@media (min-width: 1024px) {
  .container {
    max-width: 960px;
  }
}

/* 大屏 */
@media (min-width: 1200px) {
  .container {
    max-width: 1140px;
  }
}
```

## 13.3 响应式图片

```css
img {
  max-width: 100%;
  height: auto;
}
```

## 13.4 常用响应式技巧

```css
/* Grid 自动适配列数 */
.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 20px;
}

/* clamp() 实现流体字号 */
h1 {
  font-size: clamp(1.5rem, 4vw, 3rem);
}

/* 容器查询（现代浏览器支持） */
.card-container {
  container-type: inline-size;
}

@container (min-width: 400px) {
  .card {
    flex-direction: row;
  }
}
```

---

# 第十四章 调试与组织 CSS

## 14.1 使用浏览器开发者工具

- **F12** 打开开发者工具
- **Elements 面板**：查看 DOM 结构和每个元素的计算样式
- **Styles 面板**：查看、修改、禁用 CSS 规则
- **Computed 面板**：查看最终生效的属性值和盒模型
- **设备模拟**：测试不同屏幕尺寸

## 14.2 常见调试技巧

```css
/* 给元素添加临时边框，查看布局 */
* { outline: 1px solid red; }

/* 高亮特定元素 */
.debug { background: rgba(255, 0, 0, 0.1) !important; }
```

## 14.3 CSS 组织原则

**命名规范——BEM 方法**：

```css
/* Block（块） */
.card { }

/* Element（元素） */
.card__title { }
.card__body { }
.card__footer { }

/* Modifier（修饰符） */
.card--featured { }
.card__title--large { }
```

**文件组织**：

```
styles/
├── base.css        /* 重置和基础样式 */
├── layout.css      /* 页面布局 */
├── components.css  /* 组件样式 */
├── utilities.css   /* 工具类 */
└── main.css        /* 入口，导入以上文件 */
```

**CSS 自定义属性（变量）管理设计令牌**：

```css
:root {
  /* 颜色 */
  --color-primary: #3498db;
  --color-secondary: #2ecc71;
  --color-text: #333;
  --color-bg: #fff;

  /* 字号 */
  --font-size-sm: 0.875rem;
  --font-size-base: 1rem;
  --font-size-lg: 1.25rem;

  /* 间距 */
  --spacing-xs: 4px;
  --spacing-sm: 8px;
  --spacing-md: 16px;
  --spacing-lg: 24px;
  --spacing-xl: 32px;

  /* 圆角 */
  --radius-sm: 4px;
  --radius-md: 8px;
  --radius-lg: 16px;

  /* 阴影 */
  --shadow-sm: 0 1px 3px rgba(0, 0, 0, 0.12);
  --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
}
```

---

# 参考资源

[MDN CSS 参考](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Reference)
[MDN CSS 学习路径](https://developer.mozilla.org/zh-CN/docs/Learn_web_development/Core/Styling_basics)
[CSS Tricks](https://css-tricks.com/) — 实用技巧和教程
[Flexbox Froggy](https://flexboxfroggy.com/) — 游戏化学 Flexbox
[Grid Garden](https://cssgridgarden.com/) — 游戏化学 Grid
[Can I Use](https://caniuse.com/) — 浏览器兼容性查询
