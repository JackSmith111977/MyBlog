---
title: "Python笔记(持续更新中)"
date: 2025/09/06 19:56:00
tags: Python
cover: https://kei-blog.oss-cn-beijing.aliyuncs.com/asset/130997637_p0-cut.jpg
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
> [列表](#列表-list)
> [元组](#元组-tuple)
> [字符串](#字符串-string)
> [集合](#集合-set)


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
