---
title: CSS笔记
date: 2025-11-22 21:45:00
tags: css
cover: https://kei-blog.oss-cn-beijing.aliyuncs.com/asset/115945063_p0-cut.jpg
categories: 
    - 编程语言
---

# CSS笔记

## 目录

- [CSS笔记](#css笔记)
  - [目录](#目录)
  - [盒子模型](#盒子模型)
    - [盒子大小](#盒子大小)
    - [外部显示类型](#外部显示类型)
      - [区块盒子](#区块盒子)
      - [行内盒子](#行内盒子)
    - [外边距、内边距和边框](#外边距内边距和边框)
      - [外边距](#外边距)
      - [边框](#边框)
      - [内边距](#内边距)
    - [使用 diplay: inline-block](#使用-diplay-inline-block)
  - [选择器](#选择器)
    - [常见场景](#常见场景)
      - [链接样式](#链接样式)
      - [选择第一个子元素](#选择第一个子元素)
      - [选择段落中的第一行和第一个字](#选择段落中的第一行和第一个字)
      - [隔行选择](#隔行选择)


## 盒子模型
[回到目录](#目录)

![盒子模型](../assert/css_doc/box_model.png)

* **内容框**是内容所在的区域。内容可以控制**其父级的大小**，因此这通常是大小变化最大的区域。
* **内边距框**围绕着内容框，是由 padding 属性创建的空间。由于内边距位于盒子内部，因此盒子背景在内边距创建的空间中可见。 如果框设置了溢出规则（例如 overflow: auto 或 overflow: scroll），滚动条也会占用此空间。
* **边框**围绕内边距框，其空间由 border 值定义，该值会为元素创建视觉框架。元素的边框边缘是您可以看到的界限。
* **边距框**，即框周围的空间，由框的 margin 规则定义。`outline` 和 `box-shadow` 等属性也会占用此空间，因为它们绘制在元素顶部，不会影响框的大小。更改盒子 200px 中 outline-width 的盒子不会更改边框边缘内的任何内容。

### 盒子大小
[回到上一级](#盒子模型)

* 默认情况下，盒子的 height 属性控制的是 content-box 的高度，盒子的 width 属性控制 content-box 的宽度。即 `box-sizing: content-box;`
* 若希望 height 属性控制的高度即可见区域的高度， width 属性控制的宽度即可见区域宽度，则需要 `box-sizing: border-box;`

~~~css
*,
*::before,
*::after {
  box-sizing: border-box;
}
~~~
* 此 CSS 规则会选择文档中的**每个元素**以及每个 ::before 和 ::after 伪元素，并应用 box-sizing: border-box。这意味着，现在每个元素都使用此替代盒模型
* 由于替代框模型的可预测性更高，因此开发者通常会将此规则添加到**重置和规范化器**中

### 外部显示类型
[回到上一级](#盒子模型)

#### 区块盒子
[回到上一级](#外部显示类型)
* 通过 `display: block;` 设置区块盒子
* 一个拥有 **block 外部显示类型**的盒子会表现出以下行为：

    * 盒子会产生换行。
    * width 和 height 属性可以发挥作用。
    * 内边距、外边距和边框会将其他元素从当前盒子周围“推开”。
    * 如果未指定 width，方框将沿行向扩展，以填充其容器中的可用空间。在大多数情况下，盒子会变得与其容器一样宽，占据可用空间的 100%。

#### 行内盒子
[回到上一级](#外部显示类型)
* 通过 `display: inline;` 设置行内盒子
* 一个拥有 inline 外部显示类型的盒子会表现出以下行为：
    * 盒子不会产生换行。
    * width 和 height 属性将不起作用。
    * 垂直方向的内边距、外边距以及边框会被应用但是不会把其他处于 inline 状态的盒子推开。
    * 水平方向的内边距、外边距以及边框会被应用且会把其他处于 inline 状态的盒子推开。

### 外边距、内边距和边框
[回到上一级](#盒子模型)

#### 外边距
[回到上一级](#外边距内边距和边框)

* 通过 `margin` 属性来设置**各个边的外边距**
~~~css
/* 应用于所有边 */
margin: 1em;
margin: -3px; /* 负值：表现为收缩 */

/* 上边下边 | 左边右边 */
margin: 5% auto;

/* 上边 | 左边右边 | 下边 */
margin: 1em auto 2em;

/* 上边 | 右边 | 下边 | 左边 (从上开始顺时针顺序)*/
margin: 2px 1em 0 auto;
~~~
* 通过 `margin-top` 、 `margin-right` 、 `margin-bottom` 、 `margin-left` 来设置**对应边的外边距**
* 外边距存在**折叠现象**，根据相接触的两个元素是正边距还是负边距，效果会有所不同
  * 两个正边距折叠成一个**值为两个值中最大的正边距**【a=10px, b=20px 》20px】
  * 两个负边距折叠成一个**绝对值为两个值中最大的负边距**【a=-10px, b=-20px 》-20px】
  * 一正一负取**两值相加的边距值**，正负取决于这两个值的绝对值大小【a=10px, b=-20px 》-10px】

#### 边框
[回到上一级](#外边距内边距和边框)

* 可以使用 `border` 属性一次性设置**所有四个边框的宽度、颜色和样式**，可以使用下面列出的一个，两个或三个值来指定 border 属性，**值的顺序无关紧要**。
~~~css
/* style */
border: solid;

/* width | style */
border: 2px dotted;

/* style | color */
border: outset #f33;

/* width | style | color */
border: medium dashed green;
~~~
* 可以使用 `border-top` 、 `border-right` 、 `border-bottom` 、 `border-left` 来设置**对应边框的宽度、颜色和样式**
* 可以使用 `border-width` 、 `border-style` 、 `border-color` 来设置**所有边框对应的属性**
* 可以使用 `border-top-width`, `border-top-style`, `border-top-color`, `border-right-width`, `border-right-style`, `border-right-color`, `border-bottom-width`, `border-bottom-style`, `border-bottom-color`, `border-left-width`, `border-left-style`, `border-left-color` 来设置**对应边框对应的属性**

#### 内边距
[回到上一级](#外边距内边距和边框)
* 通过 `padding` 属性来设置**各个边内边距**
~~~css
/* 应用于所有边 */
padding: 1em;

/* 上边下边 | 左边右边 */
padding: 5% 10%;

/* 上边 | 左边右边 | 下边 */
padding: 1em 2em 2em;

/* 上边 | 右边 | 下边 | 左边 */
padding: 5px 1em 0 2em;
~~~
* 通过 `padding-top` 、 `padding-right` 、 `padding-bottom` 、 `padding-left` 来设置**对应边内边距**

### 使用 diplay: inline-block
[回到上一级](#盒子模型)
`display: inline-block` 是 display 的一个特殊值，它提供了**介于 inline 和 block 之间的中间位置**
一个元素使用 display: inline-block，实现我们需要的块级的部分效果：

* 设置 width 和height 属性会生效。
* padding、margin 和 border 会推开其他元素。
* 它**不会换行**



## 选择器
[回到目录](#目录)

| 选择器 | 描述 | 表示方法 |
| --- | --- | --- |
| 通用选择器 | 选择文档中的所有元素 | `*` |
| 类型选择器 | 选择相同标签的所有元素 | `标签名` |
| 类选择器 | 选择相同类名的所有元素 | `.类名` |
| ID 选择器 | 选择相同ID的元素 | `#ID名` |
| 属性选择器 | 选择相同属性的元素 | `[属性名=属性值]` 选择属性为属性值的元素 <br> `[属性名]` 选择有该属性的元素 <br> `[属性名=属性值 s]` 选择属性为属性值的，且**区分大小写**的元素 <br> `[属性名*=属性值]` 选择有该属性且值**包含属性值**的元素 <br> `[属性名^=属性值]` 选择有该属性且值**以属性值开头**的元素 <br> `[属性名$=属性值]` 选择有该属性且**以属性值结尾**的元素 |
| 分组选择器 | 选择多个元素 | `元素1,元素2,元素3` |
| 伪类 | 选择在特定状态下的元素 | `:状态名` |
| 伪元素 | 选择元素中的特定部分 | `::伪元素名` |
| 后代组合元 | 选择A内部的所有B元素 | `A B` 用空格分隔 |
| 子代组合元 | 选择A内部所有**直接**的B元素 | `A>B` 用>分隔 |
| 相邻兄弟组合元 | 选中**紧接**在 A 元素后面的**第一个** B 元素。两者必须拥有**同一个父元素**。 | `A+B` 用+分隔 |
| 通用兄弟组合元 | 选中 A 元素后面的**所有** B 元素。两者必须拥有**同一个父元素**，但不需要紧挨着。 | `A~B` 用~分隔 |
| 交集选择器 | 选择同时满足多个选择器的元素 | `元素1元素2` **紧挨着**不用空格隔开 |

### 常见场景
[回到上一级](#选择器)

#### 链接样式
[回到上一级](#常见场景)

1. 点击前
~~~css
a:link {
  color: blue;
}
~~~

2. 点击后
~~~css
a:visited {
  color: green;
}
~~~

3. 鼠标悬停
~~~css
a:hover {
  color: yellow;
  text-decoration: underline;
  cursor: pointer;
}
~~~
* `text-decoration: underline;` 添加下划线
* `cursor: pointer;` 鼠标样式为小手

4. 鼠标点击瞬间
~~~css
a:active {
  color: red;
}
~~~

#### 选择第一个子元素
[回到上一级](#常见场景)

1. 选择父元素下**某种类型的第一个子元素**
   * **严格匹配**：只有这个子元素是父元素下的第一个子元素才生效
   ~~~css
   父元素 > 子元素:first-child {
     属性: 值;
   }
   ~~~
   * **灵活匹配**：只要这个子元素是父元素下同类型的第一个元素，就生效
   ~~~css
   父元素 > 子元素:first-of-type {
     属性: 值;
   }
   ~~~

2. 选择父元素下**不论类型的第一个子元素**

~~~css
父元素 > :first-child {
  属性: 值;
}
~~~

#### 选择段落中的第一行和第一个字
[回到上一级](#常见场景)

1. 选择段落中的第一行

~~~css
p::first-line {
  font-weight: bold;
  text-transform: uppercase;
}
~~~
* `font-weight: bold;` 加粗
* `text-transform: uppercase;` 英文变大写

2. 选择段落中的第一个字
~~~css
p::first-letter {
  font-size: 3em;
  float: left;
  line-height: 1;
  padding-right: 10px;
}
~~~
* 这是**首字下沉**的常用技巧，主要用于文章等编写的场景

#### 隔行选择
[回到上一级](#常见场景)

使用伪类选择器`:nth-child([(xn+y)/odd/even])`
* x表示**隔行数**
* y表示**开始行数**，若y为0，则表示从第x行开始
* odd表示**奇数行**
* even表示**偶数行**

表格斑马纹的实现：
~~~css
/* 斑马纹 */
tbody tr:nth-child(even) {
  background-color: #f9f9f9;
}

/* 鼠标悬停高亮 (覆盖上面的颜色) */
tbody tr:hover {
  background-color: #e6f7ff; /* 浅蓝色高亮 */
  cursor: default;
}
~~~

特别的，`nth-chile()`还可以实现交集选择

~~~css
/* 必须大于等于3 (n+3) 且 必须小于等于6 (-n+6) */
tr:nth-child(n + 3):nth-child(-n + 6) {
  background-color: yellow;
}
~~~



