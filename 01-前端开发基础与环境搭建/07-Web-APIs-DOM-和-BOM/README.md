# DOM 与 BOM 参考教程
> **基于 [MDN Web Docs](https://developer.mozilla.org/zh-CN/docs/Web/API) 编写**
> 从零开始，系统掌握浏览器提供的核心 JavaScript API —— 让你真正操控网页。

---

# 第一章 全局视角：浏览器中的 JavaScript

## 1.1 三大核心

当你在浏览器中编写 JavaScript 时，实际上在和三个层面打交道：

| 层面 | 说明 | 规范来源 |
|------|------|----------|
| **ECMAScript** | 语言本身的语法、类型、运算符、语句 | ECMA-262 |
| **DOM**（Document Object Model） | 操作页面内容的 API | W3C / WHATWG |
| **BOM**（Browser Object Model） | 与浏览器窗口交互的 API | WHATWG HTML 规范 |

```
window (BOM 的根)
├── document (DOM 的入口)
├── location
├── history
├── navigator
├── screen
├── localStorage / sessionStorage
├── fetch / XMLHttpRequest
├── setTimeout / setInterval
└── console
```

## 1.2 DOM 是什么

DOM（文档对象模型）是浏览器把 HTML/XML 文档解析后生成的**树形数据结构**。每一个标签、属性、文本都会变成树上的一个**节点（Node）**，JavaScript 通过 DOM API 来读取、修改、新增、删除这些节点，从而动态改变页面内容。

## 1.3 BOM 是什么

BOM（浏览器对象模型）是浏览器提供的、**与页面内容无关**的 API 集合。它让 JavaScript 能够：

- 获取浏览器窗口尺寸
- 控制 URL 导航
- 管理浏览历史
- 检测用户网络状态
- 使用定时器
- 存储数据

BOM 长期没有正式标准，但如今 WHATWG 的 HTML 规范已经将大部分 BOM API 纳入其中。

---

# 第二章 DOM 核心概念

## 2.1 文档树

浏览器加载 HTML 后会构建一棵节点树。以下 HTML：

```html
<!DOCTYPE html>
<html>
  <head>
    <title>示例页面</title>
  </head>
  <body>
    <h1 id="title">你好</h1>
    <p class="intro">这是一段介绍。</p>
  </body>
</html>
```

会生成这样的节点树：

```
Document
└── html (Element)
    ├── head (Element)
    │   └── title (Element)
    │       └── "示例页面" (Text)
    └── body (Element)
        ├── h1#title (Element)
        │   └── "你好" (Text)
        └── p.intro (Element)
            └── "这是一段介绍。" (Text)
```

## 2.2 节点类型

DOM 定义了多种节点类型，每种对应一个数字常量：

| 类型 | 常量名 | 值 | 说明 |
|------|--------|----|------|
| `Document` | `DOCUMENT_NODE` | 9 | 整棵树的根节点 |
| `Element` | `ELEMENT_NODE` | 1 | HTML 标签，如 `<div>`、`<p>` |
| `Text` | `TEXT_NODE` | 3 | 标签内的文本内容 |
| `Comment` | `COMMENT_NODE` | 8 | HTML 注释 `<!-- -->` |
| `DocumentType` | `DOCUMENT_TYPE_NODE` | 10 | `<!DOCTYPE html>` |
| `DocumentFragment` | `DOCUMENT_FRAGMENT_NODE` | 11 | 轻量级文档容器（不在 DOM 树中） |

```javascript
document.nodeType;                  // 9 — DOCUMENT_NODE
document.documentElement.nodeType;  // 1 — ELEMENT_NODE
document.body.firstChild.nodeType;  // 3 — TEXT_NODE（通常是换行空白）
```

## 2.3 Document 与 document.documentElement

- `document` 是类型为 `Document`（nodeType = 9）的根节点
- `document.documentElement` 返回 `<html>` 元素（nodeType = 1）
- `document.head` 返回 `<head>` 元素
- `document.body` 返回 `<body>` 元素

```javascript
document.documentElement === document.querySelector('html'); // true
document.body === document.querySelector('body');             // true
```

## 2.4 Node 接口的核心属性与方法

所有节点都实现了 `Node` 接口，以下是常用成员：

**属性：**

| 属性 | 说明 |
|------|------|
| `nodeName` | 节点名称（元素返回大写标签名，如 `"DIV"`） |
| `nodeType` | 节点类型数字 |
| `nodeValue` | 节点值（文本节点返回文本，元素节点返回 `null`） |
| `textContent` | 节点及其后代的文本内容 |
| `parentNode` | 父节点 |
| `parentElement` | 父**元素**节点（父节点不是元素时返回 `null`） |
| `childNodes` | 所有子节点（NodeList，包含文本和注释节点） |
| `children` | 所有子**元素**节点（HTMLCollection） |
| `firstChild` / `lastChild` | 第一个 / 最后一个子节点 |
| `firstElementChild` / `lastElementChild` | 第一个 / 最后一个子元素 |
| `nextSibling` / `previousSibling` | 下一个 / 上一个兄弟节点 |
| `nextElementSibling` / `previousElementSibling` | 下一个 / 上一个兄弟元素 |
| `ownerDocument` | 所属文档（通常就是 `document`） |

**方法：**

| 方法 | 说明 |
|------|------|
| `appendChild(node)` | 在末尾追加子节点 |
| `insertBefore(newNode, refNode)` | 在 `refNode` 之前插入节点 |
| `removeChild(node)` | 移除子节点（返回被移除的节点） |
| `replaceChild(newNode, oldNode)` | 替换子节点 |
| `cloneNode(deep)` | 克隆节点（`deep = true` 表示深克隆） |
| `contains(node)` | 判断是否包含指定节点 |
| `hasChildNodes()` | 判断是否有子节点 |

> **`parentNode` 与 `parentElement` 的区别：** 绝大多数时候它们相同。唯一的例外是 `document.documentElement`——它的 `parentNode` 是 `document`（类型为 Document），而 `parentElement` 为 `null`（因为 Document 不是 Element）。

---

# 第三章 选取元素

## 3.1 现代选择器方法（推荐）

这两个方法使用 CSS 选择器语法，功能最强大：

```javascript
// 返回第一个匹配的元素，找不到返回 null
document.querySelector('#title');
document.querySelector('.intro');
document.querySelector('div > p:first-child');
document.querySelector('[data-role="admin"]');

// 返回所有匹配的元素（静态 NodeList）
document.querySelectorAll('.item');
document.querySelectorAll('ul li');
document.querySelectorAll('input[type="text"]');
```

`querySelectorAll` 返回的是**静态** `NodeList`：返回时就确定了内容，之后 DOM 变化不会反映在结果中。

## 3.2 经典选择器方法

| 方法 | 返回类型 | 实时 / 静态 |
|------|----------|-------------|
| `getElementById(id)` | `Element \| null` | — |
| `getElementsByClassName(name)` | `HTMLCollection` | 实时 |
| `getElementsByTagName(tag)` | `HTMLCollection` | 实时 |
| `getElementsByName(name)` | `NodeList` | 实时 |

```javascript
document.getElementById('title');
document.getElementsByClassName('intro');  // 实时集合
document.getElementsByTagName('p');        // 实时集合
document.getElementsByName('email');       // 实时 NodeList
```

## 3.3 HTMLCollection 与 NodeList

|  | HTMLCollection | 静态 NodeList | 实时 NodeList |
|--|----------------|---------------|---------------|
| 内容 | 仅元素 | 可含任意节点 | 可含任意节点 |
| 实时性 | 始终实时 | 不实时 | 实时 |
| `forEach` | 无 | 有 | 有 |
| 来源 | `getElementsBy*` | `querySelectorAll` | `getElementsByName`、`childNodes` |

将 HTMLCollection 转为数组以使用数组方法：

```javascript
const items = document.getElementsByClassName('item');

// 方式一：展开运算符
const arr1 = [...items];

// 方式二：Array.from
const arr2 = Array.from(items);

// 之后可以使用 map、filter 等
arr1.filter(el => el.dataset.active === 'true');
```

## 3.4 在元素范围内查找

除 `getElementById` 外，所有选择器方法都可以在任意元素上调用，缩小查找范围：

```javascript
const nav = document.querySelector('nav');
const links = nav.querySelectorAll('a');
const activeLink = nav.querySelector('.active');
```

## 3.5 特殊快捷属性

```javascript
document.forms;           // 所有 <form> 元素（HTMLCollection）
document.images;          // 所有 <img> 元素
document.links;           // 所有带 href 的 <a> 和 <area>
document.scripts;         // 所有 <script> 元素
document.styleSheets;     // 所有样式表
```

## 3.6 closest() — 向上查找

`element.closest(selector)` 从当前元素开始**向上**遍历祖先，返回第一个匹配选择器的元素：

```javascript
const button = document.querySelector('button');
const form = button.closest('form');     // 最近的祖先 <form>
const card = button.closest('.card');    // 最近的 class 为 card 的祖先
```

## 3.7 matches() — 判断是否匹配

```javascript
const el = document.querySelector('p');
el.matches('.intro');       // true 或 false
el.matches('p.intro');      // true 或 false
```

---

# 第四章 遍历 DOM 树

## 4.1 所有节点 vs 仅元素节点

DOM 遍历属性分为两组：

| 所有节点（含文本、注释） | 仅元素节点 |
|--------------------------|------------|
| `parentNode` | `parentElement` |
| `childNodes` | `children` |
| `firstChild` | `firstElementChild` |
| `lastChild` | `lastElementChild` |
| `nextSibling` | `nextElementSibling` |
| `previousSibling` | `previousElementSibling` |

实际开发中几乎总是使用右侧的**元素节点版本**，因为空白文本节点会干扰结果。

```html
<ul>
  <li>A</li>
  <li>B</li>
  <li>C</li>
</ul>
```

```javascript
const ul = document.querySelector('ul');

// 包含空白文本节点
ul.childNodes.length;           // 7（3 个 li + 4 个换行文本节点）
ul.firstChild;                  // #text（换行符）

// 仅元素节点
ul.children.length;             // 3
ul.firstElementChild;           // <li>A</li>
ul.lastElementChild;            // <li>C</li>
ul.children[1].textContent;     // "B"
```

## 4.2 遍历子元素的常用模式

```javascript
const list = document.querySelector('ul');

// for...of 遍历 children
for (const li of list.children) {
  console.log(li.textContent);
}

// querySelectorAll + forEach
list.querySelectorAll('li').forEach((li, index) => {
  console.log(`${index}: ${li.textContent}`);
});
```

## 4.3 递归遍历整棵子树

```javascript
function walkDOM(node, callback) {
  callback(node);
  for (const child of node.childNodes) {
    walkDOM(child, callback);
  }
}

walkDOM(document.body, node => {
  if (node.nodeType === Node.ELEMENT_NODE) {
    console.log(node.tagName);
  }
});
```

> 现代替代方案：使用 `TreeWalker` API（见第十三章）或 `document.createNodeIterator()` 可以更高效地遍历。

---

# 第五章 属性与特性

## 5.1 HTML 属性（Attribute）与 DOM 属性（Property）

这是一个常见的混淆点。简单来说：

- **HTML 属性**是写在 HTML 标签上的（`id="title"`、`class="intro"`）
- **DOM 属性**是 JavaScript 对象上的属性（`element.id`、`element.className`）

大部分标准属性会自动同步，但也有例外。

## 5.2 读取和设置属性

**方式一：DOM 属性（推荐用于标准属性）**

```javascript
const img = document.querySelector('img');

img.src;                   // 读取（注意：返回完整 URL）
img.alt = '新的描述';      // 设置
img.id;
img.hidden = true;         // 隐藏元素
```

**方式二：getAttribute / setAttribute（适用于自定义属性和非标准属性）**

```javascript
const el = document.querySelector('#title');

el.getAttribute('class');              // "intro active"
el.setAttribute('class', 'highlight'); // 设置
el.removeAttribute('title');           // 移除
el.hasAttribute('data-id');            // 判断是否存在
```

## 5.3 class 的特殊处理

由于 `class` 是 JavaScript 保留字，DOM 属性名为 `className`：

```javascript
el.className;                 // "intro active"（返回字符串）
el.className = 'new-class';   // 覆盖所有 class
```

同理，`<label>` 的 `for` 属性对应 `htmlFor`。

## 5.4 classList — 推荐的 class 操作方式

`classList` 属性返回 `DOMTokenList`，提供精细的 class 操作：

```javascript
const el = document.querySelector('.card');

el.classList.add('active');              // 添加
el.classList.add('visible', 'animate');  // 添加多个
el.classList.remove('hidden');           // 移除
el.classList.toggle('dark');             // 有则移除，无则添加
el.classList.toggle('dark', isDark);     // 第二个参数强制添加/移除
el.classList.contains('active');         // 判断是否包含 → true / false
el.classList.replace('old', 'new');      // 替换

// 遍历
for (const cls of el.classList) {
  console.log(cls);
}
```

## 5.5 data-* 自定义数据属性

HTML5 允许通过 `data-*` 属性存储自定义数据，JavaScript 通过 `dataset` 属性访问：

```html
<article data-author="张三" data-publish-date="2026-01-15" data-category="tech">
  ...
</article>
```

```javascript
const article = document.querySelector('article');

article.dataset.author;        // "张三"
article.dataset.publishDate;   // "2026-01-15"（注意驼峰转换）
article.dataset.category;      // "tech"

article.dataset.views = '1024'; // 设置新的 data 属性
delete article.dataset.category; // 删除
```

> **命名规则：** HTML 中的 `data-publish-date` 会转为 JavaScript 中的 `dataset.publishDate`（短横线转驼峰）。

## 5.6 内容操作

| 属性 | 说明 | 是否解析 HTML |
|------|------|---------------|
| `textContent` | 获取/设置纯文本内容 | 否（安全） |
| `innerHTML` | 获取/设置 HTML 内容 | 是（有 XSS 风险） |
| `outerHTML` | 获取/设置包含自身的 HTML | 是 |
| `innerText` | 类似 textContent，但考虑 CSS 可见性 | 否 |

```javascript
const div = document.querySelector('.content');

// textContent — 安全，推荐用于纯文本
div.textContent = '你好世界';

// innerHTML — 可插入 HTML，注意安全
div.innerHTML = '<strong>你好</strong>世界';

// ⚠️ 永远不要把用户输入直接放入 innerHTML
div.innerHTML = userInput; // 危险！可能导致 XSS 攻击
div.textContent = userInput; // 安全 ✓
```

---

# 第六章 创建、插入与删除节点

## 6.1 创建节点

```javascript
// 创建元素
const div = document.createElement('div');
const p = document.createElement('p');
const img = document.createElement('img');

// 创建文本节点
const text = document.createTextNode('Hello World');

// 创建文档片段（批量操作利器）
const fragment = document.createDocumentFragment();

// 创建注释
const comment = document.createComment('这是注释');
```

## 6.2 配置新元素

```javascript
const card = document.createElement('div');
card.className = 'card card-primary';
card.id = 'user-card';
card.dataset.userId = '42';
card.textContent = '这是一张卡片';
card.setAttribute('role', 'article');
```

## 6.3 插入节点

**经典方法：**

```javascript
const parent = document.querySelector('.container');
const child = document.createElement('p');
child.textContent = '新段落';

parent.appendChild(child);                         // 追加到末尾
parent.insertBefore(child, parent.firstChild);     // 插入到最前面
parent.insertBefore(child, referenceNode);          // 插入到指定节点之前
```

**现代方法（推荐）：**

`append()`、`prepend()`、`before()`、`after()`、`replaceWith()` 是更直观的现代 API，支持同时插入多个节点和字符串：

```javascript
const parent = document.querySelector('.list');
const newItem = document.createElement('li');
newItem.textContent = '新项目';

// 追加到末尾（支持字符串和多个节点）
parent.append(newItem);
parent.append('纯文本也可以', document.createElement('br'));

// 插入到开头
parent.prepend(newItem);

// 在某个元素之前/之后插入
const ref = document.querySelector('.ref-item');
ref.before(newItem);          // 在 ref 之前
ref.after(newItem);           // 在 ref 之后

// 替换
ref.replaceWith(newItem);
```

| 经典方法 | 现代方法 | 差异 |
|----------|----------|------|
| `appendChild(node)` | `append(...nodes)` | 现代版支持多参数和字符串 |
| `insertBefore(new, ref)` | `before(...nodes)` / `after(...nodes)` | 直接在目标元素上调用 |
| `replaceChild(new, old)` | `replaceWith(...nodes)` | 直接在被替换元素上调用 |

## 6.4 insertAdjacentHTML / insertAdjacentElement

精确控制插入位置，用字符串指定位置：

```javascript
const el = document.querySelector('.target');

// 四个位置：
// 'beforebegin' — el 之前（作为兄弟）
// 'afterbegin'  — el 内部最前面
// 'beforeend'   — el 内部最后面
// 'afterend'    — el 之后（作为兄弟）

el.insertAdjacentHTML('beforeend', '<p>新的段落</p>');
el.insertAdjacentElement('afterend', newElement);
el.insertAdjacentText('afterbegin', '文本内容');
```

```
<!-- beforebegin -->
<div class="target">
  <!-- afterbegin -->
  已有内容
  <!-- beforeend -->
</div>
<!-- afterend -->
```

## 6.5 删除节点

```javascript
const el = document.querySelector('#title');

// 现代方法（推荐）
el.remove();

// 经典方法
el.parentNode.removeChild(el);
```

## 6.6 克隆节点

```javascript
const original = document.querySelector('.card');

const shallowClone = original.cloneNode(false);  // 仅克隆元素本身
const deepClone = original.cloneNode(true);       // 克隆元素及所有后代

document.body.append(deepClone);
```

> **注意：** `cloneNode` 不会克隆通过 `addEventListener` 绑定的事件监听器。

## 6.7 DocumentFragment — 批量操作的性能优化

每次修改 DOM 都可能触发浏览器重排（reflow），`DocumentFragment` 让你在内存中组装好所有节点，最后一次性插入：

```javascript
const fragment = document.createDocumentFragment();

for (let i = 0; i < 1000; i++) {
  const li = document.createElement('li');
  li.textContent = `项目 ${i + 1}`;
  fragment.appendChild(li);
}

// 只触发一次 DOM 更新
document.querySelector('ul').appendChild(fragment);
```

## 6.8 使用 <template> 元素

HTML `<template>` 标签的内容不会被渲染，非常适合做模板：

```html
<template id="card-template">
  <div class="card">
    <h3 class="card-title"></h3>
    <p class="card-body"></p>
  </div>
</template>
```

```javascript
const template = document.querySelector('#card-template');

function createCard(title, body) {
  const clone = template.content.cloneNode(true);
  clone.querySelector('.card-title').textContent = title;
  clone.querySelector('.card-body').textContent = body;
  return clone;
}

document.querySelector('.cards').append(
  createCard('标题一', '内容一'),
  createCard('标题二', '内容二')
);
```

---

# 第七章 样式操作

## 7.1 内联样式：element.style

`element.style` 用于读取和设置**内联样式**，属性名使用驼峰命名：

```javascript
const box = document.querySelector('.box');

box.style.backgroundColor = '#3498db';
box.style.fontSize = '18px';
box.style.marginTop = '20px';
box.style.display = 'none';

// 移除某个内联样式
box.style.backgroundColor = '';
```

> `element.style` 只能读取内联样式（`style="..."` 属性中的值），不能读取来自样式表的计算样式。

## 7.2 批量设置样式：cssText

```javascript
box.style.cssText = 'background: red; font-size: 20px; padding: 10px;';

// 追加（不覆盖）
box.style.cssText += 'border: 1px solid black;';
```

## 7.3 读取计算样式：getComputedStyle

`window.getComputedStyle()` 返回元素最终的计算样式，无论样式来源：

```javascript
const box = document.querySelector('.box');
const styles = getComputedStyle(box);

styles.width;           // "200px"（计算后的实际值）
styles.backgroundColor; // "rgb(52, 152, 219)"
styles.fontSize;        // "18px"
styles.display;         // "flex"

// 获取伪元素的样式
const beforeStyles = getComputedStyle(box, '::before');
beforeStyles.content;   // "\"→\""
```

## 7.4 操作 CSS 类（最佳实践）

**直接修改样式不如切换 CSS 类。** 样式定义放在 CSS 中，JavaScript 只负责切换类名：

```css
/* CSS */
.hidden { display: none; }
.fade-in { opacity: 1; transition: opacity 0.3s; }
.fade-out { opacity: 0; transition: opacity 0.3s; }
```

```javascript
const el = document.querySelector('.modal');

el.classList.add('fade-in');
el.classList.remove('fade-out');
el.classList.toggle('hidden');
```

---

# 第八章 事件系统

## 8.1 事件的三种绑定方式

```javascript
const button = document.querySelector('#btn');

// ❌ 方式一：HTML 属性（不推荐，耦合 HTML 和 JS）
// <button onclick="handleClick()">

// ⚠️ 方式二：DOM 属性（同一事件只能绑定一个处理函数）
button.onclick = function(event) {
  console.log('clicked');
};

// ✅ 方式三：addEventListener（推荐）
button.addEventListener('click', handleClick);

function handleClick(event) {
  console.log('clicked', event);
}
```

## 8.2 addEventListener 详解

```javascript
element.addEventListener(eventType, handler, options);
```

第三个参数可以是布尔值（`useCapture`）或选项对象：

```javascript
button.addEventListener('click', handler, {
  capture: false,    // 是否在捕获阶段触发（默认 false）
  once: true,        // 触发一次后自动移除（非常实用）
  passive: true,     // 承诺不会调用 preventDefault()（提升滚动性能）
  signal: controller.signal // 通过 AbortController 移除监听（现代方式）
});
```

## 8.3 移除事件监听

```javascript
// 经典方式：必须传入同一个函数引用
button.addEventListener('click', handleClick);
button.removeEventListener('click', handleClick);

// ⚠️ 匿名函数无法移除
button.addEventListener('click', () => {});    // 无法移除！

// ✅ 现代方式：AbortController
const controller = new AbortController();

button.addEventListener('click', handleClick, {
  signal: controller.signal
});

// 一次性移除所有通过同一 controller 绑定的监听器
controller.abort();
```

## 8.4 事件对象

事件处理函数接收一个 `Event` 对象，包含事件的详细信息：

```javascript
button.addEventListener('click', function(event) {
  event.type;            // "click"
  event.target;          // 实际触发事件的元素
  event.currentTarget;   // 绑定监听器的元素（等于 this）
  event.timeStamp;       // 事件发生时间
  event.isTrusted;       // 是否由用户操作触发（而非脚本）

  event.preventDefault();   // 阻止默认行为（如阻止链接跳转）
  event.stopPropagation();  // 阻止事件继续传播
});
```

不同类型的事件有各自的扩展属性：

```javascript
// 鼠标事件
element.addEventListener('click', (e) => {
  e.clientX; e.clientY;   // 相对视口的坐标
  e.pageX; e.pageY;       // 相对页面的坐标
  e.button;               // 0=左键, 1=中键, 2=右键
  e.altKey; e.ctrlKey; e.shiftKey; e.metaKey; // 修饰键状态
});

// 键盘事件
element.addEventListener('keydown', (e) => {
  e.key;     // 按键的值，如 "Enter"、"a"、"ArrowUp"
  e.code;    // 物理键位，如 "KeyA"、"Digit1"
  e.repeat;  // 是否为长按重复触发
});

// 输入事件
input.addEventListener('input', (e) => {
  e.data;        // 输入的字符
  e.inputType;   // "insertText"、"deleteContentBackward" 等
});
```

## 8.5 事件传播：捕获与冒泡

DOM 事件传播分三个阶段：

```
1. 捕获阶段：从 window → document → ... → 目标父元素
2. 目标阶段：到达实际触发事件的元素
3. 冒泡阶段：从目标元素 → ... → document → window
```

```javascript
// 在冒泡阶段触发（默认）
parent.addEventListener('click', handler);

// 在捕获阶段触发
parent.addEventListener('click', handler, true);
// 或
parent.addEventListener('click', handler, { capture: true });
```

## 8.6 事件委托

利用冒泡机制，将事件监听器绑定在父元素上，通过 `event.target` 判断实际触发的子元素。这是处理动态列表的最佳实践：

```javascript
const list = document.querySelector('#todo-list');

// ❌ 给每个 li 都绑定监听器（性能差，新增元素无效）
list.querySelectorAll('li').forEach(li => {
  li.addEventListener('click', handleItemClick);
});

// ✅ 事件委托（推荐）
list.addEventListener('click', (event) => {
  const li = event.target.closest('li');
  if (!li) return;
  if (!list.contains(li)) return;
  handleItemClick(li);
});
```

> **`event.target` vs `event.currentTarget`：** `target` 是实际触发事件的元素（可能是深层子元素），`currentTarget` 是绑定监听器的元素。

## 8.7 常用事件类型速览

| 类别 | 事件 | 说明 |
|------|------|------|
| **鼠标** | `click`、`dblclick`、`mousedown`、`mouseup`、`mousemove` | 鼠标操作 |
| **鼠标移入/出** | `mouseenter`、`mouseleave` | 不冒泡 |
|  | `mouseover`、`mouseout` | 冒泡 |
| **键盘** | `keydown`、`keyup` | `keypress` 已弃用 |
| **输入** | `input`、`change`、`focus`、`blur` | 表单相关 |
| **表单** | `submit`、`reset` | 表单提交/重置 |
| **触摸** | `touchstart`、`touchmove`、`touchend` | 移动端 |
| **指针** | `pointerdown`、`pointermove`、`pointerup` | 统一鼠标/触摸/笔 |
| **滚动** | `scroll`、`wheel` | 页面/元素滚动 |
| **窗口** | `resize`、`load`、`DOMContentLoaded`、`beforeunload` | 窗口/文档事件 |
| **拖拽** | `dragstart`、`drag`、`dragover`、`drop`、`dragend` | 拖放操作 |
| **剪贴板** | `copy`、`cut`、`paste` | 剪贴板事件 |
| **动画** | `animationstart`、`animationend`、`transitionend` | CSS 动画 |

## 8.8 自定义事件

```javascript
// 创建自定义事件
const event = new CustomEvent('user-login', {
  detail: { username: '张三', role: 'admin' },
  bubbles: true,
  cancelable: true
});

// 监听
document.addEventListener('user-login', (e) => {
  console.log(`${e.detail.username} 以 ${e.detail.role} 身份登录`);
});

// 派发
document.dispatchEvent(event);
```

---

# 第九章 BOM — window 对象

## 9.1 window 的双重身份

`window` 既是 BOM 的核心对象，也是 JavaScript 的**全局对象**：

```javascript
var name = '张三';     // var 声明的变量成为 window 属性
window.name;           // "张三"

let age = 25;          // let/const 不会
window.age;            // undefined

function greet() {}    // 函数声明成为 window 方法
window.greet;          // ƒ greet() {}
```

> 在多标签页浏览器中，每个标签页拥有独立的 `window` 对象。

## 9.2 窗口尺寸

```javascript
// 视口尺寸（不含浏览器工具栏、滚动条）
window.innerWidth;   // 如 1440
window.innerHeight;  // 如 900

// 整个浏览器窗口尺寸（含工具栏）
window.outerWidth;
window.outerHeight;

// 页面实际内容尺寸
document.documentElement.scrollWidth;
document.documentElement.scrollHeight;

// 元素的尺寸与位置
const el = document.querySelector('.box');
el.clientWidth;  el.clientHeight;   // 内容 + padding（不含边框和滚动条）
el.offsetWidth;  el.offsetHeight;   // 内容 + padding + border
el.scrollWidth;  el.scrollHeight;   // 可滚动的总内容尺寸

// 获取精确的位置和尺寸（相对视口）
const rect = el.getBoundingClientRect();
rect.top; rect.right; rect.bottom; rect.left;
rect.width; rect.height;
rect.x; rect.y;
```

## 9.3 滚动

```javascript
// 当前滚动位置
window.scrollX;  // 或 window.pageXOffset
window.scrollY;  // 或 window.pageYOffset

// 滚动到指定位置
window.scrollTo(0, 500);              // 立即跳转
window.scrollTo({
  top: 500,
  left: 0,
  behavior: 'smooth'                  // 平滑滚动
});

// 相对滚动
window.scrollBy({ top: 100, behavior: 'smooth' });

// 让元素滚动到可视区域
const el = document.querySelector('#section-3');
el.scrollIntoView();                             // 顶部对齐
el.scrollIntoView({ behavior: 'smooth', block: 'center' }); // 平滑居中
```

## 9.4 定时器

```javascript
// 延迟执行（执行一次）
const timeoutId = setTimeout(() => {
  console.log('3 秒后执行');
}, 3000);
clearTimeout(timeoutId);  // 取消

// 重复执行
const intervalId = setInterval(() => {
  console.log('每隔 1 秒执行');
}, 1000);
clearInterval(intervalId); // 停止

// requestAnimationFrame（动画专用，每帧执行一次，约 60fps）
let animId;
function animate() {
  // 更新动画...
  animId = requestAnimationFrame(animate);
}
animId = requestAnimationFrame(animate);
cancelAnimationFrame(animId); // 停止
```

> **`requestAnimationFrame` vs `setInterval`：** 对于动画，始终使用 `requestAnimationFrame`。它会在浏览器下一次重绘前执行，频率自动匹配屏幕刷新率，标签页不可见时自动暂停，更节能高效。

## 9.5 弹窗方法

```javascript
// ⚠️ 以下方法会阻塞页面，现代开发中几乎不使用
alert('提示信息');
const confirmed = confirm('确认删除吗？');  // 返回 true / false
const name = prompt('请输入姓名', '默认值'); // 返回字符串或 null
```

## 9.6 窗口操作

```javascript
// 打开新窗口/标签页
const newWin = window.open('https://example.com', '_blank');

// 关闭（只能关闭由脚本打开的窗口）
newWin.close();

// 打印
window.print();
```

---

# 第十章 Location 与导航

## 10.1 Location 对象

`window.location` 和 `document.location` 指向同一个 `Location` 对象，用于获取和操作当前页面的 URL。

以 `https://www.example.com:8080/path/page?name=value#section` 为例：

| 属性 | 值 | 说明 |
|------|----|------|
| `location.href` | `"https://www.example.com:8080/path/page?name=value#section"` | 完整 URL |
| `location.protocol` | `"https:"` | 协议 |
| `location.host` | `"www.example.com:8080"` | 主机名 + 端口 |
| `location.hostname` | `"www.example.com"` | 仅主机名 |
| `location.port` | `"8080"` | 端口号 |
| `location.pathname` | `"/path/page"` | 路径 |
| `location.search` | `"?name=value"` | 查询字符串 |
| `location.hash` | `"#section"` | 锚点（片段标识符） |
| `location.origin` | `"https://www.example.com:8080"` | 协议 + 主机名 + 端口 |

## 10.2 导航方法

```javascript
// 跳转到新页面（会添加历史记录）
location.assign('https://example.com');
location.href = 'https://example.com';   // 等效

// 替换当前页面（不添加历史记录，不能后退）
location.replace('https://example.com');

// 重新加载当前页面
location.reload();
```

## 10.3 解析查询参数：URLSearchParams

```javascript
// 从当前 URL 解析
const params = new URLSearchParams(location.search);

params.get('name');        // "value"
params.has('name');        // true
params.getAll('tag');      // ["js", "dom"]（多值参数）
params.toString();         // "name=value&tag=js&tag=dom"

// 修改参数
params.set('page', '2');
params.append('filter', 'new');
params.delete('name');

// 遍历
for (const [key, value] of params) {
  console.log(`${key} = ${value}`);
}

// 构造 URL
const url = new URL('https://example.com/api');
url.searchParams.set('q', '搜索词');
url.searchParams.set('page', '1');
url.toString(); // "https://example.com/api?q=%E6%90%9C%E7%B4%A2%E8%AF%8D&page=1"
```

---

# 第十一章 History API

## 11.1 基本导航

```javascript
history.back();        // 后退一步 = 点击浏览器后退按钮
history.forward();     // 前进一步
history.go(-2);        // 后退两步
history.go(1);         // 前进一步
history.length;        // 历史记录条数
```

## 11.2 pushState 与 replaceState

这是单页应用（SPA）路由的基础 —— 在不刷新页面的情况下修改 URL：

```javascript
// 添加一条新的历史记录
history.pushState(
  { page: 'about' },        // state 对象（可序列化数据）
  '',                        // title（大部分浏览器忽略）
  '/about'                   // 新的 URL（同源）
);

// 替换当前历史记录（不新增）
history.replaceState(
  { page: 'about', scrollY: 200 },
  '',
  '/about'
);

// 读取当前状态
history.state; // { page: 'about', scrollY: 200 }
```

## 11.3 popstate 事件

当用户点击浏览器的前进/后退按钮时触发：

```javascript
window.addEventListener('popstate', (event) => {
  console.log('导航到:', location.pathname);
  console.log('状态数据:', event.state);

  // 根据 state 渲染对应的页面内容
  if (event.state?.page === 'about') {
    renderAboutPage();
  }
});
```

> **注意：** 调用 `pushState` 或 `replaceState` 本身不会触发 `popstate`，只有用户通过浏览器导航按钮或调用 `history.back()` / `history.forward()` 才会触发。

## 11.4 简易 SPA 路由示例

```javascript
function navigateTo(path, state = {}) {
  history.pushState(state, '', path);
  renderPage(path);
}

function renderPage(path) {
  const content = document.querySelector('#app');
  switch (path) {
    case '/':
      content.innerHTML = '<h1>首页</h1>';
      break;
    case '/about':
      content.innerHTML = '<h1>关于</h1>';
      break;
    default:
      content.innerHTML = '<h1>404</h1>';
  }
}

window.addEventListener('popstate', () => {
  renderPage(location.pathname);
});

document.addEventListener('click', (e) => {
  const link = e.target.closest('a[data-spa]');
  if (link) {
    e.preventDefault();
    navigateTo(link.getAttribute('href'));
  }
});
```

---

# 第十二章 Navigator 与设备信息

## 12.1 常用属性

```javascript
navigator.userAgent;     // 浏览器标识字符串
navigator.language;      // 首选语言，如 "zh-CN"
navigator.languages;     // 语言偏好数组 ["zh-CN", "zh", "en"]
navigator.onLine;        // 是否在线
navigator.cookieEnabled; // Cookie 是否启用
navigator.hardwareConcurrency; // CPU 逻辑核心数
navigator.maxTouchPoints;      // 最大触控点数（0 表示不支持触摸）
navigator.pdfViewerEnabled;    // 是否支持内联 PDF 查看
```

> **不要使用 `navigator.userAgent` 做功能检测。** 它容易被伪造且格式混乱。推荐使用特性检测（feature detection）。

## 12.2 网络状态监听

```javascript
window.addEventListener('online', () => {
  console.log('网络已恢复');
});

window.addEventListener('offline', () => {
  console.log('网络已断开');
});

// Network Information API（部分浏览器支持）
if (navigator.connection) {
  console.log(navigator.connection.effectiveType); // "4g"、"3g" 等
  console.log(navigator.connection.downlink);      // 下行带宽（Mbps）
  console.log(navigator.connection.saveData);      // 用户是否开启省流模式

  navigator.connection.addEventListener('change', () => {
    console.log('网络状况变化:', navigator.connection.effectiveType);
  });
}
```

## 12.3 地理定位

```javascript
if ('geolocation' in navigator) {
  navigator.geolocation.getCurrentPosition(
    (position) => {
      console.log('纬度:', position.coords.latitude);
      console.log('经度:', position.coords.longitude);
      console.log('精度:', position.coords.accuracy, '米');
    },
    (error) => {
      switch (error.code) {
        case error.PERMISSION_DENIED:
          console.log('用户拒绝了定位请求');
          break;
        case error.POSITION_UNAVAILABLE:
          console.log('定位信息不可用');
          break;
        case error.TIMEOUT:
          console.log('定位请求超时');
          break;
      }
    },
    {
      enableHighAccuracy: true,
      timeout: 5000,
      maximumAge: 0
    }
  );

  // 持续监听位置变化
  const watchId = navigator.geolocation.watchPosition(callback);
  navigator.geolocation.clearWatch(watchId); // 停止监听
}
```

## 12.4 剪贴板 API

```javascript
// 写入剪贴板
async function copyText(text) {
  try {
    await navigator.clipboard.writeText(text);
    console.log('已复制');
  } catch (err) {
    console.error('复制失败:', err);
  }
}

// 读取剪贴板
async function pasteText() {
  try {
    const text = await navigator.clipboard.readText();
    console.log('剪贴板内容:', text);
  } catch (err) {
    console.error('读取失败:', err);
  }
}
```

## 12.5 特性检测（最佳实践）

与其根据浏览器类型判断功能是否可用，不如直接检测功能本身：

```javascript
// ✅ 特性检测
if ('IntersectionObserver' in window) {
  // 使用 IntersectionObserver
}

if ('serviceWorker' in navigator) {
  // 注册 Service Worker
}

if (CSS.supports('display', 'grid')) {
  // 使用 CSS Grid
}

// ❌ 不要根据 userAgent 判断
if (navigator.userAgent.includes('Chrome')) { /* 不可靠 */ }
```

---

# 第十三章 现代 Observer API

浏览器提供了一组 Observer API，用于高效地**异步监听**特定变化，取代传统的轮询或事件方式。

## 13.1 IntersectionObserver — 可见性监听

监听元素是否进入/离开视口，常用于懒加载、无限滚动、曝光统计：

```javascript
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      console.log('元素可见:', entry.target);
      console.log('可见比例:', entry.intersectionRatio);

      // 懒加载图片
      const img = entry.target;
      img.src = img.dataset.src;
      observer.unobserve(img); // 加载后停止观察
    }
  });
}, {
  root: null,           // 视口作为容器（默认）
  rootMargin: '0px',    // 可以提前触发，如 '100px'
  threshold: [0, 0.5, 1] // 在 0%、50%、100% 可见时触发
});

// 观察元素
document.querySelectorAll('img[data-src]').forEach(img => {
  observer.observe(img);
});

// 停止所有观察
observer.disconnect();
```

## 13.2 MutationObserver — DOM 变化监听

监听 DOM 树的变化（属性修改、子节点增删、文本更改等）：

```javascript
const observer = new MutationObserver((mutations) => {
  mutations.forEach(mutation => {
    switch (mutation.type) {
      case 'childList':
        console.log('子节点变化:', mutation.addedNodes, mutation.removedNodes);
        break;
      case 'attributes':
        console.log('属性变化:', mutation.attributeName,
                    '新值:', mutation.target.getAttribute(mutation.attributeName));
        break;
      case 'characterData':
        console.log('文本变化:', mutation.target.data);
        break;
    }
  });
});

observer.observe(document.querySelector('#app'), {
  childList: true,        // 监听子节点增删
  attributes: true,       // 监听属性变化
  characterData: true,    // 监听文本内容变化
  subtree: true,          // 监听所有后代（不仅直接子节点）
  attributeFilter: ['class', 'style'], // 只监听特定属性
  attributeOldValue: true  // 记录旧值
});

observer.disconnect(); // 停止监听
```

## 13.3 ResizeObserver — 元素尺寸监听

监听元素尺寸变化（比 `window.resize` 更精确，可以监听任意元素）：

```javascript
const observer = new ResizeObserver((entries) => {
  for (const entry of entries) {
    const { width, height } = entry.contentRect;
    console.log(`元素尺寸: ${width} × ${height}`);

    if (width < 600) {
      entry.target.classList.add('compact');
    } else {
      entry.target.classList.remove('compact');
    }
  }
});

observer.observe(document.querySelector('.container'));
```

## 13.4 PerformanceObserver — 性能监听

```javascript
const observer = new PerformanceObserver((list) => {
  for (const entry of list.getEntries()) {
    if (entry.entryType === 'largest-contentful-paint') {
      console.log('LCP:', entry.startTime, 'ms');
    }
    if (entry.entryType === 'first-input') {
      console.log('FID:', entry.processingStart - entry.startTime, 'ms');
    }
  }
});

observer.observe({ type: 'largest-contentful-paint', buffered: true });
observer.observe({ type: 'first-input', buffered: true });
```

---

# 第十四章 客户端存储

## 14.1 存储方式对比

| 特性 | Cookie | localStorage | sessionStorage | IndexedDB |
|------|--------|--------------|----------------|-----------|
| 容量 | ~4KB | ~5-10MB | ~5-10MB | 数百 MB+ |
| 生命周期 | 可设置过期时间 | 永久（手动删除） | 标签页关闭清除 | 永久 |
| 发送到服务器 | 每次 HTTP 请求自动携带 | 否 | 否 | 否 |
| API 易用性 | 低 | 高 | 高 | 中等 |
| 同源策略 | 是 | 是 | 是（同标签页） | 是 |

## 14.2 localStorage 与 sessionStorage

两者 API 完全相同，区别仅在于生命周期：

```javascript
// 存储
localStorage.setItem('theme', 'dark');
localStorage.setItem('user', JSON.stringify({ name: '张三', age: 25 }));

// 读取
const theme = localStorage.getItem('theme');           // "dark"
const user = JSON.parse(localStorage.getItem('user')); // { name: '张三', age: 25 }

// 删除
localStorage.removeItem('theme');

// 清除所有
localStorage.clear();

// 获取存储项数量和键名
localStorage.length;     // 数量
localStorage.key(0);     // 第一个键名
```

> **注意：** `localStorage` 只能存储字符串。对象需要通过 `JSON.stringify()` 和 `JSON.parse()` 转换。

## 14.3 storage 事件

当 localStorage 在**其他标签页**中被修改时触发（同源）：

```javascript
window.addEventListener('storage', (event) => {
  console.log('键:', event.key);
  console.log('旧值:', event.oldValue);
  console.log('新值:', event.newValue);
  console.log('来源:', event.url);
  console.log('存储区:', event.storageArea);
});
```

这可以用于实现跨标签页通信。

## 14.4 结构化存储：IndexedDB 简介

IndexedDB 是浏览器内置的完整数据库系统，适合存储大量结构化数据：

```javascript
const request = indexedDB.open('MyDatabase', 1);

request.onupgradeneeded = (event) => {
  const db = event.target.result;
  const store = db.createObjectStore('users', { keyPath: 'id', autoIncrement: true });
  store.createIndex('name', 'name', { unique: false });
};

request.onsuccess = (event) => {
  const db = event.target.result;

  // 写入数据
  const tx = db.transaction('users', 'readwrite');
  tx.objectStore('users').add({ name: '张三', email: 'zhangsan@example.com' });

  // 读取数据
  const readTx = db.transaction('users', 'readonly');
  const getRequest = readTx.objectStore('users').get(1);
  getRequest.onsuccess = () => {
    console.log(getRequest.result);
  };
};
```

---

# 第十五章 网络请求：Fetch API

## 15.1 基本用法

`fetch()` 是现代的网络请求 API，基于 Promise，取代了旧的 `XMLHttpRequest`：

```javascript
// 基本 GET 请求
const response = await fetch('https://api.example.com/users');
const data = await response.json();

// 完整写法（带错误处理）
try {
  const response = await fetch('/api/users');

  if (!response.ok) {
    throw new Error(`HTTP ${response.status}: ${response.statusText}`);
  }

  const data = await response.json();
  console.log(data);
} catch (error) {
  console.error('请求失败:', error);
}
```

> **重要：** `fetch` 只在网络错误时 reject，HTTP 错误状态码（如 404、500）不会 reject，需要手动检查 `response.ok`。

## 15.2 POST 请求

```javascript
// 发送 JSON
const response = await fetch('/api/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    name: '张三',
    email: 'zhangsan@example.com'
  })
});

// 发送表单数据
const formData = new FormData(document.querySelector('form'));
const response = await fetch('/api/upload', {
  method: 'POST',
  body: formData  // 不需要手动设置 Content-Type
});
```

## 15.3 请求配置

```javascript
const response = await fetch(url, {
  method: 'GET',                 // GET, POST, PUT, DELETE, PATCH...
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer token123'
  },
  body: JSON.stringify(data),    // 请求体（GET/HEAD 不能有 body）
  mode: 'cors',                  // cors, no-cors, same-origin
  credentials: 'same-origin',   // include, same-origin, omit
  cache: 'default',             // default, no-cache, reload, force-cache
  signal: controller.signal     // AbortController 的信号
});
```

## 15.4 取消请求

```javascript
const controller = new AbortController();

// 超时自动取消
const timeoutId = setTimeout(() => controller.abort(), 5000);

try {
  const response = await fetch('/api/data', {
    signal: controller.signal
  });
  clearTimeout(timeoutId);
  const data = await response.json();
} catch (error) {
  if (error.name === 'AbortError') {
    console.log('请求被取消');
  } else {
    throw error;
  }
}

// 也可以手动取消
controller.abort();
```

## 15.5 响应处理

```javascript
const response = await fetch(url);

// 响应元数据
response.status;       // 200
response.statusText;   // "OK"
response.ok;           // true（状态码 200-299）
response.headers;      // Headers 对象
response.url;          // 最终 URL（可能经过重定向）
response.redirected;   // 是否经过重定向

// 读取响应体（只能读取一次）
const json = await response.json();       // 解析为 JSON
const text = await response.text();       // 解析为文本
const blob = await response.blob();       // 解析为 Blob
const buffer = await response.arrayBuffer(); // 解析为 ArrayBuffer
const formData = await response.formData();  // 解析为 FormData
```

---

> **参考资料：**
> - [MDN Web Docs — Web API 参考](https://developer.mozilla.org/zh-CN/docs/Web/API)
> - [MDN — DOM 概述](https://developer.mozilla.org/zh-CN/docs/Web/API/Document_Object_Model)
> - [MDN — 事件参考](https://developer.mozilla.org/zh-CN/docs/Web/Events)
> - [WHATWG HTML 规范](https://html.spec.whatwg.org/)
> - [DOM & BOM Revisited — fedknu.com](https://medium.com/@fknussel/dom-bom-revisited-cf6124e2a816)
