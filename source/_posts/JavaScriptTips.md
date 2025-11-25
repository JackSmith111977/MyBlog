---
title: JavaScript 笔记
date: 2025-11-23 21:45:00
tags: 
    - 前端
    - JavaScript
cover: https://kei-blog.oss-cn-beijing.aliyuncs.com/asset/115945063_p0-cut.jpg
categories: 
    - 编程语言
---

# JavaScript 笔记
本文是基于[MDN Web Docs](https://mdn.org.cn), 同时结合本人的学习整理而成的笔记

## 目录
- [JavaScript 笔记](#javascript-笔记)
  - [目录](#目录)
  - [语法与类型](#语法与类型)
    - [声明](#声明)
    - [声明与初始化](#声明与初始化)
    - [变量作用域](#变量作用域)
      - [全局变量和局部变量](#全局变量和局部变量)
    - [变量提升](#变量提升)
    - [常量](#常量)
    - [数据结构和类型](#数据结构和类型)
      - [数据类型](#数据类型)
      - [数字与“+”运算符](#数字与运算符)
      - [将字符串转换为数字](#将字符串转换为数字)
        - [parseInt(string, radix)](#parseintstring-radix)
        - [parseFloat(string)](#parsefloatstring)
        - [Number(value)](#numbervalue)
        - [+value](#value)
    - [字面量](#字面量)
      - [数组字面量](#数组字面量)
      - [布尔字面量](#布尔字面量)
      - [数值字面量](#数值字面量)
        - [整数字面量](#整数字面量)
        - [浮点字面量](#浮点字面量)
      - [对象字面量](#对象字面量)
      - [正则表达式字面量](#正则表达式字面量)
      - [字符串字面量](#字符串字面量)

## 语法与类型
[回到目录](#目录)

### 声明
[回到上一级](#语法与类型)

* var: 声明一个变量，并可选择将其初始化为一个值。
* let: 声明一个**块作用域**的局部变量，并可选择将其初始化为一个值。
* const: 声明一个**块作用域**的**只读**命名常量
* 使用关键字 var。例如，var x = 42。此语法可用于声明**局部变量**和**全局变量**，具体取决于执行上下文
* 使用关键字 const 或 let。例如，let y = 13。此语法可用于声明块作用域的**局部变量**

### 声明与初始化
[回到上一级](#语法与类型)
* 在像 let x = 42 这样的语句中，let x 部分称为**声明**，= 42 部分称为**初始化器**
* 在 var 和 let 声明中，初始化器是可选的。如果变量未带初始化器声明，则它被赋值为 **undefined**
* const 声明**总是**需要一个初始化器，因为它们禁止在声明后进行任何类型的赋值，并且隐式地用 undefined 初始化很可能是程序员的错误

### 变量作用域
[回到上一级](#语法与类型)
* 全局作用域：脚本模式下所有代码的默认作用域。
* 模块作用域：模块模式下代码的作用域。
* 函数作用域：通过函数创建的作用域

此外，用 let 或 const 声明的变量可以属于一个额外的作用域

* 块作用域：由一对花括号（一个块）创建的作用域

#### 全局变量和局部变量
[回到上一级](#变量作用域)
* 当您在任何**函数之外**声明变量时，它被称为**全局变量**，因为它可用于当前文档中的任何其他代码
* 当您在**函数内部**声明变量时，它被称为**局部变量**，因为它仅在该函数内部可用
* let 和 const 声明也可以作用域到它们声明的**块语句**
~~~javascript
if (Math.random() > 0.5) {
  const y = 5;
}
console.log(y); // ReferenceError: y is not defined
~~~
* 然而，用 var 创建的变量不是块作用域的，而只是块所在函数（或全局作用域）的**局部变量**
~~~javascript
if (true) {
  var x = 5;
}
console.log(x); // x is 5
~~~

* 全局变量实际上是**全局对象**的属性
* 在网页中，全局对象是 window，因此您可以使用 window.variable 语法读取和设置全局变量
* 在所有环境中，globalThis 变量（它本身也是一个全局变量）可用于读取和设置全局变量。这是为了在各种 JavaScript 运行时之间提供一致的接口

### 变量提升
[回到上一级](#语法与类型)

* 用 var 声明的变量会被提升，这意味着您可以在其作用域中的**任何位置**引用该变量，即使其声明尚未到达
* 您可以将 var 声明视为被“提升”到其函数或全局作用域的**顶部**
* 但是，如果您在变量声明之前访问它，其值始终为 undefined，因为**只有其声明和默认初始化**（使用 undefined）会被提升，**而不是其值赋值**

~~~javascript
console.log(x === undefined); // true
var x = 3;

(function () {
  console.log(x); // undefined
  var x = "local value";
})();
~~~

~~~javascript
var x;
console.log(x === undefined); // true
x = 3;

(function () {
  var x;
  console.log(x); // undefined
  x = "local value";
})();
~~~
* 两种写法等价, 前者会被解释为后者
* 由于提升，函数中的所有 var 语句都应**尽可能地放置在函数的顶部**。这种最佳实践提高了代码的清晰度
* let 和 const 是否提升是定义上的争论。在变量声明之前在块中引用变量总是会导致 ReferenceError，因为该变量从块开始直到声明被处理为止都处于“暂时死区”中

### 常量
[回到上一级](#语法与类型)

* 您可以使用 **const 关键字**创建一个只读的命名常量
* 常量标识符的语法与任何变量标识符相同：它必须以字母、下划线或美元符号 ($) 开头，并且可以包含字母、数字或下划线字符
* 您不能在同一作用域中声明与函数或变量**同名**的常量
* 但是，const 只阻止重新赋值，而不阻止突变。赋值给常量的**对象的属性**不受保护
* 同样，数组的内容也不受保护

### 数据结构和类型
[回到上一级](#语法与类型)

#### 数据类型
[回到上一级](#数据结构和类型)
最新的 ECMAScript 标准定义了**八种数据类型**

* 七种**原始数据类型**
  1. 布尔值。true 和 false。
  2. null。表示空值的特殊关键字。（因为 JavaScript 区分大小写，所以 null 与 Null、NULL 或任何其他变体都不同。）
  3. undefined。一个顶级属性，其值未定义。
  4. 数字。整数或浮点数。例如：42 或 3.14159。
  5. BigInt。任意精度的整数。例如：9007199254740992n。
  6. 字符串。表示文本值的字符序列。例如："Howdy"。
  7. Symbol。一种实例唯一且不可变的数据类型。
* 以及**对象**
* JavaScript 是一种动态类型语言。这意味着您在声明变量时不必指定其数据类型。这也意味着数据类型在脚本执行期间会根据需要**自动转换**

#### 数字与“+”运算符
[回到上一级](#数据结构和类型)
* 在涉及数字和字符串值与 + 运算符的表达式中，JavaScript 将数字值转换为字符串
~~~javascript
x = "The answer is " + 42; // "The answer is 42"
y = 42 + " is the answer"; // "42 is the answer"
z = "37" + 7; // "377"
~~~
* 对于所有其他运算符，JavaScript 不将数字值转换为字符串
~~~javascript
"37" - 7; // 30
"37" * 7; // 259
~~~

#### 将字符串转换为数字
[回到上一级](#数据结构和类型)

##### parseInt(string, radix)
[回到上一级](#将字符串转换为数字)
~~~javascript
parseInt("101", 2); // 5
~~~
* string：要转换的字符串。
* radix：要使用的基数。即以radix为进制转换为十进制数
* 返回一个数字或NaN

##### parseFloat(string)
[回到上一级](#将字符串转换为数字)
~~~javascript
parseFloat("3.14"); // 3.14
~~~
* string：要转换的字符串。
* 返回一个数字或NaN

##### Number(value)
[回到上一级](#将字符串转换为数字)
~~~javascript
Number("123"); // returns the number 123
Number("123") === 123; // true

Number("unicorn"); // NaN
Number(undefined); // NaN
~~~
* value：要转换的值。
* 返回一个数字或NaN

##### +value
[回到上一级](#将字符串转换为数字)
~~~javascript
"1.1" + "1.1"; // '1.11.1'
(+"1.1") + (+"1.1"); // 2.2
// Note: the parentheses are added for clarity, not required.
~~~
* value：要转换的值。
* **隐式执行**数字转换，这与 Number() 函数的过程相同


### 字面量
[回到上一级](#数据结构和类型)
字面量表示 JavaScript 中的值。这些是固定值（不是变量），您在脚本中字面提供

- [数组字面量](#数组字面量)
- [布尔字面量](#布尔字面量)
- [数值字面量](#数值字面量)
- [对象字面量](#对象字面量)
- [正则表达式字面量](#正则表达式字面量)
- [字符串字面量](#字符串字面量)

#### 数组字面量
[回到上一级](#字面量)
* 数组字面量是由零个或多个**表达式**组成的列表，每个表达式代表一个**数组元素**，并用 **方括号 ([])** 括起来
* 当您使用数组字面量创建数组时，它将用指定的值初始化为其元素，并且其 **length** 设置为指定的参数数量
~~~javascript
const coffees = ["French Roast", "Colombian", "Kona"];
~~~

* 每次评估数组字面量时，它都会创建一个新的数组对象
* 在**全局作用域**中用字面量定义的数组在**脚本加载时创建一次**
* 但是，如果数组字面量在**函数内部**，则**每次调用**该函数时都会实例化一个新的数组

~~~javascript
const fish = ["Lion", , "Angel"];
console.log(fish);
// [ 'Lion', <1 empty item>, 'Angel' ]
~~~
* 如果您在数组字面量中连续放置两个逗号，数组会为未指定的元素留下一个**空槽**
* 请注意，第二个项目是“empty”，这与实际的 undefined 值并不完全相同。在使用 Array.prototype.map 等**数组遍历**方法时，空槽会被跳过。但是，通过**索引访问 fish[1]** 仍然会返回 undefined
* 如果您在元素列表**末尾**包含一个尾随逗号，则该逗号将被**忽略**
* 当您有一个多行数组时，尾随逗号有助于保持 **git diff** 干净，因为在末尾添加一个项目只增加一行，而不会修改前一行

#### 布尔字面量
[回到上一级](#字面量)
布尔类型有两个字面值：true 和 false
* 不要将原始布尔值 true 和 false 与 Boolean 对象的真值和假值混淆
* Boolean 对象是原始布尔数据类型的包装器

#### 数值字面量
[回到上一级](#字面量)

##### 整数字面量
[回到上一级](#数值字面量)

整数和 BigInt 字面量可以用十进制（基数 10）、十六进制（基数 16）、八进制（基数 8）和二进制（基数 2）书写


* *十进制*整数字面量是 without 一个前导 0（零）的数字序列。
* 整数字面量的**前导 0（零）** **，或前导 0o（或 0O）** 表示它是*八进制*。八进制整数字面量只能包含数字 0 – 7。
* **前导 0x（或 0X）** 表示一个*十六进制*整数字面量。十六进制整数可以包含数字（0 – 9）和字母 a – f 以及 A – F。（字符的大小写不会改变其值。因此：0xa = 0xA = 10 且 0xf = 0xF = 15。）
* **前导 0b（或 0B）** 表示一个*二进制*整数字面量。二进制整数字面量只能包含数字 0 和 1。
* 整数字面量上的**尾随 n 后缀**表示 *BigInt* 字面量。BigInt 字面量可以使用上述任何基数。请注意，不允许使用 0123n 这样的前导零八进制语法，但 0o123n 是可以的。
~~~javascript
0, 117, 123456789123456789n             (decimal, base 10)
015, 0001, 0o777777777777n              (octal, base 8)
0x1123, 0x00111, 0x123456789ABCDEFn     (hexadecimal, "hex" or base 16)
0b11, 0b0011, 0b11101001010101010101n   (binary, base 2)
~~~

##### 浮点字面量
[回到上一级](#数值字面量)
浮点字面量可以包含以下部分

* 一个无符号十进制整数，
* 一个小数点 (.)，
* 一个小数部分（另一个十进制数），
* 一个指数
指数部分是一个 e 或 E，后跟一个整数，该整数可以带符号（前缀 + 或 -）

~~~javascript
[digits].[digits][(E|e)[(+|-)]digits]
~~~
示例:
~~~javascript
3.1415926
.123456789
3.1E+12
.1e-23
~~~

#### 对象字面量
[回到上一级](#字面量)

对象字面量是一个由零个或多个**属性名及其关联值**组成的列表，用 **花括号 ({})** 括起来

~~~javascript
const sales = "Toyota";

function carTypes(name) {
  return name === "Honda" ? name : `Sorry, we don't sell ${name}.`;
}

const car = { 
  myCar: "Saturn", 
  getCar: carTypes("Honda"), 
  special: sales 
};

console.log(car.myCar); // Saturn
console.log(car.getCar); // Honda
console.log(car.special); // Toyota
~~~
* car 对象的第一个元素定义了一个属性 myCar，并为其赋值一个新字符串 "Saturn"
* 第二个元素 getCar 属性**立即被赋值**为调用函数 (carTypes("Honda")) 的结果
* 第三个元素 special 属性使用了一个现有变量 (sales)
~~~javascript
const car = { manyCars: { a: "Saab", b: "Jeep" }, 7: "Mazda" };

console.log(car.manyCars.b); // Jeep
console.log(car[7]); // Mazda
~~~
* 您可以将**数字或字符串**字面量用作属性的名称，或者将一个对象**嵌套**在另一个对象中
* 对象属性名称可以是任何字符串，包括空字符串。如果属性名称不是有效的 JavaScript 标识符或数字，则必须用引号括起来
* 标识符可以包含Unicode字母、$、_以及数字（0-9），但不能以数字开头

无效标识符的属性名称不能作为点 (.) 属性访问, 但可以作为方括号 ([]) 访问
~~~javascript
const unusualPropertyNames = {
  "": "An empty string",
  "!": "Bang!",
};

// 点访问
console.log(unusualPropertyNames.""); // SyntaxError: Unexpected string
console.log(unusualPropertyNames.!); // SyntaxError: Unexpected token !

// 中括号访问
console.log(unusualPropertyNames[""]); // An empty string
console.log(unusualPropertyNames["!"]); // Bang!
~~~

对象字面量支持一系列**简写语法**，包括在构造时设置原型、foo: foo 赋值的简写、定义方法、进行 super 调用以及使用表达式计算属性名称
~~~javascript
const obj = {
  // __proto__
  __proto__: theProtoObj,
  // Shorthand for 'handler: handler'
  handler,
  // Methods
  toString() {
    // Super calls
    return `d ${super.toString()}`;
  },
  // Computed (dynamic) property names
  ["prop_" + (() => 42)()]: 42,
};
~~~

#### 正则表达式字面量
[回到上一级](#字面量)
正则表达式字面量（将在稍后详细定义）是括在斜杠之间的模式。以下是正则表达式字面量的示例
~~~javascript
const re = /ab+c/;
~~~

#### 字符串字面量
[回到上一级](#字面量)
字符串字面量是零个或多个字符，用双引号 (") 或单引号 (') 括起来。字符串必须用相同类型的引号

* 您可以对字符串字面量值调用 String 对象的任何方法
* JavaScript 会自动将字符串字面量转换为**临时 String 对象**，调用方法，然后**丢弃**临时 String 对象

字符串字面量允许使用**模板字符串**
~~~javascript
// Basic literal string creation
`In JavaScript '\n' is a line-feed.`;

// Multiline strings
`In JavaScript, template strings can run
 over multiple lines, but double and single
 quoted strings cannot.`;

// String interpolation
const name = "Lev",
  time = "today";
`Hello ${name}, how are you ${time}?`;
~~~
* 模板字面量为构建字符串提供了语法糖

除了普通字符之外，您还可以在字符串中包含特殊字符
| 字符 | 描述 |
| --- | --- |
| \0 | 空字节 |
| \b | 退格键 |
| \f | 换页符 |
| \n | 换行符 |
| \r | 回车符 |
| \t | 制表符 |
| \v | 垂直制表符 |
| \' | 撇号或单引号 |
| \" | 双引号 |
| \\ | 反斜杠字符 |
| \XXX | 由最多三个八进制数字 XXX（介于 0 和 377 之间）指定的带有 Latin-1 编码的字符。例如，\251 是版权符号的八进制序列。 |
| \xXX | 由两个十六进制数字 XX（介于 00 和 FF 之间）指定的带有 Latin-1 编码的字符。例如，\xA9 是版权符号的十六进制序列。 |
| \uXXXX | 由四个十六进制数字 XXXX 指定的 Unicode 字符。例如，\u00A9 是版权符号的 Unicode 序列。请参阅Unicode 转义序列。 |
| \u{XXXXX} | Unicode 码点转义。例如，\u{2F804} 与 Unicode 转义 \uD87E\uDC04 相同。 |


















