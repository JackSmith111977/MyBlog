---
title: HTML笔记
date: 2025-11-22 21:45:00
tags: html
cover: https://kei-blog.oss-cn-beijing.aliyuncs.com/asset/115945063_p0-cut.jpg
categories: 
    - 编程语言
---

# HTML笔记

本文是基于[MDN Web Docs](https://mdn.org.cn), 同时结合本人的学习整理而成的笔记

## 目录

- [HTML笔记](#html笔记)
  - [目录](#目录)
  - [HTML 基本语法](#html-基本语法)
    - [HTML 文档结构](#html-文档结构)
    - [网络元数据](#网络元数据)
      - [添加标题](#添加标题)
      - [meta元数据](#meta元数据)
      - [添加自定义图标](#添加自定义图标)
      - [将 CSS 和 JavaScript 应用于 HTML](#将-css-和-javascript-应用于-html)
      - [设置文档的主要语言](#设置文档的主要语言)
    - [标题和段落](#标题和段落)
    - [强调和重要性](#强调和重要性)
    - [列表](#列表)
    - [高级文本功能](#高级文本功能)
  - [构建文档](#构建文档)
    - [文档的基本组成部分](#文档的基本组成部分)
    - [HTML 布局元素细节](#html-布局元素细节)
      - [无语义包装器](#无语义包装器)
      - [换行与分隔线](#换行与分隔线)
  - [创建超链接](#创建超链接)
    - [链接的解析](#链接的解析)
    - [统一资源定位符（URL）与路径（path）](#统一资源定位符url与路径path)
    - [文档片段](#文档片段)
    - [绝对 URL 和相对 URL](#绝对-url-和相对-url)
    - [注意事项](#注意事项)
  - [HTML 中的图片](#html-中的图片)
    - [创建图片](#创建图片)
    - [宽度和高度](#宽度和高度)
    - [图像标题](#图像标题)
    - [网页上的其他图形](#网页上的其他图形)

## HTML 基本语法
[回到目录](#目录)

### HTML 文档结构
[回到目录](#目录)

~~~html
<!doctype html>
<html lang="en-US">
  <head>
    <meta charset="utf-8" />
    <title>My test page</title>
  </head>
  <body>
    <p>This is my page</p>
  </body>
</html>
~~~

* `<!doctype html>`: 文档类型声明，文档类型声明是一个历史遗留物，需要包含它才能让所有其他内容正常工作
* `<html></html>`: `<html>` 元素。此元素包裹页面上的所有内容。它有时被称为**根元素**
* `<head></head>`：`<head>` 元素。此元素充当您希望包含在 HTML 页面上的所有内容（不是页面将显示给访问者的内容）的容器。这包括在**搜索结果**中显示的关键字和页面描述、用于样式内容的 CSS、**字符集声明**等等
* `<meta charset="utf-8">`：`<meta>` 元素。此元素表示无法由其他 HTML 元相关元素表示的元数据，例如 `<base>`、`<link>`、`<script>`、`<style>` 或 `<title>`。**charset 属性**将文档的字符编码指定为 UTF-8，它包括绝大多数人类书写语言中的大多数字符。通过此设置，页面现在可以处理它可能包含的任何文本内容
* `<title></title>`：`<title>` 元素。这设置了**页面的标题**，即页面加载时浏览器选项卡中显示的标题。页面标题也用于在页面被收藏时**描述页面**
* `<body></body>`：`<body>` 元素。此元素包含页面上显示的所有内容，包括文本、图像、视频、游戏、可播放的音频轨道或任何其他内容

### 网络元数据
[回到目录](#目录)

#### 添加标题
[回到目录](#目录)

`<title>` 元素：页面的标题,表示**整个 HTML 文档**的标题（而不是文档的内容）;title 内容也会作为收藏页面时**建议的书签名**

#### meta元数据
[回到目录](#目录)

1. 指定字符编码

~~~html
<meta charset="utf-8">
~~~

2. 添加作者和描述

~~~html
<meta name="author" content="Chris Mills" />
<meta
  name="description"
  content="The MDN Web Docs Learning Area aims to provide
complete beginners to the Web with all they need to know to get
started with developing websites and applications." />
~~~

* **name**: 指定了 meta 元素的类型；它包含什么类型的信息
* **content**: 指定实际的元内容
* **指定作者**在很多方面都有好处：如果你对内容有任何疑问并希望**联系作者**，能够了解是谁撰写了页面是很有用的
* 指定包含与你的页面内容相关的**关键词**的描述很有用，因为它有可能使你的页面在搜索引擎中执行的相关搜索中排名更高（此类活动被称为**搜索引擎优化**，或SEO）

#### 添加自定义图标
[回到目录](#目录)

在元数据中添加对**自定义图标的引用**，这些图标将在特定上下文中显示。其中最常用的是 favicon（“收藏夹图标”的缩写，指其在浏览器“收藏夹”或“书签”列表中的使用）

~~~html
<link rel="icon" href="favicon.ico" type="image/x-icon" />
~~~
* favicon以.ico格式保存（大多数也支持更常见的格式如.gif或.png的 favicon）

#### 将 CSS 和 JavaScript 应用于 HTML
[回到目录](#目录)

1. 添加 CSS
~~~html
<link rel="stylesheet" href="my-css-file.css" />
~~~
* `<link>`元素应始终放在文档的 head 部分。它有两个属性，rel="stylesheet"表示它是文档的**样式表**，href包含样式表文件的**路径**

2. 添加 JavaScript
~~~html
<script src="my-js-file.js" defer></script>
~~~
* `<script>`元素也应该放在 **head 部分**，并且应该包含一个src属性，其中包含你想要加载的 JavaScript 的**路径**，以及**defer**（一个布尔属性），它指示浏览器在页面完成解析 HTML 后加载 JavaScript。
* defer属性很有用，因为它保证在 **JavaScript 运行之前**所有 HTML 都已加载，这样你就不会因为 JavaScript 尝试访问页面上尚不存在的 HTML 元素而出现错误。

#### 设置文档的主要语言
[回到目录](#目录)

~~~html
<html lang="en-US">
  …
</html>
~~~


### 标题和段落
[回到目录](#目录)

1. 标题

`<h1>` - `<h6>`：标题元素，用于**定义 HTML 文档的标题**。`<h1>` 元素用于定义最大的标题，而`<h6>` 元素则用于定义最小的标题

2. 段落

`<p>`：段落元素，用于**定义 HTML 文档中的段落**

### 强调和重要性

1. 强调

`<em>`：强调元素，用于**定义 HTML 文档中的强调**, 除了使文档更有用之外，屏幕阅读器也能识别它们，并且可以配置为以不同的**语调**朗读它们

* 注意事项：浏览器默认将其样式设置为斜体，但您不应**仅为获得斜体样式**而使用此标签。要实现此目的，您可以使用 `<span>` 元素和一些 CSS，或者可能使用 `<i>` 元素

2. 重要性强调

`<strong>`：强调元素，用于**定义 HTML 文档中的重要性**, 除了使文档更有用之外，屏幕阅读器也能识别它们，并且可以配置为以不同的**语调**朗读它们

* 注意事项：浏览器默认将其样式设置为粗体，但您不应**仅为获得粗体样式**而使用此标签。要实现此目的，您可以使用 `<span>` 元素和一些 CSS，或者可能使用 `<b>` 元素
* 如果需要，您可以将 strong 和 em **嵌套**在一起

3. 粗体/斜体和下划线

只有在没有更合适的元素来传达传统上由粗体、斜体或下划线传达的含义时，才适合使用 `<b>`、`<i>` 或 `<u>`；而且通常都有更合适的元素。考虑 `<strong>`、`<em>`、`<mark>` 或` <span>` 是否更合适


* `<i>` 用于传达传统上由**斜体**传达的含义：外来词、分类名称、技术术语、想法……
* `<b>` 用于传达传统上由**粗体**传达的含义：关键词、产品名称、导语句……
* `<u>` 用于传达传统上由**下划线**传达的含义：专有名词、拼写错误……

### 列表

1. 无序列表

~~~html
<ul>
  <li>milk</li>
  <li>eggs</li>
  <li>bread</li>
  <li>hummus</li>
</ul>
~~~
* 要在 HTML 中创建此列表，我们首先将整个列表包装在 `<ul>`（无序列表）元素中。然后，我们将每个项目包装在 `<li>`（列表项）元素中

2. 有序列表

~~~html
<ol>
  <li>Drive to the end of the road</li>
  <li>Turn right</li>
  <li>Go straight across the first two roundabouts</li>
  <li>Turn left at the third roundabout</li>
  <li>The school is on your right, 300 meters up the road</li>
</ol>
~~~
* 标记结构与无序列表相同，只是你必须将列表项包装在 `<ol>` 元素中，而不是 `<ul>`
* 将一个列表**嵌套**在另一个列表内是完全可以的

3. 描述列表

~~~html
<dl>
  <dt>soliloquy</dt>
  <dd>
    In drama, where a character speaks to themselves, representing their inner
    thoughts or feelings and in the process relaying them to the audience (but
    not to other characters.)
  </dd>
  <dt>monologue</dt>
  <dd>
    In drama, where a character speaks their thoughts out loud to share them
    with the audience and any other characters present.
  </dd>
  <dt>aside</dt>
  <dd>
    In drama, where a character shares a comment only with the audience for
    humorous or dramatic effect. This is usually a feeling, thought, or piece of
    additional background information.
  </dd>
</dl>
~~~
* 描述列表使用不同于其他列表类型的包装器——`<dl>`；此外，每个术语都包装在一个 `<dt>`（描述术语）元素中，每个描述都包装在一个 `<dd>`（描述定义）元素中
* 浏览器默认样式会显示描述列表，其中描述相对于术语有一定程度的**缩进**

### 高级文本功能
[回到目录](#目录)

1. 块引用

~~~html
<blockquote
  cite="https://mdn.org.cn/en-US/docs/Web/HTML/Reference/Elements/blockquote">
  <p>
    The <strong>HTML <code>&lt;blockquote&gt;</code> Element</strong> (or
    <em>HTML Block Quotation Element</em>) indicates that the enclosed text is
    an extended quotation.
  </p>
</blockquote>
~~~
* 如果某个块级内容（无论是段落、多个段落、列表等）是引自其他地方的，您应该将其包装在`<blockquote>`元素中以表示这一点，并在cite属性中包含指向引用源的 URL
* 浏览器默认样式会将其渲染为**缩进**段落，以指示它是一个引用；引文上方的段落是为了演示这一点

2. 内联引用

~~~html
<q
  cite="https://mdn.org.cn/en-US/docs/Web/HTML/Reference/Elements/q">
  intended for short quotations that don't require paragraph breaks.
</q>
~~~
* 内联引用工作方式完全相同，只是它们使用`<q>`元素。例如，下面的标记包含一个来自 MDN `<q>`页面的引用
* 浏览器默认样式会将其渲染为用引号引起来的普通文本，以表示引用

3. 引文

~~~html
<p>
  According to the
  <a href="/en-US/docs/Web/HTML/Reference/Elements/blockquote">
    <cite>MDN blockquote page</cite></a>:
</p>

<blockquote
  cite="https://mdn.org.cn/en-US/docs/Web/HTML/Reference/Elements/blockquote">
  <p>
    The <strong>HTML <code>&lt;blockquote&gt;</code> Element</strong> (or
    <em>HTML Block Quotation Element</em>) indicates that the enclosed text is
    an extended quotation.
  </p>
</blockquote>

<p>
  The quote element — <code>&lt;q&gt;</code> — is
  <q cite="https://mdn.org.cn/en-US/docs/Web/HTML/Reference/Elements/q">
    intended for short quotations that don't require paragraph breaks.
  </q>
  — <a href="/en-US/docs/Web/HTML/Reference/Elements/q"><cite>MDN q page</cite></a>.
</p>
~~~
* cite属性的内容听起来很有用，但不幸的是，浏览器、屏幕阅读器等并没有对其进行太多处理
* 有一个`<cite>`元素，但它旨在包含被引用资源的标题，例如书名
* 可以通过`<a>`元素将`<cite>`元素与`<a>`元素一起使用，以创建一个指向引用资源的超链接

4. 缩写

~~~html
<p>
  We use <abbr>HTML</abbr>, Hypertext Markup Language, to structure our web
  documents.
</p>

<p>
  I think <abbr title="Reverend">Rev.</abbr> Green did it in the kitchen with
  the chainsaw.
</p>
~~~
* `<abbr>`——它用于包裹缩写词或首字母缩略词

5. 标记联系方式

~~~html
<address>Chris Mills, Manchester, The Grim North, UK</address>
~~~
* HTML 有一个用于标记联系方式的元素——`<address>`。它包裹着您的联系方式
* 它还可以包含更复杂的标记和其他形式的联系信息

6. 上标和下标

~~~html
<p>My birthday is on the 25<sup>th</sup> of May 2001.</p>
<p>
  Caffeine's chemical formula is
  C<sub>8</sub>H<sub>10</sub>N<sub>4</sub>O<sub>2</sub>.
</p>
<p>If x<sup>2</sup> is 9, x must equal 3 or -3.</p>
~~~
* `<sup>`——用于将内容放在文本的顶部
* `<sub>`——用于将内容放在文本的底部

7. 表示计算机代码

~~~html
<pre><code>const para = document.querySelector('p');

para.onclick = function() {
  alert('Owww, stop poking me!');
}</code></pre>

<p>
  You shouldn't use presentational elements like <code>&lt;font&gt;</code> and
  <code>&lt;center&gt;</code>.
</p>

<p>
  In the above JavaScript example, <var>para</var> represents a paragraph
  element.
</p>

<p>Select all the text with <kbd>Ctrl</kbd>/<kbd>Cmd</kbd> + <kbd>A</kbd>.</p>

<pre>$ <kbd>ping mozilla.org</kbd>
<samp>PING mozilla.org (63.245.215.20): 56 data bytes
64 bytes from 63.245.215.20: icmp_seq=0 ttl=40 time=158.233 ms</samp></pre>
~~~
* `<code>`：用于标记通用计算机代码片段。
* `<pre>`：用于保留空白（通常是代码块）——如果您在文本中使用缩进或多余的空白，浏览器会忽略它，您将不会在渲染页面上看到它。但是，如果您将文本包装在`<pre></pre>`标签中，您的空白将与您在文本编辑器中看到的一样被渲染。
* `<var>`：专门用于标记变量名。
* `<kbd>`：用于标记输入到计算机中的键盘（和其他类型的）输入。
* `<samp>`：用于标记计算机程序的输出。

8. 标记时间和日期

~~~html
<!-- Standard simple date -->
<time datetime="2016-01-20">20 January 2016</time>
<!-- Just year and month -->
<time datetime="2016-01">January 2016</time>
<!-- Just month and day -->
<time datetime="01-20">20 January</time>
<!-- Just time, hours and minutes -->
<time datetime="19:30">19:30</time>
<!-- You can do seconds and milliseconds too! -->
<time datetime="19:30:01.856">19:30:01.856</time>
<!-- Date and time -->
<time datetime="2016-01-20T19:30">7.30pm, 20 January 2016</time>
<!-- Date and time with timezone offset -->
<time datetime="2016-01-20T19:30+01:00">
  7.30pm, 20 January 2016 is 8.30pm in France
</time>
<!-- Calling out a specific week number -->
<time datetime="2016-W04">The fourth week of 2016</time>
~~~

## 构建文档
[回到目录](#目录)

### 文档的基本组成部分
[回到上一级](#构建文档)


* **页眉**：`<header>`。
* **导航栏**：`<nav>`。
* **主内容**：`<main>`。主内容中还可以有各种子内容区段，可用 `<article>`、`<section>` 和 `<div>` 等元素表示。
* **侧边栏**：`<aside>`。经常放置在 `<main>` 中。
* **页脚**：`<footer>`。

### HTML 布局元素细节
[回到上一级](#构建文档)

* `<main>` 存放本页面独有的内容。每个页面上**只能用一次** `<main>`，且直接位于 `<body>` 中。最好不要把它嵌套进其他元素。
* `<article>` 包围的内容即一篇文章，与页面其他部分无关（比如一篇博文）。
* `<section>` 与 `<article>` 类似，但 `<section>` 更适用于组织页面使其**按功能**（比如迷你地图、一组文章标题和摘要）分块。一般的最佳用法是：以标题作为开头；也可以把一篇 `<article>` 分成若干部分并分别置于不同的 `<section>` 中，也可以把一个区段 `<section>` 分成若干部分并分别置于不同的 `<article>` 中，取决于上下文。
* `<aside>` 包含的内容与主要内容没有直接关系，但可以提供与主要内容间接相关的**附加信息**（术语条目、作者简介、相关链接，等等）。
* `<header>` 是**简介形式**的内容。如果它是 `<body>` 的子元素，那么就是网站的**全局页眉**。如果它是 `<article>` 或`<section>` 的子元素，那么它是这些部分**特有的页眉**（此 `<header>` 非彼标题）。
* `<nav>` 包含页面**主导航功能**。其中不应包含二级链接等内容。
* `<footer>` 包含了页面的**页脚**部分。

#### 无语义包装器
[回到上一级](#html-布局元素细节)

* `<span>` 是一个**行级无语义元素**，最好只用于无法找到更好的语义元素来包含内容时，或者不想增加特定的含义时
* `<div>` 是一个块级无语义元素，应仅用于找不到更好的块级元素时，或者不想增加特定的意义时

#### 换行与分隔线
[回到上一级](#html-布局元素细节)

* `<br>` 可在段落中进行换行；`<br>` 是唯一能够生成多个短行结构（例如邮寄地址或诗歌）的元素
* `<hr>` 元素在文档中生成一条水平分割线，表示文本中主题的变化（例如话题或场景的改变）

## 创建超链接
[回到目录](#目录)

### 链接的解析
[回到上一级](#创建超链接)
* 通过将文本或其他内容包裹在 `<a>` 元素内，并给它一个包含网址的 **href** 属性（也称为超文本引用或目标，它将包含一个网址）来创建一个基本链接
* `<a>` 中可以包含块级元素，图片等内容
* **title 属性**：当鼠标悬停在超链接上时，会显示一个提示信息

### 统一资源定位符（URL）与路径（path）

[回到上一级](#创建超链接)

* URL：统一资源定位符（Uniform Resource Locator，URL）是一个定义了**在网络上的位置**的一个文本字符串
* path：路径（Path）是一个**文件或目录的**位置

### 文档片段
[回到上一级](#创建超链接)

超链接除了可以链接到文档外，也可以链接到 HTML 文档的特定部分（被称为**文档片段**）

* 首先，给要链接到的元素分配一个 id 属性
* 为了链接到那个特定的 id，要将它放在 URL 的末尾，并在前面包含井号（**#**）

### 绝对 URL 和相对 URL
[回到上一级](#创建超链接)

* **绝对 URL**：指向由其在 Web 上的绝对位置定义的位置，包括**协议和域名**
* **相对 URL**：指向与你链接的文件相关的位置

### 注意事项
[回到上一级](#创建超链接)

1. 链接到非 HTML 资源（如图片、视频、音频等）应该**明确标注说明**
2. 在下载链接中使用 download 属性来提供一个默认保存的文件名
3. 当需要电子邮件链接时可以使用 `<a>` 元素和 mailto: URL 协议实现

如：
~~~html
<a href="mailto:example@example.com">Send email</a>
~~~

## HTML 中的图片
[回到目录](#目录)

### 创建图片
[回到上一级](#HTML 中的图片)

* `<img>` 元素用于在 HTML 文档中嵌入图片，需要包含 **src 属性和 alt 属性**才能生效
* `<img>` 元素包含 **src 属性**，该属性包含图片的 URL，可以是绝对路径或相对路径
* `<img>` 元素包含 **alt 属性**，该属性包含图片的**替代文本**，它的值应该是图片的**文本描述**，用于在图片无法显示或者因为网速慢而加载缓慢的情况下使用

### 宽度和高度
[回到上一级](#html-中的图片)

你可以用 `width` 和 `height` 属性来指定你的图片的宽度和高度。它们的值是**不带单位的整数**，**以像素为单位**表示图像的宽度和高度

### 图像标题
[回到上一级](#html-中的图片)

类似于超链接，你可以通过给图片增加 title 属性来提供更多的信息，这会给我们一个**鼠标悬停提示**

### 网页上的其他图形
[回到上一级](#html-中的图片)

1. **Canvas**
    `<canvas>` 元素提供了使用 JavaScript 绘制 2D 图形的 API。
2. **SVG**
    你可以借助**可缩放矢量图形**（SVG）来使用线条、曲线和其他几何形状来渲染 2D 图形。借助矢量图形，你可以创建能够以任意尺寸清晰缩放的图像。
3. **WebGL**
    WebGL API 指南将帮助你入门 WebGL，这是用于 Web 的 3D 图形 API，可让你在 Web 内容中使用标准的 OpenGL ES。
4. **使用 HTML 音频和视频**
    与 `<img>` 类似，你可以使用 HTML 将 `<video>` 和 `<audio>` 嵌入到网页中，并控制其播放。
5. **WebRTC**
    WebRTC 中的 RTC 代表实时通信（Real-Time Communications），这是一种可以在浏览器客户端（对等方）之间进行音频/视频流和数据共享的技术。









