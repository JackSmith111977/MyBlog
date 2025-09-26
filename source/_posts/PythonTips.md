---
title: "Python笔记(持续更新中)"
date: 2025/09/06 19:56:00
tags: Python
updated:
cover: https://kei-blog.oss-cn-beijing.aliyuncs.com/asset/130997637_p0-cut.jpg
categories:
  - 编程语言
---

<style>
.table-container {
  overflow-x: auto;
  margin: 1em 0;
}

table {
  min-width: 100%;
  display: block;
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
}

th, td {
  padding: 8px 12px;
  text-align: left;
  white-space: nowrap;
}
</style>

# Python 笔记

## 目录
> 1. [列表 list](#列表-list)
> 2. [元组 tuple](#元组-tuple)
> 3. [字符串 string](#字符串-string)
> 4. [集合 set](#集合-set)
> 5. [字典 dict](#字典-dict)
> 6. [容器 container](#容器-container)
> 7. [文件 file](#文件)
> 8. [正则表达式](#正则表达式)
> 9. [异常 Exception](#异常-exception)
> 10. [模块 module](#模块-module)
> 11. [json](#json)
> 12. [pyecharts](#pyecharts)
> 13. [面向对象 OOP](#面向对象)


## 列表 list

[返回目录](#目录)

### 列表方法
<div class="table-container">

|方法|描述|返回值|
|---|---|---|
|`index(item)`|返回列表中指定元素的索引|`索引`,item不存在返回`error`|
|`insert(index,item)`|在列表指定位置插入元素，此位置后的元素右移|`None`|
|`append(item)`|在列表末尾添加元素|`None`|
|`extend(list)`|在列表末尾添加列表元素|`None`|
|`del list[index]`|删除列表指定位置的元素|`None`|
|`pop(index)`|删除列表指定位置的元素，并返回该元素|`元素`|
|`remove(item)`|删除列表中指定元素的第一个匹配项|`None`|
|`clear()`|清空列表|`None`|
|`count(item)`|返回列表中指定元素的个数|`个数`|
|`len(list)`|返回列表的长度|`长度`|
|`reverse()`|列表倒序|`None`|
|**`list[begin:end:step]`**|切片，返回列表指定范围的元素|`列表`|

</div>


## 元组 tuple

[返回目录](#目录)

### 元组方法
<div class="table-container">

|方法|描述|返回值|
|---|---|---|
|`index(item)`|返回元组中指定元素的索引|`索引`,item不存在返回`error`|
|`count(item)`|返回元组中指定元素的个数|`个数`|
|`len(tuple)`|返回元组的长度|`长度`|
|**`tuple[begin:end:step]`**|切片，返回元组指定范围的元素|`元组`|

</div>


### 注意事项
1. 元组不可变，不能添加、删除、修改元素
2. 元组中的列表仍然可变，原因在于元组中的列表属于引用类型，列表属于对象，对象都是引用类型


## 字符串 string

[返回目录](#目录)

### 字符串方法
<div class="table-container">

|方法|描述|返回值|
|---|---|---|
|`index(item)`|返回字符串中指定元素起始位置的索引|`索引`,item不存在返回`error`|
|`replace(old,new)`|替换字符串中指定元素，原字符串不变|`替换后的字符串`|
|`split(sep)`|将字符串按指定分隔符分隔，返回列表|`列表`|
|`strip([chars])`|去除字符串头尾的指定字符，默认参数为`空格`|`字符串`|
|`count(item)`|返回字符串中指定元素的个数|`个数`|
|`len(string)`|返回字符串的长度|`长度`|
|**`string[begin:end:step]`**|切片，返回字符串指定范围的元素|`字符串`|

</div>


### 注意事项
1. 字符串不可变，不能添加、删除、修改元素

## 集合 set

[返回目录](#目录)

### 集合方法
<div class="table-container">

|方法|描述|返回值|
|---|---|---|
|`add(item)`|添加元素|`None`|
|`remove(item)`|删除元素|`None`|
|`pop()`|删除并返回集合中的第一个元素|`元素`|
|`clear()`|清空集合|`None`|
|`difference(set)`|返回两个集合的差集，即主类有而参数类没有的元素|`集合`|
|`difference_update(set)`|删除两个集合的交集元素，主类改变而参数类不变|`None`|
|`union(set)`|返回两个集合的并集，该并集是新集合，原集合不变|`集合`|
|`len(set)`|返回集合的长度|`长度`|

</div>

### 注意事项
1. 集合中的元素不能重复
2. 集合是无序的，不能通过索引访问元素

## 字典 dict

[返回目录](#目录)

### 字典方法
<div class="table-container">

|方法|描述|返回值|
|---|---|---|
|`dict[key] = value`|添加元素，若key已存在，则更新value|`None`|
|`pop(key)`|删除指定key的元素，并返回该元素|`元素`|
|`clear()`|清空字典|`None`|
|`keys()`|返回字典中所有key的列表|`列表`|
|`len(dict)`|返回字典的长度|`长度`|

</div>

## 容器 container

[返回目录](#目录)

list,tuple,string,set,dict都是容器

### 容器通用方法
<div class="table-container">

|方法|描述|返回值|
|---|---|---|
|`len(container)`|返回容器的长度|`长度`|
|`max(container)`|返回容器中的最大值|`最大值`|
|`min(container)`|返回容器中的最小值|`最小值`|
|`sorted(container, [reverse=False])`|返回容器中的排序后的元素，reverse为True时，返回排序后的元素逆序|`排序后的元素list`|

</div>

### 容器类型转换
1. 转`list`：字典中的**key值**转为list元素；字符串的每个**字符**转为list元素
2. 转`tuple`：字典中的**key值**转为tuple元素；字符串的每个**字符**转为tuple元素
3. 转`set`：字典中的**key值**转为set元素；字符串的每个**字符**转为set元素


## 文件

[返回目录](#目录)

### 文件的读取
|方法|描述|返回值|参数说明|
|---|---|---|---|
|`open(filename, mode='<r>', encoding='<utf-8>')`|打开文件|`文件对象`|filename: 文件名或文件路径 <br> mode: 文件打开模式 <br> encoding: 文件编码 <br> `r`: 只读模式 <br> `w`: 只写模式，同名文件存在则覆盖，不存在则创建 <br> `a`: 追加模式，同名文件存在则追加，不存在则创建|
|`with open(filename, mode='<r>', encoding='<utf-8>') as f:`|打开文件，在运行完此语句块时自动关闭文件|`None`|f: `open()`返回的对象|
|`file.read([size])`|读取文件内容|`文件内容`|size: 读取指定字节数，默认为-1，表示读取所有内容|
|`file.readline()`|读取文件的一行内容|`行内容`|
|`file.readlines()`|读取文件所有行内容|`行内容list`|
|`for line in file:`|循环读取文件内容|`None`|
|`file.close()`|关闭文件|`None`|

### 文件的写入
|方法|描述|返回值|参数说明|
|---|---|---|---|
|`file.write(string)`|写入文件内容，并不会立刻写入文件，而是先写入缓冲区|`None`|`string`: 要写入的内容|
|`file.flush()`|将缓冲区中的内容写入文件，`close()`函数包含`flush()`功能|`None`|


## 正则表达式

[返回目录](#目录)

### 常用元符号
|正则表达式符号|含义|
|---|---|
|`.`|匹配任意字符，除了换行符|
|`*`|匹配0个或多个前面的字符|
|`+`|匹配1个或多个前面的字符|
|`?`|匹配0个或1个前面的字符|
|`^`|匹配字符串的开头|
|`$`|匹配字符串的结尾|
|`{n}`|匹配前一个字符n次|
|`{n,m}`|匹配前一个字符n到m次|
|`{n,}`|匹配前一个字符n次以上|

### 字符类
|字符类|含义|
|---|---|
|`[abc]`|匹配a或b或c|
|`[^abc]`|匹配除了a或b或c之外的字符|
|`[a-z]`|匹配a到z之间的任意字符|
|`[A-Z]`|匹配A到Z之间的任意字符|
|`[0-9]`|匹配0到9之间的任意字符|
|`\d`|匹配数字|
|`\w`|匹配字母、数字、下划线|
|`\s`|匹配空白字符|


## 异常 Exception

[返回目录](#目录)

### 捕获异常
~~~python
try:
    # 有可能抛出异常的代码段
except [(NameError, ZeroDivisionError, ...) as e]:
    # 捕获对应类型的异常，默认所有异常
    # 异常处理代码段
[else:
    """
    没有异常时执行代码段
    """
]
[finally:
    """
     finally代码段，无论是否有异常，都会执行
    """
]
~~~

### 异常的类型
> 详细请查阅官方文档：[Python异常处理](https://docs.python.org/3/library/exceptions.html#Exception)


|异常类型|描述|父类|
|---|---|---|
|`Exception`|所有异常的基类|`object`|
|`ArithmeticError`|数学运算异常|`Exception`|
|`OverflowError`|算术运算溢出异常|`ArithmeticError`|
|`ZeroDivisionError`|除数为0异常|`ArithmeticError`|
|`FloatingPointError`|浮点数异常|`ArithmeticError`|
|`AssertionError`|断言异常|`Exception`|
|`AttributeError`|属性异常|`Exception`|
|`BufferError`|缓冲区异常|`Exception`|
|`EOFError`|文件结束异常|`Exception`|
|`ImportError`|导入异常|`Exception`|
|`ModuleNotFoundError`|模块未找到异常|`ImportError`|
|`IndexError`|索引异常，下标超出范围|`Exception`|
|`KeyError`|键异常，键集中找不到映射|`Exception`|
|`LookupError`|查找异常|`Exception`|
|`MemoryError`|内存异常|`Exception`|
|`NameError`|名称异常，变量未定义|`Exception`|
|`NotImplementedError`|未实现异常|`Exception`|
|`OSError`|操作系统异常|`Exception`|
|`OverflowError`|算术运算溢出异常|`ArithmeticError`|


## 模块 module

[返回目录](#目录)

模块是Python代码文件，模块名就是文件名，其中定义了模块的变量、函数、类等，也可以包含可执行的代码

### 导入模块
~~~python
# from 模块名 import 变量名 后续可以直接使用变量名
# import 模块名 后续必须使用模块名.变量名
[from 模块名] import <模块变量|模块函数|模块类|*> [as 别名]
~~~

### 注意事项
1. 不同模块的同名函数，后导入的会覆盖先导入的
2. `__main__`变量在模块中可以直接运行，测试函数功能，但在导入模块中，`__main__`变量会失效
3. `__all__`变量，在模块或**__init__.py**中定义，用于指定模块的成员，只有被指定成员导入时，才会被`*`导入，未被指定只能使用变量名导入
~~~python
# __all__变量指定的语法
__all__ = ['变量名', '函数名', '类名']
~~~

### Python 包 package
包是模块的集合，包名就是文件夹名，包中的模块名就是文件夹中的文件名
#### 创建包
1. 创建文件夹，文件夹名就是包名
2. 在文件夹中创建**__init__.py__**文件

### 第三方包
|语法|描述|
|---|---|
|`pip install 包名`|安装包|


## json
[返回目录](#目录)

本质上是能在不同编程语言中互操作的数据格式，在python中为字符串
~~~python
import json
~~~
### json相关操作
|方法|描述|返回值|参数说明|
|---|---|---|---|
|`json.dumps(obj, ensure_ascii=False, indent=4)`|将Python对象转换为json字符串|`json字符串`|obj: 要转换的Python对象，可以是**字典**和嵌套字典的**列表** <br> ensure_ascii: 是否转义非ASCII字符，中文显示需要设置False <br> indent: 缩进级别|
|`json.loads(json_str)`|将json字符串转换为Python对象|`Python对象`|json_str: 要转换的json字符串|

## pyecharts
[返回目录](#目录)

[官方文档](https://gallery.pyecharts.org/#/Line/README)

~~~bash
pip install pyecharts
~~~

~~~python
from pyecharts[.<选项>] import <包名> [as <别名>]
~~~

### 全局配置选项
[官方文档](https://pyecharts.org/#/zh-cn/global_options)
~~~python
line.set_global_opts(
  title_opts=opts.TitleOpts(title="GDP"),
  ...
)
~~~
|参数|描述|构造参数|参数说明|
|---|---|---|---|
|`title_opts`|标题配置项|opts.TitleOpts(title="GDP")|title: 标题名称|
|`legend_opts`|图例配置项|opts.LegendOpts(is_show=False)|is_show: 是否显示图例|
|`tooltip_opts`|提示框配置项|opts.TooltipOpts(is_show=True)|is_show: 是否显示提示框|
|`toolbox_opts`|工具箱配置项|opts.ToolboxOpts(is_show=True)|is_show: 是否显示工具箱|
|`visualmap_opts`|视觉映射配置项|opts.VisualMapOpts(is_show=True)|is_show: 是否显示视觉映射|



### 基础折线图

~~~python
# 绘制基础折线图

## 导包
from pyecharts.charts import Line

## 得到折线图对象
line = Line()

## 添加数据
### x轴数据
line.add_xaxis(["中国", "美国", "日本", "印度", "法国", "英国", "俄罗斯", "意大利", "德国", "西班牙"])
### y轴数据
line.add_yaxis("GDP", [30, 25, 20, 15, 10, 5, 2, 1, 0.5, 0.1])

## 设置全局配置选项
line.set_global_opts(
    title_opts=opts.TitleOpts(title="GDP"),
    tooltip_opts=opts.TooltipOpts(is_show=True),
    legend_opts=opts.LegendOpts(is_show=True),
    toolbox_opts=opts.ToolboxOpts(is_show=True),
    visualmap_opts=opts.VisualMapOpts(is_show=True)
)

## 生成图表
line.render()
~~~


## 面向对象
[返回目录](#目录)

### 类
基本结构：
~~~python
class Student:
    # 成员变量
    name = None
    age = None
    gender = None
    
    # 成员方法，必须有self参数，表示当前对象
    def showname(self):
        print(self.name)
~~~
构造方法：
~~~python
# 构造方法
    def __init__(self, name, gender, age):
        self.name = name
        self.gender = gender
        self.age = age
        print("创建一个学生对象")
~~~
魔术方法：
|方法|描述|参数列表|返回值|
|---|---|---|---|
|`__init__(self, *args, **kwargs)`|构造方法|`self`: 当前对象 <br> `*args`: 可变参数 <br> `**kwargs`: 关键字参数|无|
|`__str__(self)`|对象转字符串|`self`: 当前对象|字符串|
|`__lt__(self, other)`|重写小于，用于对象某一规则的比较|`self`: 当前对象 <br> `other`: 其他对象|布尔值|
|`__le__(self, other)`|重写小于等于，用于对象某一规则的比较|`self`: 当前对象 <br> `other`: 其他对象|布尔值|
|`__eq__(self, other)`|重写等于，用于对象某一规则的比较|`self`: 当前对象 <br> `other`: 其他对象|布尔值|

~~~python
    # 魔术方法
    ## __str__(): 返回对象的字符串表示
    def __str__(self):
        return "name=%s,gender=%s,age=%s" % (self.name, self.gender, self.age)
    
    ## __lt__(): 用于类对象的小于比较(less than)
    def __lt__(self, other):
        return self.age < other.age
    
    ## __le__(): 用于类对象的小于等于比较(less than or equal)
    def __le__(self, other):
        return self.age <= other.age
    
    ## __eq__(): 用于类对象的等于比较(equal)，默认"=="比较的是内存地址
    def __eq__(self, other):
        return self.age == other.age
~~~

### 类的封装
**封装**：封装就是将**属性或方法私有化**，属性或方法私有化后，外部无法直接访问属性或方法，只能通过方法访问属性或方法

语法：
属性私有化：`__属性名`
方法私有化：`__方法名()`
受保护属性：`_属性名`
受保护方法：`_方法名()`

~~~python
class Student:
    # 私有成员变量
    __name = None
    __age = None
    __sex = None
    
    def __init__(self, name, age, sex):
        self.__name = name
        self.__age = age
        self.__sex = sex
    
    def show(self):
        print("姓名：%s, 年龄：%d, 性别：%s" % (self.__name, self.__age, self.__sex))
    
    def getName(self):
        return self.__name
    
    def getAge(self):
        return self.__age
    
    def getSex(self):
        return self.__sex
    
    # 私有成员方法
    def __setName(self, name):
        self.__name = name
    
    def __setAge(self, age):
        self.__age = age
        
    def __setSex(self, sex):
        self.__sex = sex
~~~

### 类的继承
继承：子类继承父类的属性和方法，并添加新的属性和方法
1. 不同于Java语言，Python语言支持多继承
2. 多继承时，父类同名属性或方法，会按照从左到右的顺序进行覆盖，即左边优先级更大
3. 子类只继承父类的**公有**或**受保护**属性和方法，不会继承父类的私有属性或方法
4. 父类的私有属性或方法，子类无法直接访问，需通过父类方法进行访问
5. 子类可以**重写**父类的同名方法

语法：
1. 继承：`class 子类名(父类名1[, 父类名2, ...]):`

2. 调用：`super().[属性或方法名(参数)]`或`父类名.方法名(self, 参数)`

### 类的多态
* 子类继承父类，并重写父类的同名方法
* 函数参数可以指定父类对象，调用时传入子类对象
* 通过调用不同子类的同名方法，实现多态
* 父类可以声明一个抽象方法，子类必须实现该抽象方法
~~~python
# 多态
class Animal:
    def cry(self):
        print("动物叫")
    
    # 父类可以定义抽象方法 子类必须实现抽象方法
    def eat(self):
        pass
        
class Dog(Animal):
    # 重写父类的方法
    def cry(self):
        print("汪汪汪")
    
    # 重写父类抽象方法
    def eat(self):
        print("大狗嚼嚼嚼")
        
class Cat(Animal):
    # 重写父类方法
    def cry(self):
        print("喵喵喵")
        
    # 重写父类抽象方法
    def eat(self):
        print("吃吃吃")
        
# 定义一个方法 接收一个父类对象
def print_cry(animal: Animal):
    animal.cry()
    
def print_eat(animal: Animal):
    animal.eat()
        
        
dog = Dog()
cat = Cat()

# 传入不同子类 调用不同的方法
print_cry(dog)
print_cry(cat)

print_eat(dog)
print_eat(cat)
~~~























