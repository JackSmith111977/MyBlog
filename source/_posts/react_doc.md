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
































































