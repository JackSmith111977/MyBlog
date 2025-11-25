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

## JSX基础
[回到目录](#目录)

JSX 是 JavaScript 和 XML(HTML) 的缩写,表示在**JS代码中编写HTML的模板结构**,是react中编写UI模板的方式

优势:
* HTML的声明式模板写法
* JS的可编程能力

JSX是JS的语法拓展,需要**解析工具解析**之后才能在浏览器中运行

### JSX的定义和使用
[回到上一级](#jsx基础)

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
[回到上一级](#jsx基础)

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
[回到上一级](#jsx基础)

1. 只能返回一个根元素
* 如果想要在一个组件中包含**多个元素**，需要用一个**父标签**把它们包裹起来
* 如果你不想在标签中增加一个额外的 `<div>`，可以用 `<>` 和 `</>` 元素来代替, 这个空标签被称作 Fragment, **React Fragment** 允许你将子元素分组，而不会在 HTML 结构中添加额外节点

2. 标签必须闭合
3. 使用驼峰命名法给大部分属性命名
* 由于历史原因，`aria-*` 和 `data-*` 属性是以带 - 符号的 HTML 格式书写的

### JSX中使用JS表达式
[回到上一级](#jsx基础)

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

### JSX中渲染列表
[回到上一级](#jsx基础)

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

### 将Props传递给组件
[回到上一级](#jsx基础)

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

