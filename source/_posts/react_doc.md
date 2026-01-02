---
title: React笔记
date: 2025-11-23 21:45:00
tags: 前端
cover: https://kei-blog.oss-cn-beijing.aliyuncs.com/asset/115945063_p0-cut.jpg
categories: 
    - 前端框架
---

# React笔记

## 目录
- [React笔记](#react笔记)
  - [目录](#目录)
  - [快速入门](#快速入门)
    - [创建项目](#创建项目)
      - [Vite构建项目](#vite构建项目)
      - [运行项目](#运行项目)
  - [项目结构](#项目结构)
    - [包文件 package.json](#包文件-packagejson)
      - [核心依赖](#核心依赖)
      - [可执行命令](#可执行命令)
    - [源码文件目录 src](#源码文件目录-src)
      - [入口文件 main.tsx](#入口文件-maintsx)
      - [根组件 App.tsx](#根组件-apptsx)
  - [描述UI](#描述ui)
    - [JSX的定义和使用](#jsx的定义和使用)
      - [第一步: 导出组件并定义函数](#第一步-导出组件并定义函数)
      - [第二步: 添加标签](#第二步-添加标签)
      - [第三步: 使用组件](#第三步-使用组件)
    - [组件的导入与导出](#组件的导入与导出)
    - [JSX的规则](#jsx的规则)
    - [JSX中使用JS表达式](#jsx中使用js表达式)
    - [将Props传递给组件](#将props传递给组件)
      - [步骤一: 将Props传递给子组件](#步骤一-将props传递给子组件)
      - [步骤二: 在子组件中读取Props](#步骤二-在子组件中读取props)
      - [步骤三: 添加一些逻辑](#步骤三-添加一些逻辑)
      - [其他技巧](#其他技巧)
        - [使用JSX展开语法传递Props](#使用jsx展开语法传递props)
        - [将JSX作为子组件传递](#将jsx作为子组件传递)
        - [Props随时间的变化](#props随时间的变化)
    - [条件渲染](#条件渲染)
      - [条件返回JSX](#条件返回jsx)
      - [与运算符（\&\&）](#与运算符)
      - [选择性地将 JSX 赋值给变量](#选择性地将-jsx-赋值给变量)
    - [JSX中渲染列表](#jsx中渲染列表)
    - [保持组件纯粹](#保持组件纯粹)
      - [纯函数：组件作为公式](#纯函数组件作为公式)
      - [局部突变: 组件的小秘密](#局部突变-组件的小秘密)
    - [将UI视为树](#将ui视为树)
  - [添加交互](#添加交互)
    - [响应事件](#响应事件)
      - [添加事件处理函数](#添加事件处理函数)
      - [在事件处理函数中读取props](#在事件处理函数中读取props)
      - [将事件处理函数作为props传递](#将事件处理函数作为props传递)
      - [命名事件处理函数props](#命名事件处理函数props)
      - [事件传播](#事件传播)
      - [阻止传播](#阻止传播)
      - [捕获阶段事件](#捕获阶段事件)
      - [阻止默认行为](#阻止默认行为)
    - [State: 组件的记忆](#state-组件的记忆)
      - [State 变量](#state-变量)
      - [第一个 Hook](#第一个-hook)
      - [剖析 useState](#剖析-usestate)
      - [为一个组件添加多个 state 变量](#为一个组件添加多个-state-变量)
      - [State 的底层逻辑](#state-的底层逻辑)
      - [State 是隔离且私有的](#state-是隔离且私有的)
    - [渲染和提交](#渲染和提交)
      - [步骤一：触发一次渲染](#步骤一触发一次渲染)
        - [初次渲染](#初次渲染)
        - [状态更新时重新渲染](#状态更新时重新渲染)
      - [步骤二：react渲染你的组件](#步骤二react渲染你的组件)
      - [步骤三：react将更改提交到DOM上](#步骤三react将更改提交到dom上)
      - [尾声：浏览器绘制](#尾声浏览器绘制)
    - [State: 如同一张快照](#state-如同一张快照)
      - [设置State会触发渲染](#设置state会触发渲染)
      - [渲染会及时生成一张快照](#渲染会及时生成一张快照)
      - [随时间变化的State](#随时间变化的state)
    - [把一系列 state 更新加入队列](#把一系列-state-更新加入队列)
      - [React 会对 state 更新进行批处理](#react-会对-state-更新进行批处理)
      - [在下次渲染前多次更新同一个state](#在下次渲染前多次更新同一个state)
      - [如果你在替换 state 后更新 state 会发生什么](#如果你在替换-state-后更新-state-会发生什么)
      - [如果你在更新 state 后替换 state 会发生什么](#如果你在更新-state-后替换-state-会发生什么)
      - [命名惯例](#命名惯例)
    - [更新 state 中的对象](#更新-state-中的对象)
      - [将 state 视为只读的](#将-state-视为只读的)
      - [使用展开语法复制对象](#使用展开语法复制对象)
      - [更新一个嵌套对象](#更新一个嵌套对象)
      - [使用 Immer 编写简洁的更新逻辑](#使用-immer-编写简洁的更新逻辑)
    - [更新 state 中的数组](#更新-state-中的数组)
      - [在没有 mutation 的前提下更新数组](#在没有-mutation-的前提下更新数组)
        - [向数组中添加元素](#向数组中添加元素)
        - [从数组中删除元素](#从数组中删除元素)
        - [转换数组](#转换数组)
        - [替换数组中的元素](#替换数组中的元素)
        - [向数组指定位置插入元素](#向数组指定位置插入元素)
        - [翻转数组和数组排序](#翻转数组和数组排序)
        - [更新数组内部的对象](#更新数组内部的对象)
        - [使用 Immer 来处理数组](#使用-immer-来处理数组)
  - [状态管理](#状态管理)
    - [用 State 响应输入](#用-state-响应输入)
      - [声明式 UI 与命令式 UI 的比较](#声明式-ui-与命令式-ui-的比较)
      - [声明式地考虑 UI](#声明式地考虑-ui)
        - [步骤一：定位组件中不同的视图状态](#步骤一定位组件中不同的视图状态)
        - [步骤二：确定是什么触发了这些状态的改变](#步骤二确定是什么触发了这些状态的改变)
        - [步骤三：通过 useState 表示内存中的 state](#步骤三通过-usestate-表示内存中的-state)
        - [步骤四：删除任何不必要的 state 变量](#步骤四删除任何不必要的-state-变量)
        - [步骤五：连接事件处理函数以设置 state](#步骤五连接事件处理函数以设置-state)
    - [选择 State 结构](#选择-state-结构)
      - [构建 State 的原则](#构建-state-的原则)
      - [一、合并关联的 state](#一合并关联的-state)
      - [二、避免互相矛盾的 state](#二避免互相矛盾的-state)
      - [三、避免冗余的 state](#三避免冗余的-state)
      - [四、避免重复的 state](#四避免重复的-state)
      - [五、避免深度嵌套的 state](#五避免深度嵌套的-state)

## 快速入门

### 创建项目

#### Vite构建项目

~~~bash
npm create vite@latest my-app -- --template react-ts
~~~
* `npm create` - npm 的包初始化命令，用于创建新项目
* `vite@latest` - 指定使用最新版本的 Vite 包来创建项目
* `my-app` - 这是你将要创建的项目名称（文件夹名）
* `--` - 分隔符，用于区分 npm 命令本身的参数和传递给实际创建工具的参数
* `--template react-ts` - 指定使用 React + TypeScript 模板

#### 运行项目
~~~bash
npm run dev
~~~


## 项目结构
[回到目录](#目录)

### 包文件 package.json
[回到上一级](#项目结构)

#### 核心依赖
[回到上一级](#包文件-packagejson)
~~~json
{
    "dependencies": {
        "react": "^19.2.0",
        "react-dom": "^19.2.0"
    },
}
~~~

#### 可执行命令
[回到上一级](#包文件-packagejson)
~~~json
{
    "scripts": {
        "dev": "vite",
        "build": "tsc -b && vite build",
        "lint": "eslint .",
        "preview": "vite preview"
    },
}
~~~
* 通过 `npm run <script-name>` 运行可执行命令

### 源码文件目录 src
[回到上一级](#项目结构)

#### 入口文件 main.tsx
[回到上一级](#源码文件目录-src)
~~~tsx
import { createRoot } from 'react-dom/client'
import App from './App.tsx'

createRoot(document.getElementById('root')!).render(<App />)
~~~

#### 根组件 App.tsx
[回到上一级](#源码文件目录-src)
~~~tsx
function App() {
  return (
      <div className="App">
        this is App
      </div>
  )
}

export default App
~~~

## 描述UI
[回到目录](#目录)

- [JSX的定义和使用](#jsx的定义和使用)
- [组件的导入与导出](#组件的导入与导出)
- [JSX的规则](#jsx的规则)
- [JSX中使用JS表达式](#jsx中使用js表达式)
- [JSX中渲染列表](#jsx中渲染列表)
- [将Props传递给组件](#将props传递给组件)
- [条件渲染](#条件渲染)

JSX 是 JavaScript 和 XML(HTML) 的缩写,表示在**JS代码中编写HTML的模板结构**,是react中编写UI模板的方式

优势:
* HTML的声明式模板写法
* JS的可编程能力

JSX是JS的语法拓展,需要**解析工具解析**之后才能在浏览器中运行

### JSX的定义和使用
[回到上一级](#描述)

#### 第一步: 导出组件并定义函数
[回到上一级](#jsx的定义和使用)
~~~tsx
export default function MyComponent() {}
~~~
* **export default** 前缀是一种 JavaScript 标准语法
* 它允许你**导出**一个文件中的主要函数以便你以后可以从其他文件引入它
* 使用 **function MyComponent() { }** 定义名为 MyComponent 的 JavaScript 函数
* React 组件是常规的 JavaScript 函数，但 组件的名称必须以**大写字母开头**，否则它们将无法运行

#### 第二步: 添加标签
[回到上一级](#jsx的定义和使用)
~~~tsx
return (
  <div>
    MyComponent
  </div>
);
~~~
* 若标签和return不在同一行, 则需要添加括号

#### 第三步: 使用组件
[回到上一级](#jsx的定义和使用)
~~~tsx
function MyComponent() {
  return (
    <div>
      MyComponent
    </div>
  )
}
export default function App() {
  return (
    <section>
      <MyComponent />
      <MyComponent />
      <MyComponent />
    </section>
  )
}
~~~
* 注意不要嵌套定义组件, 会导致运行效率下降, 且会报错

### 组件的导入与导出
[回到上一级](#描述ui)

1. 创建一个新的tsx文件来存放该组件

MyExport.tsx
~~~tsx
function MyComponent() {
    return (
        <div>
            this is MyComponent
        </div>
    );
}
export default function MyExport() {
    return (
        <section>
            <MyComponent />
            <MyComponent />
            <MyComponent />
        </section>
    );
}
~~~
* 可以使用默认导出和具名导出

2. 导入该组件

App.tsx
~~~tsx
// 项目的根组件
// App -> index.js -> index.html(root)
import MyExport from "./MyExport"
function App() {
  return (
    <MyExport />
  );
}

export default App
~~~
* 可以使用默认导入和具名导入

### JSX的规则
[回到上一级](#描述ui)

1. 只能返回一个根元素
* 如果想要在一个组件中包含**多个元素**，需要用一个**父标签**把它们包裹起来
* 如果你不想在标签中增加一个额外的 `<div>`，可以用 `<>` 和 `</>` 元素来代替, 这个空标签被称作 Fragment, **React Fragment** 允许你将子元素分组，而不会在 HTML 结构中添加额外节点

2. 标签必须闭合
3. 使用驼峰命名法给大部分属性命名
* 由于历史原因，`aria-*` 和 `data-*` 属性是以带 - 符号的 HTML 格式书写的

### JSX中使用JS表达式
[回到上一级](#描述ui)

React允许在JSX中嵌入JS表达式,通过`{}`来包裹JS表达式

使用的表达式主要有一下四种场景:
* 使用引号传递字符串
* 识别JS变量
* 函数以及方法调用
* 使用JS对象

~~~tsx
const count = 100

function getName(){
  return 'hello world'
}
function App() {
  return (
      <div className="App">
        this is App
        {/* 使用引号传递字符串 */}
        {'this is a string'}
        {/* 识别JS变量 */}
        {count}
        {/* 函数调用 */}
        {getName()}
        {/* 方法调用 */}
        {new Date().getDate()}
        {/* 使用JS对象 外层{}是识别表达式的语法,内层是JS对象结构 */}
        <div style={{ color: "red" }}>this is div</div>
      </div>
  )
}
~~~

### 将Props传递给组件
[回到上一级](#描述ui)

- [步骤一: 将Props传递给子组件](#将props传递给组件)
- [步骤二: 在子组件中读取Props](#将props传递给组件)
- [步骤三: 添加一些逻辑](#步骤三-添加一些逻辑)
- [其他技巧](#其他技巧)

Props 是你传递给 **JSX 标签**的信息

#### 步骤一: 将Props传递给子组件
[回到上一级](#将props传递给组件)

首先，将一些 props 传递给 Avatar
~~~tsx
export default function Profile() {
  return (
    <Avatar
      person={{ name: 'Lin Lanying', imageId: '1bX5QH6' }}
      size={100}
    />
  );
}
~~~
* 这里传递了两个props, person和size
* person是一个对象, size是一个数字

#### 步骤二: 在子组件中读取Props
[回到上一级](#将props传递给组件)

子组件Avatar
~~~tsx
function Avatar({ person, size }) {
  // 在这里 person 和 size 是可访问的
}
~~~
* 你可以通过在 function Avatar 之后直接列出它们的名字 person, size 来读取这些 props
* 这些 props 在 ({ 和 }) 之间，并由逗号分隔
* 你可以通过在参数后面写 `=` 和默认值来进行解构
* 默认值仅在缺少 size prop 或 size={undefined} 时生效。 但是如果你传递了 size={null} 或 size={0}，默认值将 不 被使用

#### 步骤三: 添加一些逻辑
[回到上一级](#将props传递给组件)

utils.tsx
~~~tsx
export interface Person{
    name: string,
    imageId: string
}

export function getImageUrl(person: Person, size = 's') {
    return (
        'https://i.imgur.com/' +
        person.imageId +
        size +
        '.jpg'
    );
}
~~~
* tsx中, 参数需要指定类型
* 接口Person定义了name和imageId两个属性, 指定类型为string

App.tsx
~~~tsx
import { getImageUrl } from "./utils";
import type { Person } from "./utils";

interface AvatarProps {
  person: Person;
  size: number;
}

function Avatar({person, size}: AvatarProps){
  return (
    <img
      className="avatar"
      src={getImageUrl(person)}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}
function App() {
  return (
    <>
      <Avatar
        size={100}
        person={{
          name: 'Katsuko Saruwatari',
          imageId: 'YfeOqp2'
        }}
      />
    </>
  );
}
~~~
* props作为形参, 同样要指定类型
* 接口AvatarProps定义了person和size两个属性, 指定了类型为Person和number

#### 其他技巧
[回到上一级](#将props传递给组件)

##### 使用JSX展开语法传递Props
一些组件将它们 **所有的 props** 转发给子组件, 这些组件不直接使用他们本身的任何 props

Profile 转给 Avatar
~~~tsx
function Profile({ person, size, isSepia, thickBorder }) {
  return (
    <div className="card">
      <Avatar
        person={person}
        size={size}
        isSepia={isSepia}
        thickBorder={thickBorder}
      />
    </div>
  );
}
~~~
使用更简洁的“展开”语法
~~~tsx
function Profile(props) {
  return (
    <div className="card">
      <Avatar {...props} />
    </div>
  );
}
~~~
* 请克制地使用展开语法

##### 将JSX作为子组件传递
[回到上一级](#其他技巧)

utils.tsx
~~~tsx
export interface Person{
    name: string,
    imageId: string
}

export function getImageUrl(person: Person, size = 's') {
    return (
        'https://i.imgur.com/' +
        person.imageId +
        size +
        '.jpg'
    );
}
~~~

Avatar.tsx
~~~tsx
import type { Person } from "./utils";
import { getImageUrl } from "./utils";

export interface AvatarProps {
    person: Person;
    size: number;
}

export function Avatar({ person, size }: AvatarProps) {
    return (
        <img
            className="avatar"
            src={getImageUrl(person)}
            alt={person.name}
            width={size}
            height={size}
        />
    );
}
~~~

App.tsx
~~~tsx
// 项目的根组件
// App -> index.js -> index.html(root)
import type React from "react";
import { Avatar } from "./Avatar";

interface Children {
  children: React.ReactNode
}

function Card({children}: Children){
  return (
    <div className="card">
      {children}
    </div>
  )
}

function App() {
  return (
    <>
    <Card> 
      <Avatar
        size={100}
        person={{
          name: 'Katsuko Saruwatari',
          imageId: 'YfeOqp2'
        }}
      />
    </Card>
    </>
  );
}

export default App
~~~
* 父组件Card接受子组件Avatar的JSX作为参数, 然后将其传递给子组件div容器中
* ReactNode 是一个**联合类型**，可以包含以下所有可能的值类型：
  * string - **文本内容**（如 This is children）
  * number - **数字值**（如 42）
  * boolean - **布尔值**（通常用于条件渲染）
  * null/undefined - **空值**（React 会忽略渲染）
  * ReactElement - **单个 JSX 元素**（如 `<Avatar />`）
  * ReactFragment - **React 片段**（如 <>...</>）
  * ReactNode[] - 上述任意类型的**数组**
* 所以, 实际上你不仅可以让父组件card接受子组件Avatar的JSX作为参数, 你也可以接受任意**以上类型的参数**
* React.ReactNode 本质上是 React 对"任何可以渲染的内容"的**类型抽象**，是 React 类型系统中**处理子元素**的标准方式

##### Props随时间的变化
[回到上一级](#其他技巧)

* 一个组件可能会随着时间的推移收到不同的 props
* props 是 **不可变的**, 当一个组件需要改变它的 props（例如，响应用户交互或新数据）时, 它必须**创建一个新的对象**来替换它, 它的**旧 props 将被丢弃**，最终 JavaScript 引擎将回收它们占用的内存
* 当你需要响应用户输入（例如更改所选颜色）时，你可以“设置 state”

### 条件渲染
[回到上一级](#描述ui)

#### 条件返回JSX
[回到上一级](#条件渲染)

1. 传递一个带有**布尔属性**的Props
App.tsx
~~~tsx
function App() {
  return (
    <section>
      <h1>Kei 的 购物清单</h1>
      <ul>
        <Item
          isPacked={true}
          name="宇航服"/>
        <Item
          isPacked={false}
          name="带金箔的头盔"/>
        <Item
          isPacked={true}
          name="一色彩羽的照片"/>
      </ul>
    </section>
  );
}
~~~
* 这里使用布尔属性isPacked来表示Item是否被打包

2. 通过**条件判断**语句返回不同的子组件JSX
App.tsx
~~~tsx
interface ItemProps {
  isPacked: boolean;
  name: string;
}

function Item( {isPacked, name}: ItemProps){
  if (isPacked){
    return <li className="item">{name} ✔</li>;
  } else {
    return <li className="item">{name}</li>;
  }
}
~~~
* isPacked属性为true时, 返回带有✔的JSX, 否则返回不带✔的JSX
* 返回值可以为null, 表示不渲染任何内容
* 只有两个if分支时, 可以使用三元表达式

三元表达式写法
~~~tsx
function Item( {isPacked, name}: ItemProps){
  return <li className="item"> { isPacked ? name + ' √' : name } </li>
}
~~~

可以添加更多换行和括号来嵌套JSX, 比如删除线`<del></del>`
~~~tsx
function Item( {isPacked, name}: ItemProps){
  return (
    <li className="item">
      {
        isPacked ?
        (
          <del>
            {name + '✔'}
          </del>
        ):
        (
          name
        )
      }
    </li>
  )
}
~~~

#### 与运算符（&&） 
[回到上一级](#条件渲染)
在 React 组件里，通常用在当条件成立时，你想渲染一些 JSX，或者不做任何渲染。使用 `&&`，你也可以实现**仅当 isPacked 为 true** 时，渲染勾选符号

~~~tsx
function Item( {isPacked, name}: ItemProps){
  return (
    <li className="item">
      {name}{ isPacked && "✔" }
    </li>
  )
}
~~~
* 当 isPacked 为真值时，则（&&）渲染勾选符号，否则，不渲染
* 需注意, `&&` 的左侧需严格为**布尔值**或**条件表达式**, 否则起不到条件渲染的效果, 如果左侧为数字0, 则会**直接渲染 0**

#### 选择性地将 JSX 赋值给变量 
[回到上一级](#条件渲染)
当这些快捷方式妨碍写普通代码时，可以考虑使用 **if 语句和变量**

~~~tsx
function Item( {isPacked, name}: ItemProps){
  let itemContent;
  if (isPacked){
    itemContent = (
      <del>
        {name + "✔"}
      </del>
    );
  }else{
    itemContent = name;
  }
  return (
    <li className="item">
      {itemContent}
    </li>
  )
}
~~~
* 这种方式是最冗长的，但也是最灵活的
* 注意在tsx中, let 声明的变量初始化后, **不能隐式转换类型**

### JSX中渲染列表
[回到上一级](#描述ui)

渲染列表可以使用JS原生的**map方法**遍历数组, 然后返回JSX元素

~~~tsx
const list = [
  { id: 1, name: 'Vue' },
  { id: 2, name: 'React' },
  { id: 3, name: 'Angular' }
]
function App() {
  return (
    <div className="App">
      this is App
      {/* 实现列表渲染 */}
      <ul>
        {list.map(item => <li key={item.id}>{item.name}</li>)}
      </ul>
    </div>
  )
}
~~~
* 注意列表渲染要加上**独一无二的key**属性, 一般使用字符串或数值作为key, 用于React**提升列表渲染性能**

### 保持组件纯粹
[回到上一级](#描述ui)

部分 JavaScript 函数是 **纯粹** 的，这类函数通常被称为**纯函数**。纯函数仅执行计算操作，不做其他操作

#### 纯函数：组件作为公式 
[回到上一级](#保持组件纯粹)

在计算机科学中（尤其是函数式编程的世界中），纯函数 通常具有如下特征：

* **只负责自己的任务**。它不会更改在该函数调用前就已存在的对象或变量。
* **输入相同，则输出相同**。给定相同的输入，纯函数应总是返回相同的结果。

React 便围绕着这个概念进行设计。React 假设你编写的所有组件都是纯函数。也就是说，对于**相同的输入**，你所编写的 React 组件必须**总是返回相同的 JSX**

~~~tsx
interface RecipeProps {
  drinker: number;
}

function Recipe({drinker} : RecipeProps){
  return (
    <ol>
      <li>Boil {drinker} cup of water</li>
      <li>Add {drinker} spoons of tea and {0.5 * drinker} spoons of spice </li>
      <li>Add {0.5 * drinker} cups of milk to boil and sugar to taste </li>
    </ol>
  )
}

function App() {
  return (
    <section>
      <ul>
        <h1>Spiced Chai Recipe</h1>
        <h2>For two</h2>
        <Recipe drinker={2} />
        <h2>For a gathering</h2>
        <Recipe drinker={4} />
      </ul>
    </section>
  );
}

export default App
~~~
* 当你给函数 Recipe 传入 drinkers={2} 参数时，它将返回包含 2 cups of water 的 JSX。永远如此。
* 而当你传入 drinkers={4} 时，它将返回包含 4 cups of water 的 JSX。永远如此。
* 就像数学公式一样

当违反这一规则, 那么每次 React 渲染 Recipe 组件时，React 都会生成不同的 JSX, 这可能产生**不符合预期**的结果, 这种现象被称为 **突变(mutation)**

#### 局部突变: 组件的小秘密
[回到上一级](#保持组件纯粹)

纯函数不会改变**函数作用域外**的变量、或在函数调用前创建的对象——这会使函数变得不纯粹
~~~tsx
interface CupProps {
  guest: number;
}

function Cup({ guest }: CupProps) {
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaGathering() {
  const cups = [];
  for (let i = 1; i <= 12; i++) {
    cups.push(<Cup key={i} guest={i} />);
  }
  return cups;
}
~~~
* 如果 cups 变量或 [] 数组是在 TeaGathering **函数之外**创建的，这将是一个很大的问题！因为如果那样的话，当你调用数组的 push 方法时，就会更改 预先存在的 对象
* 但是，这里不会有影响，因为每次渲染时，你都是在 TeaGathering **函数内部**创建的它们。TeaGathering 之外的代码并不会知道发生了什么
* 这就被称为 **“局部 mutation”** — 如同藏在组件里的小秘密。

### 将UI视为树
[回到上一级](#描述ui)

树是项目和 UI 之间的关系模型，通常使用**树结构**来表示 UI。例如，浏览器使用树结构来建模 HTML（DOM）与CSS（CSSOM）。移动平台也使用树来表示其视图层次结构
![alt text](../assert/react_doc/preserving_state_dom_tree.webp)


* 树是表示实体之间关系的常见方式，它们经常用于建模 UI。
* 渲染树表示单次渲染中 React 组件之间的嵌套关系。
* 使用条件渲染，渲染树可能会在不同的渲染过程中发生变化。使用不同的属性值，组件可能会渲染不同的子组件。
* 渲染树有助于识别顶级组件和叶子组件。顶级组件会影响其下所有组件的渲染性能，而叶子组件通常会频繁重新渲染。识别它们有助于理解和调试渲染性能问题。
* 依赖树表示 React 应用程序中的模块依赖关系。
* 构建工具使用依赖树来捆绑必要的代码以部署应用程序。
* 依赖树有助于调试大型捆绑包带来的渲染速度过慢的问题，以及发现哪些捆绑代码可以被优化。


## 添加交互
[回到目录](#目录)

### 响应事件
[回到上一级](#添加交互)

#### 添加事件处理函数
[回到上一级](#响应事件)

如需添加一个事件处理函数，你需要**先定义一个函数**，然后 将其作为 prop 传入 合适的 JSX 标签

比如, 你想让按钮**点击时**弹出一个消息框
~~~tsx
export default function Button(){
  function handleClick(){
    alert('You clicked me!');
  }

  return (
    <button onClick={handleClick}>
      Click me 
    </button>
  )
}
~~~
按照如下**三个步骤**，即可让它在用户点击时显示消息：
* 在 Button 组件 **内部** 声明一个名为 handleClick 的函数。
* 实现函数内部的逻辑（使用 alert 来显示消息）。
* 添加 onClick={handleClick} 到 `<button>` JSX 中。

**事件处理函数**有如下特点:

* 通常在你的组件 **内部** 定义
* 名称以 handle 开头，后跟事件名称

你也可以在 JSX 中定义一个**内联的事件处理函数**
~~~tsx
export default function Button(){
  return (
    <button onClick={function handleClick(){
      alert('You clicked me!')
    }}>
      Click me 
    </button>
  )
}
~~~

或者直接使用更为简洁**箭头函数**
~~~tsx
export default function Button(){
  return (
    <button onClick={() => {
      alert('You clicked me!')
    }}>
      Click me 
    </button>
  )
}
~~~

**注意事项:**
* 传递给事件处理函数的函数应**直接传递**，而非**调用**
* **传递函数**只在事件触发时才执行, 而**调用函数**在渲染时会直接执行, 这是因为位于 JSX {} 之间的 JavaScript 会立即执行
~~~tsx
// 传递一个函数
<button onClick={handleClick}>
<button onClick={() => alert('You clicked me!')}>

// 调用一个函数
<button onClick={handleClick()}>
<button onClick={alert('You clicked me!')}>
~~~

#### 在事件处理函数中读取props
[回到上一级](#响应事件)

由于事件处理函数声明于**组件内部**，因此它们可以**直接访问**组件的 props
~~~tsx
interface AlertButtonProps {
  message: string;
  children: React.ReactNode;
}

function AlertButton({message, children}: AlertButtonProps){
  return(
    <button onClick={() => alert(message)}>
      {children}
    </button>
  )
}

export default function ToolBar(){
  return (
    <>
      <AlertButton message="正在播放...">
        播放
      </AlertButton>
      <AlertButton message="正在暂停...">
        暂停
      </AlertButton>
    </>
  )
}
~~~

#### 将事件处理函数作为props传递
[回到上一级](#响应事件)

通常，我们会在父组件中定义子组件的事件处理函数
~~~tsx
interface ButtonProps {
  onClick: () => void;
  children: React.ReactNode;
}

interface PlayButtonProps {
  movieName: string;
}

function Button({onClick, children}: ButtonProps){
  return (
    <button onClick={onClick}>
      {children}
    </button>
  )
}

function PlayButton({movieName}: PlayButtonProps){
  return(
    <Button onClick={() => alert("正在播放" + movieName)}>
      播放
    </Button>
  )
}

function UploadButton(){
  function handleClick(){
    alert("正在上传...")
  }

  return (
    <Button onClick={handleClick}>
      上传
    </Button>
  )
}

export default function ToolsBar(){
  return (
    <section>
      <PlayButton movieName="《拔作岛》"/>
      <UploadButton/>
    </section>
  )
}
~~~

示例中，Toolbar 组件渲染了一个 PlayButton 组件和 UploadButton 组件：
* PlayButton 将箭头函数`() => alert("正在播放" + movieName)`作为 onClick prop 传递给 Button 组件
* UploadButton 将 handleClick 函数作为 onClick prop 传递给 Button 组件

* 最后，你的 Button 组件接收一个名为 onClick 的 prop; 它直接将这个 prop 以 `onClick={onClick}` 方式传递给浏览器内置的 `<button>`
* 当点击按钮时，React 会调用**传入的函数**

#### 命名事件处理函数props
[回到上一级](#响应事件)

内置组件（`<button>` 和 `<div>`）仅支持 **浏览器事件名称**，例如 onClick。但是，当你构建**自己的组件**时，你可以按你个人喜好命名事件处理函数的 prop。

~~~tsx
export default function App(){
  return (
    <div>
      <Toolbar
        onPlayMovie={() => alert('Playing Movie')}
        onUploadImage={() => alert('Uploading Image')} 
      />
    </div>
  )
}

interface ToolbarProps {
  onPlayMovie: () => void;
  onUploadImage: () => void;
}

function Toolbar({onPlayMovie, onUploadImage}: ToolbarProps){
  return (
    <>
      <Button onClick={onPlayMovie}>Play Movie</Button>
      <Button onClick={onUploadImage}>Upload Image</Button>
    </>
  )
}

interface ButtonProps {
  onClick: () => void;
  children: React.ReactNode;
}

function Button({onClick, children}: ButtonProps){
  return (
    <button onClick={onClick}>
      {children}
    </button>
  )
}
~~~
* 浏览器内置的 `<button>`（小写）仍然需要使用 onClick prop
* 而自定义的 Button 组件接收到的 prop 名称可由你决定

#### 事件传播
[回到上一级](#响应事件)

事件处理函数还将捕获任何来自子组件的事件。通常，我们会说事件会沿着树向上“冒泡”或“传播”：它从事件发生的地方开始，然后**沿着树向上传播**。

~~~tsx
export default function App(){
  return (
    <div className="Toolbar" onClick={() => alert("Clicked Toolbar")}>
      <button onClick={() => alert("Playing Movie")}>Play Movie</button>
      <button onClick={() => alert("Uploading Image")}>Upload Image</button>
    </div>
  )
}
~~~
* 如果你点击任一按钮，它自身的 onClick 将首先执行，然后父级 `<div>` 的 onClick 会接着执行
* 因此会出现两条消息。如果你点击 toolbar 本身，将只有父级 `<div>` 的 onClick 会执行

#### 阻止传播
[回到上一级](#响应事件)

事件处理函数接收一个 **事件对象** 作为唯一的参数。按照惯例，它通常被称为 e ，代表 “event”（事件）。你可以使用此对象来**读取有关事件的信息**。这个事件对象还允许你**阻止传播**

~~~tsx
import type React from "react";
import "./css/example.css"
export default function App(){
  return (
    <div className="Toolbar" onClick={() => alert("Clicked Toolbar")}>
      <Button onClick={() => alert("Playing Movie")}>Play Movie</Button>
      <Button onClick={() => alert("Uploading Image")}>Upload Image</Button>
    </div>
  )
}

interface ButtonProps {
  onClick: () => void;
  children: React.ReactNode;
}

function Button({onClick, children}: ButtonProps){
  return (
    <button onClick={ e => {
      e.stopPropagation();
      onClick();
    }}>
      {children}
    </button>
  )
}
~~~
**先阻止**事件的冒泡，**再执行**事件处理函数
* React 调用了传递给 `<button>` 的 onClick 处理函数。
* 定义在 Button 中的处理函数执行了如下操作：
  * 调用 e.stopPropagation()，阻止事件进一步冒泡。
  * 调用 onClick 函数，它是从 Toolbar 组件传递过来的 prop。
* 在 Toolbar 组件中定义的函数，显示按钮对应的 alert。
* 由于传播被阻止，父级 `<div>` 的 onClick 处理函数不会执行。

#### 捕获阶段事件
[回到上一级](#响应事件)

每个事件分三个阶段传播：

1. 它**向下传播**，调用所有的 onClickCapture 处理函数。
2. 它执行被点击元素的 onClick 处理函数。
3. 它**向上传播**，调用所有的 onClick 处理函数。

~~~tsx
export default function App(){
  return (
    <div className="Toolbar" 
      onClickCapture={() => alert("div capture")}
      onClick={() => alert("div click")}
    >
      <button 
        onClickCapture={() => alert("button capture")}
        onClick={() => alert("button click")}
      >
          Play Movie
      </button>
    </div>
  )
}
~~~
* 触发顺序为 div capture -> button capture -> button click -> div click

#### 阻止默认行为
[回到上一级](#响应事件)

某些浏览器事件具有与事件相关联的默认行为。例如，点击 `<form>` 表单内部的按钮会触发表单提交事件，默认情况下将**重新加载整个页面**：

~~~tsx
export default function SignUp(){
  return (
    <>
      <form
        onSubmit={() => alert("提交表单!")}
      >
        <input/>
        <button>提交</button>
      </form>
    </>
  )
}
~~~

你可以调用事件对象中的 **e.preventDefault()** 来阻止这种情况发生：

~~~tsx
export default function SignUp(){
  return (
    <>
      <form
        onSubmit={e => {
          e.preventDefault()
          alert("提交表单!")}}
      >
        <input/>
        <button>提交</button>
      </form>
    </>
  )
}
~~~


### State: 组件的记忆
[回到上一级](#添加交互)

#### State 变量
[回到上一级](#state-组件的记忆)

为什么需要 State 变量：
* 局部变量**无法在多次渲染中持久保存**。 当 React 再次渲染这个组件时，它会从头开始渲染——不会考虑之前对局部变量的任何更改
* 更改局部变量**不会触发渲染**。 React 没有意识到它需要使用新数据再次渲染组件

State 变量：
* *State* 变量 用于**保存渲染间的数据**
* *State setter* 函数 **更新变量**并触发 React **再次渲染**组件

添加一个 state 变量：

1. 要添加 state 变量，先从文件顶部的 React 中**导入** useState

~~~tsx
import { useState } from 'react';
~~~

2. 将普通变量的声明改为：

~~~tsx
const [index, setIndex] = useState(0);
~~~
* index 是一个 **state 变量**，setIndex 是对应的 **setter 函数**
* 这里的 `[` 和 `]` 语法称为**数组解构**，它允许你从数组中读取值。 useState 返回的数组总是正好有两项

示例程序：
* 数据文件 data.ts
~~~tsx
export const sculptureList = [{
  name: 'Homenaje a la Neurocirugía',
  artist: 'Marta Colvin Andrade',
  description: 'Although Colvin is predominantly known for abstract themes that allude to pre-Hispanic symbols, this gigantic sculpture, an homage to neurosurgery, is one of her most recognizable public art pieces.',
  url: 'https://i.imgur.com/Mx7dA2Y.jpg',
  alt: 'A bronze statue of two crossed hands delicately holding a human brain in their fingertips.'  
}, {
  name: 'Floralis Genérica',
  artist: 'Eduardo Catalano',
  description: 'This enormous (75 ft. or 23m) silver flower is located in Buenos Aires. It is designed to move, closing its petals in the evening or when strong winds blow and opening them in the morning.',
  url: 'https://i.imgur.com/ZF6s192m.jpg',
  alt: 'A gigantic metallic flower sculpture with reflective mirror-like petals and strong stamens.'
}, {
  name: 'Eternal Presence',
  artist: 'John Woodrow Wilson',
  description: 'Wilson was known for his preoccupation with equality, social justice, as well as the essential and spiritual qualities of humankind. This massive (7ft. or 2,13m) bronze represents what he described as "a symbolic Black presence infused with a sense of universal humanity."',
  url: 'https://i.imgur.com/aTtVpES.jpg',
  alt: 'The sculpture depicting a human head seems ever-present and solemn. It radiates calm and serenity.'
}, {
  name: 'Moai',
  artist: 'Unknown Artist',
  description: 'Located on the Easter Island, there are 1,000 moai, or extant monumental statues, created by the early Rapa Nui people, which some believe represented deified ancestors.',
  url: 'https://i.imgur.com/RCwLEoQm.jpg',
  alt: 'Three monumental stone busts with the heads that are disproportionately large with somber faces.'
}, {
  name: 'Blue Nana',
  artist: 'Niki de Saint Phalle',
  description: 'The Nanas are triumphant creatures, symbols of femininity and maternity. Initially, Saint Phalle used fabric and found objects for the Nanas, and later on introduced polyester to achieve a more vibrant effect.',
  url: 'https://i.imgur.com/Sd1AgUOm.jpg',
  alt: 'A large mosaic sculpture of a whimsical dancing female figure in a colorful costume emanating joy.'
}, {
  name: 'Ultimate Form',
  artist: 'Barbara Hepworth',
  description: 'This abstract bronze sculpture is a part of The Family of Man series located at Yorkshire Sculpture Park. Hepworth chose not to create literal representations of the world but developed abstract forms inspired by people and landscapes.',
  url: 'https://i.imgur.com/2heNQDcm.jpg',
  alt: 'A tall sculpture made of three elements stacked on each other reminding of a human figure.'
}, {
  name: 'Cavaliere',
  artist: 'Lamidi Olonade Fakeye',
  description: "Descended from four generations of woodcarvers, Fakeye's work blended traditional and contemporary Yoruba themes.",
  url: 'https://i.imgur.com/wIdGuZwm.png',
  alt: 'An intricate wood sculpture of a warrior with a focused face on a horse adorned with patterns.'
}, {
  name: 'Big Bellies',
  artist: 'Alina Szapocznikow',
  description: "Szapocznikow is known for her sculptures of the fragmented body as a metaphor for the fragility and impermanence of youth and beauty. This sculpture depicts two very realistic large bellies stacked on top of each other, each around five feet (1,5m) tall.",
  url: 'https://i.imgur.com/AlHTAdDm.jpg',
  alt: 'The sculpture reminds a cascade of folds, quite different from bellies in classical sculptures.'
}, {
  name: 'Terracotta Army',
  artist: 'Unknown Artist',
  description: 'The Terracotta Army is a collection of terracotta sculptures depicting the armies of Qin Shi Huang, the first Emperor of China. The army consisted of more than 8,000 soldiers, 130 chariots with 520 horses, and 150 cavalry horses.',
  url: 'https://i.imgur.com/HMFmH6m.jpg',
  alt: '12 terracotta sculptures of solemn warriors, each with a unique facial expression and armor.'
}, {
  name: 'Lunar Landscape',
  artist: 'Louise Nevelson',
  description: 'Nevelson was known for scavenging objects from New York City debris, which she would later assemble into monumental constructions. In this one, she used disparate parts like a bedpost, juggling pin, and seat fragment, nailing and gluing them into boxes that reflect the influence of Cubism’s geometric abstraction of space and form.',
  url: 'https://i.imgur.com/rN7hY6om.jpg',
  alt: 'A black matte sculpture where the individual elements are initially indistinguishable.'
}, {
  name: 'Aureole',
  artist: 'Ranjani Shettar',
  description: 'Shettar merges the traditional and the modern, the natural and the industrial. Her art focuses on the relationship between man and nature. Her work was described as compelling both abstractly and figuratively, gravity defying, and a "fine synthesis of unlikely materials."',
  url: 'https://i.imgur.com/okTpbHhm.jpg',
  alt: 'A pale wire-like sculpture mounted on concrete wall and descending on the floor. It appears light.'
}, {
  name: 'Hippos',
  artist: 'Taipei Zoo',
  description: 'The Taipei Zoo commissioned a Hippo Square featuring submerged hippos at play.',
  url: 'https://i.imgur.com/6o5Vuyu.jpg',
  alt: 'A group of bronze hippo sculptures emerging from the sett sidewalk as if they were swimming.'
}];
~~~
* 一个数组，里面存放了12个对象，对象里面有5个属性，name，artist，description，url，alt
<br>

* 程序文件 App.js
~~~tsx
import "./css/example.css"

import { useState } from 'react';
import { sculptureList } from './data/data';

export default function Gallery() {
  const [index, setIndex] = useState(0);

  function handleClick() {
    setIndex(index + 1);
  }

  let sculpture = sculptureList[index];
  return (
    <>
      <button onClick={handleClick}>
        Next
      </button>
      <h2>
        <i>{sculpture.name} </i> 
        by {sculpture.artist}
      </h2>
      <h3>  
        ({index + 1} of {sculptureList.length})
      </h3>
      <img 
        src={sculpture.url} 
        alt={sculpture.alt}
      />
      <p>
        {sculpture.description}
      </p>
    </>
  );
}
~~~
* 点击 “Next” 按钮切换当前雕塑

#### 第一个 Hook
[回到上一级](#state-组件的记忆)

* 在 React 中，useState 以及任何其他**以“use”开头**的函数都被称为 **Hook**
* Hook 是特殊的函数，只在 React 渲染时有效

注意事项：
* Hooks ——以 use 开头的函数——只能在组件或自定义 Hook 的**最顶层调用**
*  你不能在条件语句、循环语句或其他嵌套函数内调用 Hook

#### 剖析 useState
[回到上一级](#state-组件的记忆)

当你调用 useState 时，你是在告诉 React 你想让这个组件记住一些东西：

~~~tsx
const [index, setIndex] = useState(0);
~~~

useState 的唯一参数是 state 变量的**初始值**。在这个例子中，index 的初始值被useState(0)设置为 0。

<br>

每次你的组件渲染时，**useState** 都会给你一个包含两个值的数组：

1. state 变量 (index) 会**保存上次渲染的值**。
2. state setter 函数 (setIndex) 可以**更新 state 变量**并**触发 React 重新渲染**组件。

<br>

对于刚刚的示例，经历了以下过程：

1. **组件进行第一次渲染**。 因为你将 0 作为 index 的初始值传递给 useState，它将**返回 [0, setIndex]**。 React 记住 0 是最新的 state 值。
2. **你更新了 state**。当用户点击按钮时，它会调用 setIndex(index + 1)。 index 是 0，所以它是 **setIndex(1)**。这告诉 React 现在**记住 index 是 1** 并触发下一次渲染。
3. **组件进行第二次渲染**。React 仍然看到 useState(0)，但是因为 React **记住** 了你将 index 设置为了 1，它将**返回 [1, setIndex]**。
4. 以此类推！

#### 为一个组件添加多个 state 变量
[回到上一级](#state-组件的记忆)

~~~tsx
// import "./css/example.css"

import { useState } from 'react';
import { sculptureList } from './data/data';

export default function Gallery() {
  const [index, setIndex] = useState(0);
  // 增加是否展示更多细节的状态量
  const [showMore, setShowMore] = useState(false)

  function handleClick() {
    setIndex(index + 1);
  }

  function handleMoreClick(){
    setShowMore(!showMore)
  }

  let sculpture = sculptureList[index];
  return (
    <>
      <button onClick={handleClick}>
        Next
      </button>
      <h2>
        <i>{sculpture.name} </i> 
        by {sculpture.artist}
      </h2>
      <h3>  
        ({index + 1} of {sculptureList.length})
      </h3>

      {/* 增加是否展示详细信息的按钮 */}
      <button onClick={handleMoreClick}>
        {showMore ? 'Hide' : 'show'} details
      </button>
      {showMore && <p>{sculpture.description}</p>}

      <br/>

      <img 
        src={sculpture.url} 
        alt={sculpture.alt}
      />
    </>
  );
}
~~~
* 你可以在一个组件中拥有**任意多种类型的 state 变量**。
* 该组件有两个 state 变量，一个**数字 index** 和一个**布尔值 showMore**，点击 “Show Details” 会改变 showMore 的值

#### State 的底层逻辑
[回到上一级](#state-组件的记忆)


在同一组件的每次渲染中，**Hooks 都依托于一个稳定的调用顺序**
~~~js
let componentHooks = [];
let currentHookIndex = 0;

// useState 在 React 中是如何工作的（简化版）
function useState(initialState) {
  let pair = componentHooks[currentHookIndex];
  if (pair) {
    // 这不是第一次渲染
    // 所以 state pair 已经存在
    // 将其返回并为下一次 hook 的调用做准备
    currentHookIndex++;
    return pair;
  }

  // 这是我们第一次进行渲染
  // 所以新建一个 state pair 然后存储它
  pair = [initialState, setState];

  function setState(nextState) {
    // 当用户发起 state 的变更，
    // 把新的值放入 pair 中
    pair[0] = nextState;
    updateDOM();
  }

  // 存储这个 pair 用于将来的渲染
  // 并且为下一次 hook 的调用做准备
  componentHooks[currentHookIndex] = pair;
  currentHookIndex++;
  return pair;
}

function Gallery() {
  // 每次调用 useState() 都会得到新的 pair
  const [index, setIndex] = useState(0);
  const [showMore, setShowMore] = useState(false);

  function handleNextClick() {
    setIndex(index + 1);
  }

  function handleMoreClick() {
    setShowMore(!showMore);
  }

  let sculpture = sculptureList[index];
  // 这个例子没有使用 React，所以
  // 返回一个对象而不是 JSX
  return {
    onNextClick: handleNextClick,
    onMoreClick: handleMoreClick,
    header: `${sculpture.name} by ${sculpture.artist}`,
    counter: `${index + 1} of ${sculptureList.length}`,
    more: `${showMore ? 'Hide' : 'Show'} details`,
    description: showMore ? sculpture.description : null,
    imageSrc: sculpture.url,
    imageAlt: sculpture.alt
  };
}

function updateDOM() {
  // 在渲染组件之前
  // 重置当前 Hook 的下标
  currentHookIndex = 0;
  let output = Gallery();

  // 更新 DOM 以匹配输出结果
  // 这部分工作由 React 为你完成
  nextButton.onclick = output.onNextClick;
  header.textContent = output.header;
  moreButton.onclick = output.onMoreClick;
  moreButton.textContent = output.more;
  image.src = output.imageSrc;
  image.alt = output.imageAlt;
  if (output.description !== null) {
    description.textContent = output.description;
    description.style.display = '';
  } else {
    description.style.display = 'none';
  }
}

let nextButton = document.getElementById('nextButton');
let header = document.getElementById('header');
let moreButton = document.getElementById('moreButton');
let description = document.getElementById('description');
let image = document.getElementById('image');
let sculptureList = [{
  name: 'Homenaje a la Neurocirugía',
  artist: 'Marta Colvin Andrade',
  description: 'Although Colvin is predominantly known for abstract themes that allude to pre-Hispanic symbols, this gigantic sculpture, an homage to neurosurgery, is one of her most recognizable public art pieces.',
  url: 'https://i.imgur.com/Mx7dA2Y.jpg',
  alt: 'A bronze statue of two crossed hands delicately holding a human brain in their fingertips.'  
}, {
  name: 'Floralis Genérica',
  artist: 'Eduardo Catalano',
  description: 'This enormous (75 ft. or 23m) silver flower is located in Buenos Aires. It is designed to move, closing its petals in the evening or when strong winds blow and opening them in the morning.',
  url: 'https://i.imgur.com/ZF6s192m.jpg',
  alt: 'A gigantic metallic flower sculpture with reflective mirror-like petals and strong stamens.'
}, {
  name: 'Eternal Presence',
  artist: 'John Woodrow Wilson',
  description: 'Wilson was known for his preoccupation with equality, social justice, as well as the essential and spiritual qualities of humankind. This massive (7ft. or 2,13m) bronze represents what he described as "a symbolic Black presence infused with a sense of universal humanity."',
  url: 'https://i.imgur.com/aTtVpES.jpg',
  alt: 'The sculpture depicting a human head seems ever-present and solemn. It radiates calm and serenity.'
}, {
  name: 'Moai',
  artist: 'Unknown Artist',
  description: 'Located on the Easter Island, there are 1,000 moai, or extant monumental statues, created by the early Rapa Nui people, which some believe represented deified ancestors.',
  url: 'https://i.imgur.com/RCwLEoQm.jpg',
  alt: 'Three monumental stone busts with the heads that are disproportionately large with somber faces.'
}, {
  name: 'Blue Nana',
  artist: 'Niki de Saint Phalle',
  description: 'The Nanas are triumphant creatures, symbols of femininity and maternity. Initially, Saint Phalle used fabric and found objects for the Nanas, and later on introduced polyester to achieve a more vibrant effect.',
  url: 'https://i.imgur.com/Sd1AgUOm.jpg',
  alt: 'A large mosaic sculpture of a whimsical dancing female figure in a colorful costume emanating joy.'
}, {
  name: 'Ultimate Form',
  artist: 'Barbara Hepworth',
  description: 'This abstract bronze sculpture is a part of The Family of Man series located at Yorkshire Sculpture Park. Hepworth chose not to create literal representations of the world but developed abstract forms inspired by people and landscapes.',
  url: 'https://i.imgur.com/2heNQDcm.jpg',
  alt: 'A tall sculpture made of three elements stacked on each other reminding of a human figure.'
}, {
  name: 'Cavaliere',
  artist: 'Lamidi Olonade Fakeye',
  description: "Descended from four generations of woodcarvers, Fakeye's work blended traditional and contemporary Yoruba themes.",
  url: 'https://i.imgur.com/wIdGuZwm.png',
  alt: 'An intricate wood sculpture of a warrior with a focused face on a horse adorned with patterns.'
}, {
  name: 'Big Bellies',
  artist: 'Alina Szapocznikow',
  description: "Szapocznikow is known for her sculptures of the fragmented body as a metaphor for the fragility and impermanence of youth and beauty. This sculpture depicts two very realistic large bellies stacked on top of each other, each around five feet (1,5m) tall.",
  url: 'https://i.imgur.com/AlHTAdDm.jpg',
  alt: 'The sculpture reminds a cascade of folds, quite different from bellies in classical sculptures.'
}, {
  name: 'Terracotta Army',
  artist: 'Unknown Artist',
  description: 'The Terracotta Army is a collection of terracotta sculptures depicting the armies of Qin Shi Huang, the first Emperor of China. The army consisted of more than 8,000 soldiers, 130 chariots with 520 horses, and 150 cavalry horses.',
  url: 'https://i.imgur.com/HMFmH6m.jpg',
  alt: '12 terracotta sculptures of solemn warriors, each with a unique facial expression and armor.'
}, {
  name: 'Lunar Landscape',
  artist: 'Louise Nevelson',
  description: 'Nevelson was known for scavenging objects from New York City debris, which she would later assemble into monumental constructions. In this one, she used disparate parts like a bedpost, juggling pin, and seat fragment, nailing and gluing them into boxes that reflect the influence of Cubism’s geometric abstraction of space and form.',
  url: 'https://i.imgur.com/rN7hY6om.jpg',
  alt: 'A black matte sculpture where the individual elements are initially indistinguishable.'
}, {
  name: 'Aureole',
  artist: 'Ranjani Shettar',
  description: 'Shettar merges the traditional and the modern, the natural and the industrial. Her art focuses on the relationship between man and nature. Her work was described as compelling both abstractly and figuratively, gravity defying, and a "fine synthesis of unlikely materials."',
  url: 'https://i.imgur.com/okTpbHhm.jpg',
  alt: 'A pale wire-like sculpture mounted on concrete wall and descending on the floor. It appears light.'
}, {
  name: 'Hippos',
  artist: 'Taipei Zoo',
  description: 'The Taipei Zoo commissioned a Hippo Square featuring submerged hippos at play.',
  url: 'https://i.imgur.com/6o5Vuyu.jpg',
  alt: 'A group of bronze hippo sculptures emerging from the sett sidewalk as if they were swimming.'
}];

// 使 UI 匹配当前 state
updateDOM();
~~~
* componentHooks用于**记住**所有hook状态
* currentHookIndex用于**读取**当前hook状态
* 每次调用`updateDOM()`时，会将currentHookIndex**归零**，同时会调用`Gallery()`，currentHookIndex从0开始**按顺序**依次读取componentHooks中的hook状态，刷新UI
* 也就是说，react无需记住每个状态的名字匹配状态，只需要每次**按顺序**调用`useState()`即可

#### State 是隔离且私有的
[回到上一级](#state-组件的记忆)

* State 是屏幕上组件实例内部的状态。
* 换句话说，如果你渲染同一个组件两次，每个副本都会有**完全隔离**的 state！改变其中一个**不会影响**另一个。
* 如果你希望两个画廊保持其 states 同步怎么办？在 React 中执行此操作的正确方法是**从子组件中删除 state** 并将其**添加到离它们最近的共享父组件**中

### 渲染和提交
[回到上一级](#添加交互)

#### 步骤一：触发一次渲染
[回到上一级](#渲染和提交)

有两种原因会导致组件的渲染:

1. 组件的 **初次渲染**。
2. 组件（或者其祖先之一）的 **状态发生了改变**。

##### 初次渲染
[回到上一级](#步骤一：触发一次渲染)

当**应用启动时**，会触发初次渲染。框架和沙箱有时会隐藏这部分代码，但它是通过调用 `createRoot` 方法并传入目标 DOM 节点，然后用你的组件调用 `render` 函数完成的：

Image.jsx
~~~tsx
export default function Image() {
  return (
    <img
      src="https://i.imgur.com/ZF6s192.jpg"
      alt="'Floralis Genérica' by Eduardo Catalano: a gigantic metallic flower sculpture with reflective petals"
    />
  );
}
~~~

index.jsx
~~~tsx
import Image from './Image.js';
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'))
root.render(<Image />);
~~~
* `createRoot` 允许在浏览器的 DOM 节点中创建根节点以显示 React 组件
* 试着注释掉 root.render()，然后你将会看到组件消失

另外，你需要在根路径下修改 `index.html` 文件，将 body 标签下的 script 标签的 src 属性改为 index.jsx 的路径，才能使其生效

##### 状态更新时重新渲染
[回到上一级](#步骤一：触发一次渲染)
一旦组件被初次渲染，你就可以通过使用 **set 函数** 更新其状态来触发之后的渲染。更新组件的状态会自动将一次渲染送入队列。

#### 步骤二：react渲染你的组件
[回到上一级](#渲染和提交)
在你触发渲染后，React 会调用你的组件来确定要在屏幕上显示的内容。**“渲染中” 即 React 在调用你的组件**。

* 在进行**初次渲染**时, React 会调用**根组件**。
* 对于**后续的渲染**, React 会调用内部状态更新**触发了渲染的函数组件**。

这个过程是**递归**的：
* 如果更新后的组件会**返回某个另外的组件**，那么 React 接下来就会渲染 **那个** 组件
* 而如果**那个**组件又返回了**某个组件**，那么 React 接下来就会渲染 **那个** 组件，以此类推
* 这个过程会持续下去，直到**没有更多的嵌套组件**并且 React 确切知道哪些东西应该显示到屏幕上为止

index.jsx
~~~tsx
import { createRoot } from "react-dom/client";
import Gallery from "./Gallery";

const root = createRoot(document.getElementById('root')!)
root.render(<Gallery/>)
~~~

Gallery.jsx
~~~tsx
export default function Gallery() {
    return (
        <section>
            <h1>Gallery</h1>
            <Image />
            <Image />
            <Image />
        </section>
    )
}

function Image() {
    return (
        <img
            src="https://i.imgur.com/ZF6s192.jpg"
            alt="'Floralis Genérica' by Eduardo Catalano: a gigantic metallic flower sculpture with reflective petals"
        />
    );
}
~~~
* 在**初次渲染**中， React 将会为`<section>`、`<h1>` 和三个 `<img>` 标签 创建 DOM 节点。
* 在一次**重渲染过程**中, React 将**计算**它们的哪些属性（如果有的话）**自上次渲染以来已更改**。在下一步（提交阶段）之前，它不会对这些信息执行任何操作。
* 渲染必须是纯计算：相同输入，相同输出，并且只做自己的事情

#### 步骤三：react将更改提交到DOM上
[回到上一级](#渲染和提交)

在渲染（调用）你的组件之后，React 将会修改 DOM。

* 对于**初次渲染**，React 会使用 appendChild() DOM API 将其创建的所有 DOM 节点放在屏幕上。
* 对于**重渲染**，React 将应用最少的必要操作（在渲染时计算！），以使得 DOM 与最新的渲染输出相互匹配。

#### 尾声：浏览器绘制
[回到上一级](#渲染和提交)
在渲染完成并且 React 更新 DOM 之后，浏览器就会重新绘制屏幕

### State: 如同一张快照
[回到上一级](#添加交互)

#### 设置State会触发渲染
[回到上一级](#state-如同一张快照)

~~~tsx
import { useState } from "react";

export default function Form(){
  const [isSent, setIsSent] = useState(false);
  const [message, setMessage] = useState("hi");
  if (isSent){
    return <h1>Your message is on its way!</h1>
  }
  return (
    <form onSubmit={e =>{
      e.preventDefault();
      setIsSent(true);
      sentMessage(message);
    }}>
      <textarea
      placeholder="message"
      value={message}
      onChange={e => setMessage(e.target.value)}/>
      <button type="submit">Send</button>
    </form>
  )
}

function sentMessage(message: string){
  console.log(message);
}
~~~
当你单击按钮时会发生以下情况：

1. 执行 onSubmit 事件处理函数。
2. setIsSent(true) 将 isSent 设置为 true 并排列一个新的渲染。
3. React 根据新的 isSent 值**重新渲染**组件。

#### 渲染会及时生成一张快照
[回到上一级](#state-如同一张快照)

“正在渲染” 就意味着 React 正在调用你的组件——一个函数。你从该函数返回的 JSX 就像是在**某个时间点上 UI 的快照**。它的 props、事件处理函数和内部变量都是 **根据当前渲染时的 state** 被计算出来的

当 React 重新渲染一个组件时：

1. React 会**再次调用你的函数**
2. 函数会返回**新的 JSX 快照**
3. React 会**更新界面**以匹配返回的快照

这是一个例子：你可能会以为点击“+3”按钮会调用 setNumber(number + 1) 三次从而使计数器递增三次
~~~tsx
import { useState } from "react";

export default function Counter(){
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 1)
        setNumber(number + 1)
        setNumber(number + 1)
      }}>+3</button>
    </>
  )
}
~~~
* 请注意，每次点击**只会让 number 递增一次！**
* 设置 state 只会**为下一次渲染**变更 state 的值。
* 在第一次渲染期间，number 为 0，在第二次渲染之前，number 都会是 0
* 这也就解释了为什么在 **那次渲染中的 onClick 处理函数中**，即便在调用了 setNumber(number + 1) 之后，number 的值也仍然是 0
* 尽管你调用了三次 setNumber(number + 1)，但在 **这次渲染的** 事件处理函数中 number 会一直是 0，所以你会**三次将 state 设置成 1**
* 由于 React 的批处理机制，这三个setNumber 被视为**同一次渲染**

这次渲染等同于：
~~~tsx
<button onClick={() => {
  setNumber(0 + 1);
  setNumber(0 + 1);
  setNumber(0 + 1);
}}>+3</button>
~~~

#### 随时间变化的State
[回到上一级](#state-如同一张快照)

~~~tsx
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5);
        setTimeout(() => {
          alert(number);
        }, 3000);
      }}>+5</button>
    </>
  )
}
~~~
* 一个 state 变量的值**永远不会在一次渲染的内部发生变化**， 即使其事件处理函数的代码是**异步**的
* 在 **那次渲染的** onClick 内部，number 的值即使在调用 setNumber(number + 5) 之后也还是 0
* React 会使 state 的值**始终“固定”在一次渲染的各个事件处理函数内部**

### 把一系列 state 更新加入队列
[回到上一级](#添加交互)

#### React 会对 state 更新进行批处理 
[回到上一级](#把一系列-state-更新加入队列)

* React 会等到事件处理函数中的**所有代码都运行完毕**再处理你的 state 更新
* 只有在你的事件处理函数及其中**任何代码执行完成**之后，UI 才会更新
* 这种特性也就是**批处理**

#### 在下次渲染前多次更新同一个state
[回到上一级](#把一系列-state-更新加入队列)

如果你想在下次渲染之前**多次更新同一个 state**，你可以像 `setNumber(n => n + 1)` 这样传入一个根据队列中的前一个 state 计算下一个 state 的 **函数**，而不是像 `setNumber(number + 1)` 这样传入 **下一个 state 值**

~~~tsx
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={(e) => {
        e.preventDefault();
        setNumber(number => number + 1)
        setNumber(number => number + 1)
        setNumber(number => number + 1)
      }}>+3</button>
    </>
  )
}
~~~
* 在这里，`n => n + 1` 被称为 **更新函数**
* 当你将它传递给一个 state 设置函数时：

    1. React 会将此函数**加入队列**，以便在事件处理函数中的所有其他代码运行后进行处理。
    2. 在下一次渲染期间，React 会**遍历队列**并给你更新之后的最终 state。

#### 如果你在替换 state 后更新 state 会发生什么 
[回到上一级](#把一系列-state-更新加入队列)

~~~tsx
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={(e) => {
        e.preventDefault();
        setNumber(number + 5)
        setNumber(number => number + 1)
      }}>+6</button>
    </>
  )
}
~~~
* 这是事件处理函数告诉 React 要做的事情：

    1. setNumber(number + 5)：number 为 0，所以 setNumber(0 + 5)。React 将 **“替换为 5”** 添加到其队列中。
    2. setNumber(n => n + 1)：n => n + 1 是一个更新函数。 React 将 **该函数** 添加到其队列中。

|更新队列| n |返回值|
|---|---|---|
|“替换为 5”|	0（未使用）|	5|
|n => n + 1|	5	|5 + 1 = 6|

* `setState(x)`实际上相当于`setState(n => x)`

#### 如果你在更新 state 后替换 state 会发生什么 
[回到上一级](#把一系列-state-更新加入队列)

~~~tsx
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={(e) => {
        e.preventDefault();
        setNumber(number + 5);
        setNumber(number => number + 1);
        setNumber(42);
      }}>+6</button>
    </>
  )
}
~~~
* 以下是 React 在执行事件处理函数时处理这几行代码的过程：

    1. setNumber(number + 5)：number 为 0，所以 setNumber(0 + 5)。React 将 “替换为 5” 添加到其队列中。
    2. setNumber(n => n + 1)：n => n + 1 是一个更新函数。React 将该函数添加到其队列中。
    3. setNumber(42)：React 将 “替换为 42” 添加到其队列中。

|更新队列| n |返回值|
|---|---|---|
|“替换为 5”|	0（未使用）|	5|
|n => n + 1|	5	|5 + 1 = 6|
|“替换为 42”|	6（未使用）|	42|

#### 命名惯例
[回到上一级](#把一系列-state-更新加入队列)

1. 使用State变量的首字母
2. 使用State变量的全称


### 更新 state 中的对象
[回到上一级](#添加交互)

state 中可以保存任意类型的 JavaScript 值，包括**对象**。但是，你**不应该直接修改**存放在 React state 中的对象。相反，当你想要更新一个对象时，你需要**创建一个新的对象**（或者将其拷贝一份），然后将 state 更新为此对象。

#### 将 state 视为只读的
[回到上一级](#更新-state-中的对象)

在下面的例子中，我们用一个存放在 state 中的对象来表示指针当前的位置。当你在预览区触摸或移动光标时，红色的点本应移动。但是实际上红点**仍停留在原处**：
~~~tsx
import { useState } from 'react';

export default function Counter() {
  const [position, setPosition] = useState({
    x: 0,
    y: 0,
  })

  return (
    <>
      <div 
        onPointerMove={ e => {
          position.x = e.clientX;
          position.y = e.clientY;
        }}
        style={{
          position: 'relative',
          width: '100vw',
          height: '100vh'
        }}
      >
        <div
          style={{
            position: 'absolute',
            backgroundColor: 'red',
            borderRadius: '50%',
            transform: `translate(${position.x}px, ${position.y}px)`,
            left: -20,
            top: -20,
            width: 20,
            height: 20, // 等价20px，但在css中，这种写法是不允许的
          }}
        />
      </div>
    </>
  )
}
~~~
问题出在下面这段代码中：

~~~tsx
onPointerMove={ e => {
  position.x = e.clientX;
  position.y = e.clientY;
}}
~~~
* 这段代码虽然将现在指针的位置赋予了position对象，但是由于并**没有使用 setter 函数**，React 并不知道position对象已经改变，因此 React **没有做出任何响应**

正确方式应该是**创建一个新对象**，然后将 state 更新为新对象：
~~~tsx
onPointerMove={ e => {
  setPosition({
    x: e.clientX,
    y: e.clientY,
  })
}}
~~~
* 通过使用 setPosition，你在告诉 React：
    * 使用这个新的对象**替换** position 的值
    * 然后**再次渲染**这个组件

#### 使用展开语法复制对象
[回到上一级](#更新-state-中的对象)

但是通常，你会希望把 **现有** 数据作为你所创建的新对象的一部分。例如，你可能只想要更新表单中的**一个字段**，其他的字段仍然**使用之前的值**。

下面的代码中，输入框并不会正常运行，因为 onChange 直接修改了 state ：
~~~tsx
import React, { useState } from 'react';

export default function Form() {
  const [person, setPerson] = useState({
    firstName: 'Barbara',
    lastName: 'Hepworth',
    email: 'bhepworth@sculpture.com'
  });

  // 不同于jsx，tsx需要指定事件e的类型为ChangeEvent<HTMLInputElement>
  function handleFirstNameChange(e: React.ChangeEvent<HTMLInputElement>) {
    person.firstName = e.target.value;
  }

  function handleLastNameChange(e: React.ChangeEvent<HTMLInputElement>) {
    person.lastName = e.target.value;
  }

  function handleEmailChange(e: React.ChangeEvent<HTMLInputElement>) {
    person.email = e.target.value;
  }

  return (
    <>
      <label>
        First name:
        <input
          value={person.firstName}
          onChange={handleFirstNameChange}
        />
      </label>
      <label>
        Last name:
        <input
          value={person.lastName}
          onChange={handleLastNameChange}
        />
      </label>
      <label>
        Email:
        <input
          value={person.email}
          onChange={handleEmailChange}
        />
      </label>
      <p>
        {person.firstName}{' '}
        {person.lastName}{' '}
        ({person.email})
      </p>
    </>
  );
}
~~~

正确写法仍是创建一个新对象，然后将 state 改变为新对象：

~~~tsx
function handleFirstNameChange(e: React.ChangeEvent<HTMLInputElement>) {
  setPerson({
    firstName: e.target.value,
    lastName: person.lastName,
    email: person.email
  })
}
~~~

不过，可以使用展开语法`...对象名`来简化对象的创建（或者说是**复制**），只需要重新赋值**需要更改的字段**即可：
~~~tsx
function handleFirstNameChange(e: React.ChangeEvent<HTMLInputElement>) {
  setPerson({
    ...person,
    firstName: e.target.value,
  })
}
~~~
* 需要注意的是，展开语法是**浅拷贝**--只会复制一层，在**嵌套属性**中，需要**多次使用**展开语法

更进一步，你也可以在对象的定义中使用 `[` 和 `]` 括号来实现属性的动态命名:
~~~tsx
import React, { useState } from 'react';

export default function Form() {
  const [person, setPerson] = useState({
    firstName: 'Barbara',
    lastName: 'Hepworth',
    email: 'bhepworth@sculpture.com'
  });

  function handleInputChange(e: React.ChangeEvent<HTMLInputElement>) {
    setPerson({
      ...person,
      [e.target.name]: e.target.value
    })
  }

  return (
    <>
      <label>
        First name:
        <input
          name='firstName'
          value={person.firstName}
          onChange={handleInputChange}
        />
      </label>
      <label>
        Last name:
        <input
          name='lastName'
          value={person.lastName}
          onChange={handleInputChange}
        />
      </label>
      <label>
        Email:
        <input
          name='email'
          value={person.email}
          onChange={handleInputChange}
        />
      </label>
      <p>
        {person.firstName}{' '}
        {person.lastName}{' '}
        ({person.email})
      </p>
    </>
  );
}
~~~
* `name`属性**必须**与对象属性**相同**
* 这样，不同的输入框就能通过`name`属性区分，一个处理函数就能动态地为不同输入框的对应属性赋值

#### 更新一个嵌套对象 
[回到上一级](#更新-state-中的对象)

~~~tsx
import React, { useState } from 'react';

export default function Form() {
  const [person, setPerson] = useState({
    name: 'Niki de Saint Phalle',
    artwork: {
      title: 'Blue Nana',
      city: 'Hamburg',
      image: 'https://i.imgur.com/Sd1AgUOm.jpg',
    }
  });

  function handleNameChange(e: React.ChangeEvent<HTMLInputElement>) {
    setPerson({
      ...person,
      name: e.target.value
    });
  }

  function handleTitleChange(e: React.ChangeEvent<HTMLInputElement>) {
    setPerson({
      ...person,
      artwork: {
        ...person.artwork,
        title: e.target.value
      }
    });
  }

  function handleCityChange(e: React.ChangeEvent<HTMLInputElement>) {
    setPerson({
      ...person,
      artwork: {
        ...person.artwork,
        city: e.target.value
      }
    });
  }

  function handleImageChange(e: React.ChangeEvent<HTMLInputElement>) {
    setPerson({
      ...person,
      artwork: {
        ...person.artwork,
        image: e.target.value
      }
    });
  }

  return (
    <>
      <label>
        Name:
        <input
          value={person.name}
          onChange={handleNameChange}
        />
      </label>
      <label>
        Title:
        <input
          value={person.artwork.title}
          onChange={handleTitleChange}
        />
      </label>
      <label>
        City:
        <input
          value={person.artwork.city}
          onChange={handleCityChange}
        />
      </label>
      <label>
        Image:
        <input
          value={person.artwork.image}
          onChange={handleImageChange}
        />
      </label>
      <p>
        <i>{person.artwork.title}</i>
        {' by '}
        {person.name}
        <br />
        (located in {person.artwork.city})
      </p>
      <img 
        src={person.artwork.image} 
        alt={person.artwork.title}
      />
    </>
  );
}
~~~
* 更新嵌套属性和上面的并没有很大区别，只需要在**更新嵌套属性**时，使用**多个展开语法**即可

#### 使用 Immer 编写简洁的更新逻辑 
[回到上一级](#更新-state-中的对象)

由 Immer 提供的 draft 是一种**特殊类型的对象**，被称为 Proxy，它会记录你用它所进行的操作。这就是你能够随心所欲地直接修改对象的原因所在！从原理上说，Immer 会**弄清楚 draft 对象的哪些部分被改变了**，并会依照你的修改**创建出一个全新的对象**。

* 尝试使用 Immer:

    1. 运行 `npm install use-immer` 添加 Immer 依赖
    2. 用 `import { useImmer } from 'use-immer'` 替换掉 `import { useState } from 'react'`

下面我们把上面的例子用 Immer 实现一下：
~~~tsx
import { useImmer } from 'use-immer';

export default function Form() {
  const [person, updatePerson] = useImmer({
    name: 'Niki de Saint Phalle',
    artwork: {
      title: 'Blue Nana',
      city: 'Hamburg',
      image: 'https://i.imgur.com/Sd1AgUOm.jpg',
    }
  });

  function handleNameChange(e: React.ChangeEvent<HTMLInputElement>) {
    updatePerson(draft => {
      draft.name = e.target.value;
    });
  }

  function handleTitleChange(e: React.ChangeEvent<HTMLInputElement>) {
    updatePerson(draft => {
      draft.artwork.title = e.target.value;
    });
  }

  function handleCityChange(e: React.ChangeEvent<HTMLInputElement>) {
    updatePerson(draft => {
      draft.artwork.city = e.target.value;
    });
  }

  function handleImageChange(e: React.ChangeEvent<HTMLInputElement>) {
    updatePerson(draft => {
      draft.artwork.image = e.target.value;
    });
  }

  return (
    <>
      <label>
        Name:
        <input
          value={person.name}
          onChange={handleNameChange}
        />
      </label>
      <label>
        Title:
        <input
          value={person.artwork.title}
          onChange={handleTitleChange}
        />
      </label>
      <label>
        City:
        <input
          value={person.artwork.city}
          onChange={handleCityChange}
        />
      </label>
      <label>
        Image:
        <input
          value={person.artwork.image}
          onChange={handleImageChange}
        />
      </label>
      <p>
        <i>{person.artwork.title}</i>
        {' by '}
        {person.name}
        <br />
        (located in {person.artwork.city})
      </p>
      <img 
        src={person.artwork.image} 
        alt={person.artwork.title}
      />
    </>
  );
}
~~~

### 更新 state 中的数组
[回到上一级](#添加交互)

数组是另外一种可以存储在 state 中的 JavaScript 对象，它虽然是可变的，但是却**应该被视为不可变**。同对象一样，当你想要更新存储于 state 中的数组时，你需要**创建一个新的数组**（或者创建一份已有数组的拷贝值），并使用新数组设置 state。

#### 在没有 mutation 的前提下更新数组 
[回到上一级](#更新-state-中的数组)

##### 向数组中添加元素
[回到上一级](#在没有-mutation-的前提下更新数组)

在添加元素时，应该**避免使用 push() 方法【将元素插到数组结尾】和 unshift() 方法【将元素加入数组开头】**，而是应该使用**展开语法**创建一个新的数组，再用 setter 来设置 state

~~~tsx
import { useState } from "react";

let nextId = 0;

interface Artist {
  id: number;
  name: string;
}
export default function List(){
  const [name, setName] = useState('');
  const [artists, setArtists] = useState<Artist[]>([]);

  return (
    <>
      <h1>振奋人心的艺术家：</h1>
      <input
        value={name}
        onChange={e => setName(e.target.value)}
      />
      <button onClick={() => {
        setArtists([
          ...artists,
          {id: nextId++, name: name}
        ])
      }}>添加</button>
      <ul>
        {artists.map(artist => (
          <li key={artist.id}>
            {artist.name}
          </li>
        ))}
      </ul>
    </>
  )
}
~~~
* **注意**：在tsx中，数组类型的 state 需要使用泛型来定义，如 `const [artists, setArtists] = useState<Artist[]>([])`，若使用 `const [artists, setArtists] = useState([{} as Artist])` 类型欺骗，则在初始化是会存在一个**空对象**
* 数组展开运算符还允许将新添加的元素放在**数组开头**，如：

~~~tsx
<button onClick={() => {
  setArtists([
    {id: nextId++, name: name},
    ...artists,
  ])
}}>添加</button>
~~~

* 也可以改成 useImmer 版本的
~~~tsx
const [artists, updateArtists] = useImmer<Artist[]>([]);
~~~
* 这样就能使用 push() 方法和 unshift() 方法来添加元素了
~~~tsx
<button onClick={() => {
  updateArtists(artists => {
    artists.unshift({
      id: nextId++,
      name: name,
    });
  });
}}>添加</button>
~~~

##### 从数组中删除元素
[回到上一级](#在没有-mutation-的前提下更新数组)
从数组中删除元素需要避免使用 pop() 方法，shift() 方法 ，splice() 方法

从数组中删除一个元素最简单的方法就是**将它过滤出去**。换句话说，你需要生成一个不包含该元素的新数组。这可以通过 filter 方法实现，例如：
~~~tsx
import { useState } from "react";

let nextId = 0;

interface Artist {
  id: number;
  name: string;
}
export default function List() {
  const [name, setName] = useState('');
  const [artists, setArtists] = useState<Artist[]>([]);

  // 往数组中添加元素
  const handleAdd = () => {
    setArtists([
      ...artists,
      { id: nextId++, name: name },
    ]);
  }

  // 从数组中删除元素
  const handleDelete = (artistId: number) => {
    setArtists(artists.filter(artist => artist.id !== artistId));
  }

  return (
    <>
      <h1>振奋人心的艺术家：</h1>
      <input
        value={name}
        onChange={e => setName(e.target.value)}
      />
      <button onClick={handleAdd}>添加</button>
      <ul>
        {artists.map(artist => (
          <>
            <li key={artist.id}>
              {artist.id}{' '}{artist.name}{' '}
            </li>
            <button onClick={() => handleDelete(artist.id)}>删除</button>
          </>
        ))}
      </ul>
    </>
  )
}
~~~
* 这里，`artists.filter(a => a.id !== artist.id)` 表示**创建一个新的数组，该数组由那些 `ID` 与 `artists.id` 不同的 artists 组成**
* 需要注意的是 filter() 方法中，传入的箭头函数若用大括号包裹，则**返回值必须用 return 显式返回**，否则会返回 undefined，导致所有元素都不符合条件，都会被过滤掉；或者**去掉大括号**

##### 转换数组
[回到上一级](#在没有-mutation-的前提下更新数组)

如果你想改变数组中的某些或全部元素，你可以用 `map()` **创建一个新数组**

在下面的例子中，一个数组记录了两个圆形和一个正方形的坐标。当你点击按钮时，仅有两个圆形会向下移动 50px。这是通过使用 `map()` **生成一个新数组**实现的。

~~~tsx
import { useState } from "react"

let initialShapes = [
  {id: 0, type: 'circle', x: 50, y: 100},
  {id: 1, type: 'square', x: 150, y: 100},
  {id: 2, type: 'circle', x: 250, y: 100},
]

export default function ShapeEditor(){
  const [shapes, setShapes] = useState(initialShapes)

  function handleClick(){
    // 使用map方法创建新的数组
    setShapes(shapes.map(shape => {
      if (shape.type === 'square'){
        return shape;
      }else{
        return {...shape, y: shape.y + 50}
      }
    }))
  }

  return (
    <>
      <button onClick={handleClick}>所有圆形下移</button>
      {shapes.map(shape => (
        <div
          key={shape.id}
          style={{
            background: 'purple',
            position: 'absolute',
            left: shape.x,
            top: shape.y,
            borderRadius: shape.type === 'circle'? '50%' : '',
            width: 20,
            height: 20,
          }}
        />
      ))}
    </>
  )
}
~~~
* **注意**：`map()` 方法会**返回一个新数组**，而不是修改原数组。

##### 替换数组中的元素
[回到上一级](#在没有-mutation-的前提下更新数组)

想要**替换数组中一个或多个元素**是非常常见的。类似 `arr[0] = 'bird'` 这样的赋值语句会直接修改原始数组，所以在这种情况下，你也应该使用 map。

~~~tsx
import { useState } from "react"

let initialCounters = [
  0, 0, 0,
]

export default function CounterList(){
  const [counters, setCounters] = useState(initialCounters)

  function handleClick(i: number){
    setCounters(counters.map((counter, index) => {
      if(index === i){
        return counter + 1
      }else{
        return counter
      }
    }))
  }

  return (
    <ul>
      {counters.map((counter, index) => (
        <li key={index}>
          {counter}
          <button onClick={() => handleClick(index)}>+1</button>
        </li>
      ))}
    </ul>
  )
}
~~~
* map() 方法中第一个参数是**数组元素**，第二个参数是**索引**

##### 向数组指定位置插入元素
[回到上一级](#在没有-mutation-的前提下更新数组)

有时，你也许想向数组特定位置插入一个元素，这个位置既不在数组开头，也不在末尾。为此，你可以将**数组展开运算符** `...` 和 `slice()` 方法一起使用。

slice() 方法让你从数组中切出“一片”。为了将元素插入数组，你需要先**展开原数组在插入点之前的切片**，然后**插入新元素**，最后**展开原数组中剩下的部分**。

~~~tsx
import { useState } from "react"

let nextId = 3;

let initialArtists = [
  {id: 0, name: 'Martin'},
  {id: 1, name: 'James'},
  {id: 2, name: 'Jerry'},
];

interface Position {
  position: number;
}

export default function List(){
  const [name, setName] = useState('');
  const [artists, setArtists] = useState(initialArtists);
  const [position, setPosition] = useState(0);

  function handleClick(){
    setArtists([
      ...artists.slice(0, position),
      {id: nextId++, name: name},
      ...artists.slice(position)
    ])
  }

  return (
    <>
      <input
        value={name}
        onChange={e => setName(e.target.value)}
      />
      <input
        type="number"
        value={position}
        onChange={e => setPosition(Number(e.target.value))}
      />
      <button onClick={handleClick}>插入</button>
      <ul> 
        {artists.map(artist => (
          <li key={artist.id}>
            {artist.name}
          </li>
        ))}
      </ul>
    </>
  )
}
~~~
* `...artists.slice(0, position)` 表示**从数组开头到插入点之前的元素**
* `...artists.slice(position)` 表示**从插入点之后的元素**
* 注意：input 标签的输入必须经过`Number()` 转换为数字，否则和 `position` 的类型不同，会导致报错

##### 翻转数组和数组排序
[回到上一级](#在没有-mutation-的前提下更新数组)

JavaScript 中的 `reverse()` 和 `sort()` 方法**会改变原数组**，所以你无法直接使用它们。

然而，你可以**先拷贝**这个数组，再改变这个拷贝后的值。

翻转数组的例子：
~~~tsx
import { useState } from "react"

let initialArtists = [
  {id: 0, name: 'Martin'},
  {id: 2, name: 'James'},
  {id: 1, name: 'Jerry'},
];

export default function List(){
  const [artists, setArtists] = useState(initialArtists);

  function handleClick(){
    setArtists([...artists].reverse()) // 排序: return 小于0, 则a排前面, 否则b排前面
  }

  return (
    <>
      <button onClick={handleClick}>翻转</button>
      <ul> 
        {artists.map(artist => (
          <li key={artist.id}>
            {artist.name}
          </li>
        ))}
      </ul>
    </>
  )
}
~~~

数组排序的例子：
~~~tsx
import { useState } from "react"

let initialArtists = [
  {id: 0, name: 'Martin'},
  {id: 2, name: 'James'},
  {id: 1, name: 'Jerry'},
];

export default function List(){
  const [artists, setArtists] = useState(initialArtists);

  function handleClick(){
    setArtists([...artists].sort((a,b) => b.id - a.id)) // 排序: return 小于0, 则a排前面, 否则b排前面
  }

  return (
    <>
      <button onClick={handleClick}>排序</button>
      <ul> 
        {artists.map(artist => (
          <li key={artist.id}>
            {artist.name}
          </li>
        ))}
      </ul>
    </>
  )
}
~~~
* sort() 方法进行数字排序时，传入方法参数 `(a,b) => b.id - a.id`，return 小于0, 则a排前面, 否则b排前面

##### 更新数组内部的对象
[回到上一级](#在没有-mutation-的前提下更新数组)

对象并不是 *真的* 位于数组“内部”。可能他们在代码中看起来像是在数组“内部”，但其实数组中的每个对象都是**这个数组“指向”的一个存储于其它位置的值**。这就是当你在处理类似 `list[0]` 这样的嵌套字段时需要格外小心的原因。其他人的艺术品清单**可能指向了数组的同一个元素**！

在下面的例子中，两个不同的艺术品清单有着相同的初始 state。他们**本应该互不影响**，但是因为一次 mutation，他们的 state 被**意外地共享**了，勾选一个清单中的事项会影响另外一个清单：
~~~tsx
import { useState } from "react";

let nextId = 3;
const initialList = [
  {id: 0, title: 'Learn React', seen: false},
  {id: 1, title: 'Apply for jobs', seen: true},
  {id: 2, title: 'Profit!', seen: false},
]

export default function BucketList() {
  const [myList, setMyList] = useState(initialList);
  const [yourList, setYourList] = useState(initialList);

  function handleToggleMyList(artworkId: number, nextSeen: boolean){
    const myNextList = [...myList];
    const artwork = myNextList.find(a => a.id === artworkId);
    if(artwork) artwork.seen = nextSeen;
    setMyList(myNextList);
  }

  function handleToggleYourList(artworkId: number, nextSeen: boolean){
    const yourNextList = [...yourList];
    const artwork = yourNextList.find(a => a.id === artworkId);
    if(artwork) artwork.seen = nextSeen;
    setYourList(yourNextList);
  }

  return(
    <>
      <h1>想看的艺术品清单</h1>
      <h2>我想看的</h2>
      <ItemList
        artworks={myList}
        onToggle={handleToggleMyList}
      />
      <h2>你想看的</h2>
      <ItemList
        artworks={yourList}
        onToggle={handleToggleYourList}
      />
    </>
  )
}

interface ItemListProps {
  artworks: {id: number, title: string, seen: boolean}[];
  onToggle(artworkId: number, nextSeen: boolean): void;
}

function ItemList({artworks, onToggle}: ItemListProps){
  return(
    <ul>
      {artworks.map(artwork => (
        <li key={artwork.id}>
          <label>
            <input
              type="checkbox"
              checked={artwork.seen}
              onChange={e => {
                onToggle(artwork.id, e.target.checked);
              }}
            />
            {artwork.title}
          </label>
        </li>
      ))}
    </ul>
  )
}
~~~
问题出现在这里：
~~~tsx
const myNextList = [...myList];
const artwork = myNextList.find(a => a.id === artworkId);
if(artwork) artwork.seen = nextSeen;
setMyList(myNextList);
~~~
* 虽然 myNextList 这个数组是新的，但是其**内部的元素本身**与原数组 myList 是相同的。
* 因此，修改 artwork.seen，其实是在**修改原始的 artwork 对象**

你可以使用 `map` 在没有 mutation 的前提下**将一个旧的元素替换成更新的版本**：
~~~tsx
const myNextList = myList.map(artwork => {
  if (artwork.id === artworkId){
    return {...artwork, seen: nextSeen}
  }else{
    return artwork;
  }
})
setMyList(myNextList);
~~~

##### 使用 Immer 来处理数组
[回到上一级](#在没有-mutation-的前提下更新数组)

如果你不想改变 state 的数据结构，你也许会更喜欢使用 Immer ，它让你可以继续使用方便的，但会直接修改原值的语法，并负责**为你生成拷贝值**。

当使用 Immer 时，类似 `artwork.seen = nextSeen` 这种会产生 mutation 的语法**不会再有任何问题**了。

这是因为你并不是在直接修改原始的 state，而是在修改 Immer 提供的一个**特殊的 draft 对象**，在幕后，Immer 总是会根据你对 draft 的修改来**从头开始构建下一个 state**

## 状态管理
[回到目录](#目录)

### 用 State 响应输入
[回到上一级](#状态管理)

#### 声明式 UI 与命令式 UI 的比较 
[回到上一级](#用-state-响应输入)

告诉计算机如何去更新 UI 的编程方式被称为**命令式编程**
* 当你开发一个让用户提交答案的表单时，**命令式编程**的逻辑会如下：
  * 当你向表单输入数据时，“提交”按钮会随之变成**可用状态**
  * 当你点击“提交”后，表单和提交按钮都会随之变成**不可用状态**，并且会加载动画会随之**出现**
  * 如果网络请求成功，表单会随之**隐藏**，同时“提交成功”的信息会随之**出现**
  * 如果网络请求失败，错误信息会随之**出现**，同时表单又变为**可用状态**

声明你想要显示的内容，计算机通过计算得出该如何去更新 UI，这种方式被称为**声明式 UI**

#### 声明式地考虑 UI
[回到上一级](#用-state-响应输入)

React 是声明式的，使用声明式的思想实现上述逻辑：

1. **定位**你的组件中不同的视图状态
2. **确定**是什么触发了这些 state 的改变
3. **表示**内存中的 state（需要使用 useState）
4. **删除**任何不必要的 state 变量
5. **连接**事件处理函数去设置 state

##### 步骤一：定位组件中不同的视图状态
[回到上一级](#声明式地考虑-ui)

首先，你需要去可视化 UI 界面中用户**可能看到的所有不同的“状态”**：
* **无数据**：表单有一个不可用状态的“提交”按钮。
* **输入中**：表单有一个可用状态的“提交”按钮。
* **提交中**：表单完全处于不可用状态，加载动画出现。
* **成功时**：显示“成功”的消息而非表单。
* **错误时**：与输入状态类似，但会多错误的消息。

~~~tsx
export default function form({ status }: any) {
    if (status === 'success') {
        return (
            <h1>That's right!</h1>
        )
    }

    return (
        <>
            <h1>猜猜看呐！</h1>
            <p>随便猜吧</p>
            <form>
                <textarea disabled={status === 'submitting'} />
                <button disabled={status === 'submitting' || status === 'empty'}>我猜！</button>
            </form>
            {status === 'error' && (
                <p>很好的答案，可惜错了</p>
            )}
        </>
    )
}
~~~

你也可以使用 map 方法将这个表单的所有状态展示出来：
~~~tsx
import { useState } from "react"
import Form from "./Form"

function App() {
  const [status, setStatus] = useState([
    'empty',
    'typing',
    'submitting',
    'success',
    'error'
  ])

  return (
    <>
      {status.map(s => (
        <section key={s} style={{border:'1px black solid'}}>
          <p>状态：{s}</p>
          <Form 
            status={s} 
          />
        </section>
      ))}
    </>
  )
  
}
export default App
~~~
* 类似这样的页面通常被称作 **“living styleguide”** 或 **“storybook”** 

##### 步骤二：确定是什么触发了这些状态的改变
[回到上一级](#声明式地考虑-ui)
你可以触发 state 的更新来响应两种输入：
* **人为输入**。比如点击按钮、在表单中输入内容，或导航到链接。
* **计算机输入**。比如网络请求得到反馈、定时器被触发，或加载一张图片。

以上两种情况中，**你必须设置 state 变量** 去更新 UI。对于正在开发中的表单来说，你需要改变 state 以响应几个不同的输入：
* **改变输入框中的文本时**（人为）应该根据输入框的内容是否是**空值**，从而决定将表单的状态从空值状态切换到输入中或切换回原状态。
* **点击提交按钮时**（人为）应该将表单的状态切换到**提交中**的状态。
* **网络请求成功后**（计算机）应该将表单的状态切换到**成功**的状态。
* **网络请求失败后**（计算机）应该将表单的状态切换到**失败**的状态，与此同时，显示错误信息。

##### 步骤三：通过 useState 表示内存中的 state 
[回到上一级](#声明式地考虑-ui)
state 的每个部分都是“处于变化中的”，并且**你需要让“变化的部分”尽可能的少**

* 先从**绝对必须存在**的状态开始
* 接下来，你需要一个状态变量来代表你想要显示的那个可视状态
* 如果你很难立即想出最好的办法，那就先**从添加足够多的 state 开始**，确保所有可能的视图状态都囊括其中

##### 步骤四：删除任何不必要的 state 变量
[回到上一级](#声明式地考虑-ui)
你的目的是防止出现在**内存中的 state 不代表任何你希望用户看到的有效 UI 的情况**
这有一些你可以问自己的， 关于 state 变量的问题：
* 这个 state 是否会导致矛盾？
* 相同的信息是否已经在另一个 state 变量中存在
* 你是否可以通过另一个 state 变量的相反值得到相同的信息？

##### 步骤五：连接事件处理函数以设置 state
[回到上一级](#声明式地考虑-ui)
最后，创建事件处理函数去设置 state 变量。
~~~tsx
import { useState } from "react"

export default function form() {
    const [status, setStatus] = useState('empty'); // typing || submitting || error || success
    const [answer, setAnswer] = useState('');
    const [error, setError] = useState<Error | null>(null);

    if (status === 'success') {
        return (
            <h1>That's right!</h1>
        )
    }

    async function handleSubmit(e: React.FormEvent<HTMLFormElement>){
        e.preventDefault();
        setStatus('submitting');
        try{
            await submitForm(answer);
            setStatus('success');
        }catch(error){
            setStatus('typing');
            setError(error as Error);
        }
    }

    return (
        <>
            <h1>猜猜看呐！</h1>
            <p>随便猜吧</p>
            <form onSubmit={handleSubmit}>
                <textarea 
                    value={answer}
                    disabled={status === 'submitting'} 
                    onChange={e => {
                        setAnswer(e.target.value);
                        if(e.target.value.length > 0){
                            setStatus('typing');
                        }else{
                            setStatus('empty');
                        }
                    }}
                />
                <button disabled={status === 'submitting' || status === 'empty'}>我猜！</button>
            </form>
            {error !== null && (
                <p>{error.message}</p>
            )}
        </>
    )
}

function submitForm(answer: string){
    return new Promise<void>((resolve, reject) => {
        setTimeout(() => {
            let shouldError = answer.trim().toLowerCase() !== '42';
            if (shouldError){
                reject(new Error('你猜的很好，但是不对捏😝'))
            }else{
                resolve()
            }
        }, 1500)
    })
}
~~~
* setTimeout 模拟网络请求
* 若请求成功且不需要返回数据，Promise对象需要**使用 void 泛型**
* 容易忽略的是在**改变输入时**需要检查输入字符串的长度并**调整 status 变量**，使按钮能够启用

### 选择 State 结构
[回到上一级](#状态管理)

> 构建良好的 state 可以让组件变得易于修改和调试，而不会经常出错。

#### 构建 State 的原则
[回到上一级](#选择-state-结构)

当你编写一个存有 state 的组件时，你需要选择使用多少个 state 变量以及它们都是怎样的数据格式。尽管选择次优的 state 结构下也可以编写正确的程序，但有几个原则可以指导你做出更好的决策：
1. **合并关联的 state**。如果你总是同时更新两个或更多的 state 变量，请考虑将它们合并为一个单独的 state 变量。
2. **避免互相矛盾的 state**。当 state 结构中存在多个相互矛盾或“不一致”的 state 时，你就可能为此会留下隐患。应尽量避免这种情况。
3. **避免冗余的 state**。如果你能在渲染期间从组件的 props 或其现有的 state 变量中计算出一些信息，则不应将这些信息放入该组件的 state 中。
4. **避免重复的 state**。当同一数据在多个 state 变量之间或在多个嵌套对象中重复时，这会很难保持它们同步。应尽可能减少重复。
5. **避免深度嵌套的 state**。深度分层的 state 更新起来不是很方便。如果可能的话，最好以扁平化方式构建 state。

#### 一、合并关联的 state
[回到上一级](#选择-state-结构)

类似这样的 state ：
~~~tsx
const [x, setX] = useState(0);
const [y, setY] = useState(0);
~~~
如果某两个 state 变量总是一起变化，则将它们统一成一个 state 变量可能更好：
~~~tsx
const [position, setPosition] = useState({x: 0, y: 0});
~~~
这样你就**不会忘记让它们始终保持同步**，就像下面这个例子中，移动光标会同时更新红点的两个坐标:
~~~tsx
import { useState } from "react";

export default function MovingDot() {
    const [position, setPosition] = useState({
        x: 0,
        y: 0
    });

    return (
        <div
            onMouseMove={e => {
                setPosition({
                    x: e.clientX,
                    y: e.clientY
                });
            }}
            style={{
                height: "100vh",
                width: "100vw"
            }}
        >
            <div
                style={{
                    height: "50px",
                    width: "50px",
                    background: "#ff0000",
                    borderRadius: "50%",
                    transform: `translate(${position.x}px, ${position.y}px)`,
                    position: "absolute",
                    left: -25,
                    top: -25
                }}
            />
        </div>
    )
}
~~~
* 如果只想改变其中一个坐标，**请不要忘记使用展开语法复制剩余的坐标**

#### 二、避免互相矛盾的 state
[回到上一级](#选择-state-结构)

下面是带有 isSending 和 isSent 两个 state 变量的酒店反馈表单：
~~~tsx
import { useState } from "react";

export default function FeedbackForm(){
    const [text, setText] = useState('');
    const [isSending, setIsSending] = useState(false);
    const [isSent, setIsSent] = useState(false);

    if (isSent) {
        return <h1>中!</h1>
    }

    return (
        <form>
            <h1>你想反馈啥？</h1>
            <textarea
                value={text}
                onChange={e => setText(e.target.value)}
            />
            <button
                disabled={isSending}
                onClick={async e => {
                    e.preventDefault();
                    setIsSending(true);
                    await submit(text);
                    setIsSending(false);
                    setIsSent(true);
                }}
            >发送</button>
            {isSending && <span>正在发送...</span>}
        </form>
    )
}

function submit(text: string){
    return new Promise<void>(resolve => {
        setTimeout(() => resolve(), 2000);
    })
}
~~~
* 尽管这段代码是有效的，但也会让一些 state “极难处理”。
* 例如，如果你忘记同时调用 setIsSent 和 setIsSending，则可能会出现 isSending 和 isSent 同时为 true 的情况。
* 因为 isSending 和 isSent 不应同时为 true，所以**最好用一个 status 变量来代替它们**，这个 state 变量可以采取三种有效状态其中之一：'typing' (初始), 'sending', 和 'sent':
~~~tsx
import { useState } from "react";

export default function FeedbackForm(){
    const [text, setText] = useState('');
    // const [isSending, setIsSending] = useState(false);
    // const [isSent, setIsSent] = useState(false);
    const [status, setStatus] = useState<'typing' | 'sending' | 'sent'>('typing');

    if (status === 'sent') {
        return <h1>中!</h1>
    }

    return (
        <form>
            <h1>你想反馈啥？</h1>
            <textarea
                value={text}
                onChange={e => setText(e.target.value)}
            />
            <button
                disabled={status === 'sending'}
                onClick={async e => {
                    e.preventDefault();
                    setStatus('sending');
                    await submit(text);
                    setStatus('sent');
                }}
            >发送</button>
            {status === 'sending' && <span>正在发送...</span>}
        </form>
    )
}

function submit(text: string){
    return new Promise<void>(resolve => {
        setTimeout(() => resolve(), 2000);
    })
}
~~~

#### 三、避免冗余的 state
[回到上一级](#选择-state-结构)
如果你能在渲染期间从组件的 props 或其现有的 state 变量中计算出一些信息，则不应该把这些信息放到该组件的 state 中。
~~~tsx
const [firstName, setFirstName] = useState('');
const [lastName, setLastName] = useState('');
const [fullName, setFullName] = useState('');
~~~
* 例如，fullName 可以通过 firstName 和 lastName 计算得出
~~~tsx
const [firstName, setFirstName] = useState('');
const [lastName, setLastName] = useState('');
const fullName = firstName + ' ' + lastName;
~~~
* 因此，更改处理程序不需要做任何特殊操作来更新它。
* 当你调用 setFirstName 或 setLastName 时，你会**触发一次重新渲染**，然后下一个 fullName 将从新数据中计算出来。

另一个常见的冗余 state 场景是**在 state 中镜像 props**：
~~~tsx
function Message({ messageColor }) {
  const [color, setColor] = useState(messageColor);
}
~~~
* 如果父组件稍后传递不同的 messageColor 值（例如，将其从 'blue' 更改为 'red'），**则 color state 变量将不会更新！** 
* state 仅在第一次渲染期间初始化。
* 相反，你要在代码中**直接使用 messageColor 属性**。
* 如果你想给它起一个更短的名称，请使用常量：
~~~tsx
function Message({ messageColor }) {
  const color = messageColor;
}
~~~
* 这种写法就**不会与从父组件传递的属性失去同步**。

只有当你 **想要忽略特定 props 属性的所有更新时**，将 props “镜像”到 state 才有意义。按照惯例，prop 名称**以 initial 或 default 开头**，以阐明该 prop 的新值将被忽略：
~~~tsx
function Message({ initialColor }) {
  // 这个 `color` state 变量用于保存 `initialColor` 的 **初始值**。
  // 对于 `initialColor` 属性的进一步更改将被忽略。
  const [color, setColor] = useState(initialColor);
}
~~~

#### 四、避免重复的 state
[回到上一级](#选择-state-结构)

下面这个菜单列表组件可以让你在多种旅行小吃中选择一个：
~~~tsx
import { useState } from "react";

export default function Menu() {
    const [items, setItems] = useState([
        { id: 0, name: 'Burger' },
        { id: 1, name: 'Pizza' },
        { id: 2, name: 'Salad' },
    ]);
    const [selectedItem, setSelectedItem] = useState(items[0]);

    return (
        <>
            <h1>你要吃啥</h1>
            <ul>
                {items.map(item => (
                    <li key={item.id}>
                        <input
                            value={item.name}
                            onChange={e => {
                                e.preventDefault();
                                setItems(items.map(i => {
                                    if (i.id === item.id) {
                                        return { ...i, name: e.target.value };
                                    } else {
                                        return i;
                                    }
                                }))
                            }}
                        />
                        <button
                            onClick={() => {
                                setSelectedItem(item)
                            }}
                        >
                            选择
                        </button>
                    </li>
                ))}
            </ul>
            <p>你选的是：{selectedItem.name}</p>
        </>
    )
}
~~~
* 当前，它将所选元素**作为对象**存储在 selectedItem state 变量中。
* 然而，这并不好：selectedItem 的内容与 items 列表中的某个项**是同一个对象**。 
* 这意味着关于该项本身的信息在两个地方**产生了重复**。
* 如果你首先单击菜单上的“Choose” 然后 编辑它，输入会更新，但**底部的标签不会反映编辑内容**。 这是因为**你有重复的 state**，并且你忘记更新了 selectedItem
* 尽管你也可以更新 selectedItem，但更简单的解决方法是**消除重复项**：
~~~tsx
import { useState } from "react";

export default function Menu() {
    const [items, setItems] = useState([
        { id: 0, name: 'Burger' },
        { id: 1, name: 'Pizza' },
        { id: 2, name: 'Salad' },
    ]);
    const [selectedId, setSelectedId] = useState(0);

    const selectedItem = items.find(item => item.id === selectedId);

    return (
        <>
            <h1>你要吃啥</h1>
            <ul>
                {items.map(item => (
                    <li key={item.id}>
                        <input
                            value={item.name}
                            onChange={e => {
                                e.preventDefault();
                                setItems(items.map(i => {
                                    if (i.id === item.id) {
                                        return { ...i, name: e.target.value };
                                    } else {
                                        return i;
                                    }
                                }))
                            }}
                        />
                        <button
                            onClick={() => {
                                setSelectedId(item.id)
                            }}
                        >
                            选择
                        </button>
                    </li>
                ))}
            </ul>
            <p>你选的是：{selectedItem?.name}</p>
        </>
    )
}
~~~
* 现在，如果你编辑 selected 元素，下面的消息将**立即更新**
* 这是因为 setItems 会触发重新渲染，而 items.find(...) 会找到带有更新文本的元素
* 你不需要在 state 中保存 **选定的元素**，因为只有 **选定的 ID** 是必要的。其余的可以在渲染期间计算。

#### 五、避免深度嵌套的 state
[回到上一级](#选择-state-结构)

想象一下，一个由**行星、大陆和国家**组成的旅行计划。你可能会尝试使用嵌套对象和数组来构建它的 state，就像下面这个例子：
~~~tsx
export const initialTravelPlan = {
  id: 0,
  title: '(Root)',
  childPlaces: [
    {
      id: 1,
      title: '地球',
      childPlaces: [
        {
          id: 2,
          title: '亚洲',
          childPlaces: [
            { id: 3, title: '中国', childPlaces: [] },
            { id: 4, title: '新加坡', childPlaces: [] },
          ]
        },
        {
          id: 5,
          title: '欧洲',
          childPlaces: [
            { id: 6, title: '法国', childPlaces: [] },
          ]
        },
      ]
    },
    {
      id: 7,
      title: '月球',
      childPlaces: [
        { id: 8, title: '第谷环形山', childPlaces: [] },
      ]
    }
  ]
};
~~~
* 假设你想添加一个按钮来删除一个你已经去过的地方，更新嵌套的 state 需要从更改部分**一直向上复制对象**。删除一个深度嵌套的地点将涉及**复制其整个父级地点链**。
* 这样的代码可能**非常冗长**。

如果 state 嵌套太深，难以轻松更新，可以**考虑将其“扁平化”**
* 不同于树状结构，每个节点的 place 都是一个包含 **其子节点** 的数组
* 你可以让每个节点的 place 作为数组保存 **其子节点的 ID**
* 然后存储一个节点 ID 与相应节点的**映射关系**
~~~tsx
export const initialTravelPlan = {
    0: {
        id: 0,
        title: '(Root)',
        childId: [1, 7],
    },
    1: {
        id: 1,
        title: '地球',
        childId: [2, 5],
    },
    2: {
        id: 2,
        title: '亚洲',
        childId: [3, 4],
    },
    3: {
        id: 3,
        title: '中国',
        childId: [],
    },
    4: {
        id: 4,
        title: '新加坡',
        childId: [],
    },
    5: {
        id: 5,
        title: '欧洲',
        childId: [6],
    },
    6: {
        id: 6,
        title: '法国',
        childId: [],
    },
    7: {
        id: 7,
        title: '月球',
        childId: [8],
    },
    8: {
        id: 8,
        title: '第谷环形山',
        childId: [],
    },
};
~~~
**现在 state 已经“扁平化”（也称为“规范化”）**，更新嵌套项会变得更加容易。

通过递归树，我们可以通过映射关系找到所有结点以及其子集：
~~~tsx
import type { PlaceTreeProps } from "./flattening_types";

/**
 * 这个组件的逻辑可以拆解为三步：
 * 1. 查字典：根据传入的 id，去大对象 placesById 中找到对应的数据。
 * 2. 渲染自己：显示标题和“去过了”按钮。
 * 3. 递归子级：如果有 childIds，就循环它们，再次调用 FlatteningPlaceTree。
 */
export default function FlatteningPlaceTree({
    id,
    parentId,
    placesById, // 这是存储所有地点的“大字典”
    onComplete
}: PlaceTreeProps) { 
    // 第一步：查找当前节点数据
    const place = placesById[id];
    
    // 防御性编程：万一 ID 不存在，直接返回 null 避免报错
    if (!place) return null;

    const childIds = place.childIds;

    return (
        <li style={{ marginBottom: '8px' }}>
            {/* 显示当前地点名称 */}
            {place.title}
            
            {/* 点击按钮时，告诉父级：请把我的 ID 从我父节点的 childIds 列表中移除 */}
            <button 
                style={{ marginLeft: '10px' }}
                onClick={() => onComplete(parentId, id)}
            >
                去过了
            </button>

            {/* 第三步：递归逻辑 */}
            {childIds.length > 0 && (
                <ol>
                    {childIds.map(childId => (
                        <FlatteningPlaceTree
                            key={childId}
                            id={childId}
                            parentId={place.id} // 对子级来说，我(place.id)就是它们的父级
                            placesById={placesById} // 继续向下传递大字典
                            onComplete={onComplete} // 继续向下传递回调函数
                        />
                    ))}
                </ol>
            )}
        </li>
    );
}
~~~
再通过主容器完成删除的逻辑：
~~~tsx
import { useState } from "react";
import { initialTravelPlan } from "./flattening_places";
import FlatteningPlaceTree from "./FlatteningPlaceTree";
import type { Plan } from "./flattening_types";

/**
 * 这是管理状态的“大脑”组件。
 * 扁平化结构让更新操作变得极其简单：
 * 你不需要去树的深处寻找 parent，只需要知道 parentId 就能直接在 state 中修改它。
 */
export default function FlatteningTravelPlan() {
    // 将整个大字典存入 state
    const [plan, setPlan] = useState<Plan>(initialTravelPlan);

    /**
     * 处理“完成”逻辑
     * @param parentId 触发点击的节点的父 ID
     * @param childId 触发点击的节点本身的 ID
     */
    function handleComplete(parentId: number, childId: number) {
        // 1. 直接从字典里取出父级对象
        const parent = plan[parentId];

        // 2. 创建父级的新副本，把那个“去过了”的 ID 过滤掉
        const nextParent = {
            ...parent,
            childIds: parent.childIds.filter(id => id !== childId)
        };

        // 3. 更新 state
        setPlan({
            ...plan,          // 展开原有的字典
            [parentId]: nextParent // 覆盖被修改的父级项
        });
    }

    // 找到入口点：ID 为 0 的根节点
    const root = plan[0];

    return (
        <div style={{ padding: '20px' }}>
            <h1>旅游计划（扁平化逻辑演示）</h1>
            <ol>
                {/* 从根节点的子 ID 开始第一层递归渲染 */}
                {root.childIds.map(id => (
                    <FlatteningPlaceTree
                        key={id}
                        id={id}
                        parentId={0} // 根节点的 ID 是 0
                        placesById={plan}
                        onComplete={handleComplete}
                    />
                ))}
            </ol>
        </div>
    );
}
~~~
* 值得注意的是，这里的删除仅仅是 **软删除** ，即只是删除了父元素与子元素之间的映射关系，没有真正在内存中删除这些元素
* 使用 useImmer 可以实现更简洁的真正删除：


~~~tsx
import { useState } from "react";
import { initialTravelPlan } from "./flattening_places";
import FlatteningPlaceTree from "./FlatteningPlaceTree";
import type { Plan } from "./flattening_types";
import { useImmer } from "use-immer";

/**
 * 这是管理状态的“大脑”组件。
 * 扁平化结构让更新操作变得极其简单：
 * 你不需要去树的深处寻找 parent，只需要知道 parentId 就能直接在 state 中修改它。
 */
export default function FlatteningTravelPlan() {
    // 将整个大字典存入 state
    const [plan, updatePlan] = useImmer<Plan>(initialTravelPlan);

    /**
     * 处理“完成”逻辑
     * @param parentId 触发点击的节点的父 ID
     * @param childId 触发点击的节点本身的 ID
     */
    function handleComplete(parentId: number, childId: number) {
        updatePlan(draft => {
            // 1. 逻辑删除：断开父子之间的 ID 引用
            const parent = draft[parentId];
            parent.childIds = parent.childIds.filter(id => id !== childId);

            // 2. 物理删除：定义一个递归函数，从字典中彻底抹除该节点及其所有后代
            function deleteAllChildren(id: number) {
                const place = draft[id];
                // 先处理它的子节点（顺藤摸瓜）
                place.childIds.forEach(deleteAllChildren);
                // 最后把自己从大字典中删除（物理清理）
                delete draft[id];
            }

            // 执行清理
            deleteAllChildren(childId);
        })
    }

    // 找到入口点：ID 为 0 的根节点
    const root = plan[0];

    return (
        <div style={{ padding: '20px' }}>
            <h1>旅游计划（扁平化逻辑演示）</h1>
            <ol>
                {/* 从根节点的子 ID 开始第一层递归渲染 */}
                {root.childIds.map(id => (
                    <FlatteningPlaceTree
                        key={id}
                        id={id}
                        parentId={0} // 根节点的 ID 是 0
                        placesById={plan}
                        onComplete={handleComplete}
                    />
                ))}
            </ol>
        </div>
    );
}
~~~


















































































