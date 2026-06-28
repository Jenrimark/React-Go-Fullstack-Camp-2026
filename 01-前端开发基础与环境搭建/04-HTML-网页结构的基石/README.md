# 现代 HTML 参考教程
> **基于 [MDN Web Docs](https://developer.mozilla.org/zh-CN/docs/Learn_web_development/Core/Structuring_content) 编写**
> 从零开始，系统掌握 HTML —— 构建 Web 的基石。

---

# 第一章 HTML 是什么？

**HTML**（HyperText Markup Language，超文本标记语言）是一种 *标记语言*，用于告诉浏览器如何组织网页内容。它不是编程语言，而是由一系列**元素**组成的结构描述语言——你用这些元素来包裹内容的不同部分，使其以特定方式显示或运作。

```html
<p>这是一段文本。</p>
```

上面的 `<p>` 元素告诉浏览器：这段文字是一个「段落」。

## 关键概念

| 概念 | 说明 |
|------|------|
| **标记语言** | 用标签给文本添加结构和含义，而非执行逻辑运算 |
| **超文本** | 通过链接将文档与文档连接在一起，构成互联的网络 |
| **HTML 文档** | 扩展名为 `.html` 的文本文件，通常入口文件命名为 `index.html` |

> 💡 **提示**：HTML 标签不区分大小写，`<P>` 和 `<p>` 均有效，但最佳实践是**全部使用小写**。

---

# 第二章 HTML 元素剖析

## 2.1 元素的组成

一个完整的 HTML 元素包含三个部分：

```
┌─────────────┐   ┌──────────┐   ┌──────────────┐
│  开始标签    │ → │   内容    │ → │   结束标签    │
│  <p>        │   │ 这是文本  │   │   </p>       │
└─────────────┘   └──────────┘   └──────────────┘
```

```html
<p>我的猫咪脾气很暴躁</p>
```

- **开始标签**（Opening Tag）：`<p>` —— 标志元素的开始
- **内容**（Content）：`我的猫咪脾气很暴躁` —— 元素包裹的实际内容
- **结束标签**（Closing Tag）：`</p>` —— 标志元素的结束

## 2.2 嵌套元素

元素可以放在其他元素内部，称为**嵌套**：

```html
<p>我的猫咪脾气<strong>很</strong>暴躁。</p>
```

⚠️ **嵌套必须正确闭合** —— 先开后关，后开先关：

```html
<!-- ✅ 正确 -->
<p>这是<strong>重要</strong>内容</p>

<!-- ❌ 错误 —— 标签交叉重叠 -->
<p>这是<strong>重要内容</p></strong>
```

## 2.3 空元素（Void Elements）

有些元素没有内容也没有结束标签，它们被称为**空元素**：

```html
<img src="photo.jpg" alt="一张照片" />
<br />
<hr />
<input type="text" />
```

> 空元素末尾的 `/` 是可选的，写 `<br>` 和 `<br />` 都可以。

---

# 第三章 属性

属性为元素提供额外信息，写在开始标签中：

```html
<a href="https://www.mozilla.org" title="Mozilla 首页">访问 Mozilla</a>
```

## 3.1 属性的语法

```
属性名="属性值"
```

规则：
- 属性名与元素名之间用**空格**分隔
- 多个属性之间也用**空格**分隔
- 属性值用**引号**包裹（推荐双引号）

```html
<img src="images/logo.png" alt="公司标识" width="200" height="100" />
```

## 3.2 布尔属性

有些属性不需要值，只要出现就表示 `true`：

```html
<!-- 禁用输入框 -->
<input type="text" disabled />

<!-- 未禁用的输入框 -->
<input type="text" />
```

## 3.3 引号的使用

```html
<!-- ✅ 双引号（推荐） -->
<a href="https://example.com">链接</a>

<!-- ✅ 单引号（也可以） -->
<a href='https://example.com'>链接</a>

<!-- ❌ 不加引号（可能出错） -->
<a href=https://example.com title=示例页面>链接</a>
```

如果属性值中包含引号，可以用另一种引号包裹：

```html
<a href="https://example.com" title="一个'有趣'的页面">链接</a>
```

---

# 第四章 HTML 文档结构

一个完整的 HTML 页面骨架如下：

```html
<!DOCTYPE html>
<html lang="zh-CN">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>我的网页</title>
  </head>
  <body>
    <h1>欢迎来到我的网页</h1>
    <p>这是页面内容。</p>
  </body>
</html>
```

## 逐行解析

| 代码 | 说明 |
|------|------|
| `<!DOCTYPE html>` | 文档类型声明，告诉浏览器使用 HTML5 标准渲染 |
| `<html lang="zh-CN">` | 根元素，`lang` 属性声明页面语言 |
| `<head>` | 文档头部，包含元数据（不直接显示在页面上） |
| `<meta charset="utf-8" />` | 设置字符编码为 UTF-8，支持中文等多语言字符 |
| `<meta name="viewport" ...>` | 响应式设计必需，让页面适配移动设备 |
| `<title>` | 页面标题，显示在浏览器标签页和搜索结果中 |
| `<body>` | 文档主体，包含所有用户可见的内容 |

---

# 第五章 标题与段落

标题和段落是 HTML 中最基础也最重要的文本结构元素。

## 5.1 六级标题

HTML 提供了 `<h1>` 到 `<h6>` 共六级标题，级别依次递减：

```html
<h1>一级标题（页面主标题）</h1>
<h2>二级标题</h2>
<h3>三级标题</h3>
<h4>四级标题</h4>
<h5>五级标题</h5>
<h6>六级标题</h6>
```

## 5.2 段落

用 `<p>` 元素表示一个段落：

```html
<p>话说天下大势，分久必合，合久必分。</p>
<p>周末七国分争，并入于秦。及秦灭之后，楚、汉分争，又并入于汉。</p>
```

## 5.3 标题使用最佳实践

```html
<!-- ✅ 正确的标题层级 -->
<h1>三国演义</h1>

  <h2>第一回 宴桃园豪杰三结义</h2>
    <p>话说天下大势，分久必合，合久必分……</p>

    <h3>却说张飞</h3>
      <p>却说张飞饮了数杯闷酒……</p>

  <h2>第二回 张翼德怒鞭督邮</h2>
    <p>且说董卓字仲颖……</p>
```

**标题使用原则：**

1. 每页**只用一次** `<h1>`（页面主标题）
2. **不要跳级**（如 `<h1>` 之后直接用 `<h4>`）
3. 每页**不超过三个层级**为宜
4. **不要**为了字号大小而选用标题级别——那是 CSS 的工作

## 5.4 为什么结构化很重要？

- **无障碍**：屏幕阅读器靠标题为视障用户导航内容
- **SEO**：搜索引擎依据标题的关键词理解页面内容
- **可读性**：用户快速浏览时依靠标题定位感兴趣的内容
- **可维护性**：CSS 和 JavaScript 需要结构化的元素作为选择器

---

# 第六章 强调与重要性

## 6.1 强调 `<em>`

用 `<em>` 表示语气上的**强调**（浏览器默认渲染为斜体）：

```html
<p>我很<em>庆幸</em>你没有<em>迟到</em>。</p>
```

> 不要仅为了获得斜体效果而使用 `<em>`，纯样式需求应使用 CSS 的 `font-style: italic`。

## 6.2 着重强调 `<strong>`

用 `<strong>` 表示内容的**重要性**（浏览器默认渲染为粗体）：

```html
<p>这杯液体<strong>毒性很大</strong>。</p>
<p>千万<strong>不要</strong>迟到！</p>
```

## 6.3 嵌套使用

`<em>` 和 `<strong>` 可以相互嵌套：

```html
<p>这杯液体<strong>毒性很大</strong>——如果喝了它，你<strong>可能<em>会死</em></strong>。</p>
```

## 6.4 `<b>`、`<i>`、`<u>` 的现代语义

这些标签在 HTML5 中被赋予了新的语义角色：

| 元素 | 现代用途 | 示例 |
|------|----------|------|
| `<i>` | 外语词汇、技术术语、思想 | `<i lang="fr">soupe à l'oignon</i>` |
| `<b>` | 关键词、产品名称 | `根据<b>语义</b>意义来使用元素` |
| `<u>` | 专有名词、拼写错误标注 | `我会改掉写<u>措字</u>的毛病` |

> ⚠️ 人们通常将下划线和超链接联系在一起，所以在 Web 中谨慎使用 `<u>`。

---

# 第七章 列表

## 7.1 无序列表 `<ul>`

适用于**顺序无关**的列表项：

```html
<ul>
  <li>豆浆</li>
  <li>油条</li>
  <li>豆汁</li>
  <li>焦圈</li>
</ul>
```

渲染效果：
- 豆浆
- 油条
- 豆汁
- 焦圈

## 7.2 有序列表 `<ol>`

适用于**有先后顺序**的列表项：

```html
<ol>
  <li>沿这条路走到头</li>
  <li>右转</li>
  <li>直行穿过第一个十字路口</li>
  <li>在第三个十字路口处左转</li>
  <li>学校在你的右手边</li>
</ol>
```

渲染效果：
1. 沿这条路走到头
2. 右转
3. 直行穿过第一个十字路口
4. 在第三个十字路口处左转
5. 学校在你的右手边

## 7.3 嵌套列表

列表可以相互嵌套：

```html
<ol>
  <li>准备食材</li>
  <li>烹饪步骤
    <ul>
      <li>正宗川菜做法：加入花生米翻炒起锅</li>
      <li>北方做法：加入黄瓜丁、胡萝卜丁和花生米</li>
    </ul>
  </li>
  <li>装盘上桌</li>
</ol>
```

## 7.4 描述列表 `<dl>`

适用于**术语和定义**的对应关系：

```html
<dl>
  <dt>内心独白</dt>
  <dd>戏剧中，某个角色对自己的内心活动进行念白，只面向观众。</dd>

  <dt>旁白</dt>
  <dd>场景之外的补充注释念白，只面向观众。</dd>
  <dd>写作中，与当前主题相关但不适于直接置于主线中的内容。</dd>
</dl>
```

> 一个术语 `<dt>` 可以对应**多个**描述 `<dd>`。

---

# 第八章 构建文档 —— 语义化布局

## 8.1 网页的典型区域

```
┌─────────────────────────────────────────┐
│                 header                   │  ← 页眉（标题、Logo）
├─────────────────────────────────────────┤
│                  nav                     │  ← 导航栏
├────────────────────────┬────────────────┤
│                        │                │
│        main            │    aside       │  ← 主内容 + 侧边栏
│   ┌──────────────┐     │                │
│   │   article    │     │                │
│   │  ┌────────┐  │     │                │
│   │  │section │  │     │                │
│   │  └────────┘  │     │                │
│   └──────────────┘     │                │
├────────────────────────┴────────────────┤
│                 footer                   │  ← 页脚
└─────────────────────────────────────────┘
```

## 8.2 语义化结构元素

| 元素 | 用途 |
|------|------|
| `<header>` | 页眉或区块头部，包含标题、Logo、导航等 |
| `<nav>` | 主导航区域 |
| `<main>` | 页面主内容，**每页仅使用一次** |
| `<article>` | 独立的文章内容（如博客文章） |
| `<section>` | 文档中按功能划分的区段 |
| `<aside>` | 与主内容间接相关的辅助信息（侧边栏） |
| `<footer>` | 页脚，包含版权、联系方式等 |

## 8.3 完整示例

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="utf-8" />
  <title>我的站点</title>
</head>
<body>

  <header>
    <h1>我的站点</h1>
  </header>

  <nav>
    <ul>
      <li><a href="/">主页</a></li>
      <li><a href="/about">关于</a></li>
      <li><a href="/contact">联系</a></li>
    </ul>
  </nav>

  <main>
    <article>
      <h2>文章标题</h2>
      <p>文章正文内容……</p>

      <section>
        <h3>子章节</h3>
        <p>更多内容……</p>
      </section>
    </article>

    <aside>
      <h2>相关链接</h2>
      <ul>
        <li><a href="#">推荐阅读 1</a></li>
        <li><a href="#">推荐阅读 2</a></li>
      </ul>
    </aside>
  </main>

  <footer>
    <p>&copy; 2026 我的站点. 保留所有权利.</p>
  </footer>

</body>
</html>
```

## 8.4 无语义包装器

当没有合适的语义元素时，使用：

- `<div>` —— 块级无语义容器
- `<span>` —— 行内无语义容器

```html
<div class="shopping-cart">
  <h2>购物车</h2>
  <p>总消费：¥237.89</p>
</div>

<p>国王在<span class="note">凌晨 1 点</span>才回房间。</p>
```

> ⚠️ 不要滥用 `<div>`。优先使用语义化元素（`<article>`、`<section>`、`<nav>` 等），只在没有更好选择时才用 `<div>`。

## 8.5 换行与水平线

```html
<!-- 换行：用于诗歌、地址等短行结构 -->
<p>
  床前明月光，<br />
  疑是地上霜。<br />
  举头望明月，<br />
  低头思故乡。
</p>

<!-- 水平线：表示主题的转换 -->
<p>第一段故事……</p>
<hr />
<p>另一段故事……</p>
```

---

# 第九章 创建超链接

超链接是互联网的灵魂——让文档与文档之间实现互联。

## 9.1 基本链接

使用 `<a>` 元素创建链接，`href` 属性指定目标地址：

```html
<a href="https://www.mozilla.org/zh-CN/">访问 Mozilla</a>
```

## 9.2 链接类型

**块级链接**——任何内容都可以变成链接：

```html
<a href="/about">
  <h2>关于我们</h2>
  <p>点击了解更多信息</p>
</a>
```

**图片链接**：

```html
<a href="https://www.mozilla.org">
  <img src="mozilla-logo.png" alt="Mozilla 首页" />
</a>
```

## 9.3 URL 与路径

| 路径类型 | 示例 | 说明 |
|----------|------|------|
| 绝对 URL | `https://www.example.com/page.html` | 完整地址，包含协议和域名 |
| 相对路径（同级） | `contact.html` | 同一目录下的文件 |
| 相对路径（子目录） | `projects/index.html` | 进入子目录 |
| 相对路径（上级） | `../pdfs/doc.pdf` | `..` 返回上一级目录 |

## 9.4 文档片段（锚点链接）

链接到页面中的特定位置：

```html
<!-- 步骤 1：给目标元素添加 id -->
<h2 id="contact">联系方式</h2>

<!-- 步骤 2：链接到该 id -->
<a href="#contact">跳转到联系方式</a>

<!-- 也可以链接到其他页面的特定位置 -->
<a href="about.html#team">关于页面的团队部分</a>
```

## 9.5 电子邮件链接

```html
<a href="mailto:hello@example.com">发送邮件</a>

<!-- 带主题和正文 -->
<a href="mailto:hello@example.com?subject=你好&body=邮件内容">联系我们</a>
```

## 9.6 下载链接

```html
<a href="files/report.pdf" download="年度报告.pdf">
  下载年度报告（PDF, 4MB）
</a>
```

## 9.7 链接最佳实践

```html
<!-- ✅ 好的链接文本 -->
<a href="https://www.mozilla.org/firefox/">下载 Firefox</a>

<!-- ❌ 差的链接文本 -->
<a href="https://www.mozilla.org/firefox/">点击这里</a>下载 Firefox
```

**原则：**

- 链接文本要有描述性，不要使用「点击这里」
- 不要在链接文本中写「链接到……」
- 保持链接文本简短
- 链接到非 HTML 资源时注明文件类型和大小

---

# 第十章 图片

## 10.1 嵌入图片

使用 `<img>` 元素，它是空元素，必需属性为 `src` 和 `alt`：

```html
<img src="images/dinosaur.jpg" alt="恐龙骨架的头部和躯干" />
```

## 10.2 备选文本 `alt`

`alt` 属性在图片无法显示时提供替代文本：

```html
<img
  src="images/cat.jpg"
  alt="一只橘色的猫咪蜷缩在沙发上" />
```

**`alt` 编写原则：**

| 场景 | alt 值 |
|------|--------|
| 有信息含义的图片 | 简明描述图片内容 |
| 纯装饰性图片 | `alt=""` (空字符串) |
| 已有文字说明的图片 | `alt=""` 避免冗余 |
| 作为链接的图片 | 描述链接目标 |

## 10.3 图片尺寸

始终设置 `width` 和 `height`，避免页面加载时内容跳动：

```html
<img
  src="images/photo.jpg"
  alt="风景照片"
  width="800"
  height="600" />
```

> 如需调整显示大小，使用 CSS 而非修改 HTML 属性。

## 10.4 图片说明文字 `<figure>`

使用 `<figure>` 和 `<figcaption>` 为图片添加语义化的说明文字：

```html
<figure>
  <img
    src="images/dinosaur.jpg"
    alt="恐龙骨架"
    width="400"
    height="341" />
  <figcaption>曼彻斯特大学博物馆中展出的霸王龙骨架。</figcaption>
</figure>
```

> `<figure>` 不仅限于图片，也可用于代码块、图表、表格等独立内容单元。

## 10.5 HTML 图片 vs CSS 背景图

| | HTML `<img>` | CSS `background-image` |
|--|-------------|----------------------|
| **用途** | 有内容含义的图片 | 纯装饰性图片 |
| **无障碍** | 支持 `alt` 文本 | 无法提供替代文本 |
| **SEO** | 可被搜索引擎索引 | 不可索引 |
| **选择建议** | 内容图片用 HTML | 装饰用 CSS |

---

# 第十一章 视频与音频

## 11.1 嵌入视频

```html
<video src="video/my-video.mp4" controls width="640" height="360">
  <p>你的浏览器不支持 HTML 视频，请<a href="video/my-video.mp4">下载视频</a>。</p>
</video>
```

常用属性：

| 属性 | 说明 |
|------|------|
| `controls` | 显示播放控件 |
| `autoplay` | 自动播放（慎用） |
| `loop` | 循环播放 |
| `muted` | 静音 |
| `poster` | 视频封面图 |
| `preload` | 预加载策略：`none` / `metadata` / `auto` |

## 11.2 多格式支持

不同浏览器支持的视频格式不同，使用 `<source>` 提供多种格式：

```html
<video controls width="640" height="360">
  <source src="video/my-video.webm" type="video/webm" />
  <source src="video/my-video.mp4" type="video/mp4" />
  <p>你的浏览器不支持 HTML 视频。</p>
</video>
```

## 11.3 嵌入音频

`<audio>` 的使用方式与 `<video>` 类似，但不支持 `width`、`height`、`poster`：

```html
<audio controls>
  <source src="audio/song.mp3" type="audio/mpeg" />
  <source src="audio/song.ogg" type="audio/ogg" />
  <p>你的浏览器不支持 HTML 音频。</p>
</audio>
```

## 11.4 字幕与副标题

使用 `<track>` 元素为视频添加字幕（WebVTT 格式）：

```html
<video controls>
  <source src="video/movie.mp4" type="video/mp4" />
  <track
    src="subtitles/zh.vtt"
    kind="subtitles"
    srclang="zh"
    label="中文"
    default />
</video>
```

---

# 第十二章 表格

## 12.1 基础表格

```html
<table>
  <tr>
    <th>姓名</th>
    <th>年龄</th>
    <th>城市</th>
  </tr>
  <tr>
    <td>张三</td>
    <td>28</td>
    <td>北京</td>
  </tr>
  <tr>
    <td>李四</td>
    <td>32</td>
    <td>上海</td>
  </tr>
</table>
```

| 元素 | 说明 |
|------|------|
| `<table>` | 表格容器 |
| `<tr>` | 表格行（table row） |
| `<th>` | 表头单元格（table header） |
| `<td>` | 数据单元格（table data） |

## 12.2 表格结构元素

```html
<table>
  <caption>2026 年度销售数据</caption>

  <thead>
    <tr>
      <th>季度</th>
      <th>销售额</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>Q1</td>
      <td>¥120,000</td>
    </tr>
    <tr>
      <td>Q2</td>
      <td>¥150,000</td>
    </tr>
  </tbody>

  <tfoot>
    <tr>
      <td>合计</td>
      <td>¥270,000</td>
    </tr>
  </tfoot>
</table>
```

## 12.3 合并单元格

```html
<!-- 跨列合并 -->
<td colspan="2">跨两列的内容</td>

<!-- 跨行合并 -->
<td rowspan="3">跨三行的内容</td>
```

## 12.4 表格无障碍

- 使用 `<caption>` 为表格提供说明
- 使用 `<th>` 标记表头，并搭配 `scope` 属性：

```html
<th scope="col">列标题</th>
<th scope="row">行标题</th>
```

---

# 第十三章 表单与按钮

## 13.1 基本表单结构

```html
<form action="/submit" method="post">
  <label for="name">姓名：</label>
  <input type="text" id="name" name="name" required />

  <label for="email">邮箱：</label>
  <input type="email" id="email" name="email" required />

  <button type="submit">提交</button>
</form>
```

## 13.2 常用输入类型

| `type` 值 | 说明 | 示例 |
|-----------|------|------|
| `text` | 单行文本 | 姓名、地址 |
| `email` | 邮箱（自带验证） | 电子邮件 |
| `password` | 密码（隐藏输入） | 登录密码 |
| `number` | 数字 | 年龄、数量 |
| `tel` | 电话号码 | 手机号 |
| `url` | 网址 | 个人网站 |
| `date` | 日期选择器 | 出生日期 |
| `checkbox` | 复选框 | 兴趣爱好 |
| `radio` | 单选按钮 | 性别选择 |
| `file` | 文件上传 | 头像图片 |
| `range` | 滑块 | 音量调节 |
| `color` | 颜色选择器 | 主题颜色 |
| `search` | 搜索框 | 站内搜索 |

## 13.3 其他表单元素

**文本域：**

```html
<label for="message">留言：</label>
<textarea id="message" name="message" rows="5" cols="40"></textarea>
```

**下拉选择：**

```html
<label for="city">城市：</label>
<select id="city" name="city">
  <option value="">-- 请选择 --</option>
  <option value="beijing">北京</option>
  <option value="shanghai">上海</option>
  <option value="guangzhou">广州</option>
</select>
```

**字段分组：**

```html
<fieldset>
  <legend>个人信息</legend>
  <label for="fname">名字：</label>
  <input type="text" id="fname" name="fname" />
  <label for="lname">姓氏：</label>
  <input type="text" id="lname" name="lname" />
</fieldset>
```

## 13.4 按钮类型

```html
<!-- 提交表单 -->
<button type="submit">提交</button>

<!-- 重置表单 -->
<button type="reset">重置</button>

<!-- 普通按钮（搭配 JavaScript 使用） -->
<button type="button">点击我</button>
```

## 13.5 表单验证属性

| 属性 | 说明 |
|------|------|
| `required` | 必填字段 |
| `minlength` / `maxlength` | 文本最小/最大长度 |
| `min` / `max` | 数值最小/最大值 |
| `pattern` | 正则表达式验证 |
| `placeholder` | 占位提示文本 |

```html
<input
  type="text"
  name="username"
  required
  minlength="3"
  maxlength="20"
  pattern="[a-zA-Z0-9]+"
  placeholder="请输入用户名" />
```

---

# 第十四章 特殊字符与注释

## 14.1 字符引用

HTML 中 `<`、`>`、`"`、`'`、`&` 是特殊字符，需要用字符引用来表示：

| 字符 | 字符引用 | 含义 |
|------|---------|------|
| `<` | `&lt;` | 小于号（less than） |
| `>` | `&gt;` | 大于号（greater than） |
| `"` | `&quot;` | 双引号（quotation） |
| `'` | `&apos;` | 单引号（apostrophe） |
| `&` | `&amp;` | 与号（ampersand） |

```html
<!-- 在页面中显示 HTML 标签文本 -->
<p>HTML 中用 &lt;p&gt; 来定义段落元素。</p>
```

## 14.2 HTML 注释

注释不会被浏览器渲染，用于在代码中留下说明：

```html
<!-- 这是一条注释，浏览器不会显示它 -->
<p>这段文字会被显示。</p>

<!-- <p>这段文字被注释掉了，不会显示。</p> -->
```

## 14.3 HTML 中的空白

无论你在代码中写了多少空格和换行，HTML 解析器都会将连续的空白压缩为单个空格：

```html
<!-- 这两种写法效果相同 -->
<p>狗狗很 呆 萌。</p>

<p>狗狗很
    呆
        萌。</p>
```

---

# 第十五章 HTML 调试与最佳实践

## 15.1 常见错误

| 错误 | 示例 | 修正 |
|------|------|------|
| 未闭合标签 | `<p>文本` | `<p>文本</p>` |
| 标签嵌套错误 | `<p><em>文本</p></em>` | `<p><em>文本</em></p>` |
| 属性值缺少引号 | `<a href=url>` | `<a href="url">` |
| 缺少 `alt` 属性 | `<img src="a.jpg">` | `<img src="a.jpg" alt="描述">` |
| 重复的 `id` | 两个元素用同一 `id` | 每个 `id` 必须唯一 |

## 15.2 验证工具

使用 W3C 的 [HTML 验证器](https://validator.w3.org/) 检查你的 HTML 是否符合标准。

## 15.3 最佳实践清单

- [x] 始终声明 `<!DOCTYPE html>`
- [x] 设置 `<html lang="...">`，方便屏幕阅读器和搜索引擎
- [x] 使用 `<meta charset="utf-8" />`
- [x] 添加 `<meta name="viewport" ...>` 实现响应式
- [x] 为每张图片编写有意义的 `alt` 文本
- [x] 使用语义化元素而非通用 `<div>`
- [x] 标题层级合理，不跳级
- [x] 链接文本具有描述性
- [x] 表单控件搭配 `<label>` 使用
- [x] 代码缩进一致，保持可读性
- [x] 属性值使用双引号
- [x] 标签全部小写

---

# 参考资源

- [MDN Web Docs - 使用 HTML 构建 Web](https://developer.mozilla.org/zh-CN/docs/Learn_web_development/Core/Structuring_content)
- [MDN HTML 元素参考](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Reference/Elements)
- [W3C HTML 验证器](https://validator.w3.org/)
- [HTML Living Standard](https://html.spec.whatwg.org/)
