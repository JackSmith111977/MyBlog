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








