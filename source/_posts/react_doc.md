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

### JSX中使用JS表达式

