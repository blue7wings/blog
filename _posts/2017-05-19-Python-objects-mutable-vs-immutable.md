---
layout: post
title: "Python对象：可变和不可变对象类型"
subtitle: "可变和不可变对象类型的理解，运用和坑"
date: 2017-04-19
author: LiamHsia
category: Python
finished: true
---

>并不是所有的Python对象处理方式都是一样的，有的对象是可变的(mutable)，此类对象可以被改变，其余的对象则是不可变的(immutable)，它们无法被修改，并且当我们尝试更新此类对象的时候，会返回一个新的对象。知道这些对我们写Python代码有什么意义呢？我们来一探究竟。

## 不可变(immutable)对象类型
- int
- float
- decimal
- complex
- bool
- string
- tuple
- range
- frozenset
- bytes

## 可变(mutable)对象类型
- list
- dict
- set
- bytearray
- user-defined classes (unless specifically made immutable)

怎么记忆这些可变或者不可变的对象类型呢？很简单，容器（containers）和自定义（user-defined）类型都是可变的，最典型的就是`list`，列表类型相当于一个容器，数据可以放进去，可以取出来，所以是可变对象类型。标量类型（scalar type）的对象基本上都是不可变，比如，`int`，`string`。但是，有一个例外，`tuple`是一种不可变对象类型，其实也很好理解，元组声明完成之后，就无法再进行修改，自然是不可变对象类型。

## 不可变和可变对象带来的小思考
理解不可变对象和可变对象看似是无关痛痒的知识点，如果我们能够灵活运用，能有效提高我们代码运行的效率，我们看下面合并字符串的例子：

```python
string_build = ""
for data in container:
    string_build += str(data)
```
实际上，这段代码效率是非常低下的，字符串是不可变对象类型，当合并两个字符串的时候，会创建第三个字符串，迭代次数过多或者数据量很大的字符串合并，就会在创建第三个字符串的时候浪费掉很多的内存空间，不仅如此，在迭代最后一次，为最后的结果还会开辟更大的空间来存储，实在是太浪费。

下面是更加有效和哲学（pythonic）的方式：
```python
builder_list = []
for data in container:
    builder_list.append(str(data))
"".join(builder_list)
 
### 另一种使用list实现的方法
"".join([str(data) for data in container])
 
### 或者使用map函数
"".join(map(str, container))
```
上面的代码，就充分利用了可变对象特点，当数据要更新的时候，不开辟新的空间，而是在原先的空间上增加，大大减少了空间的使用，提高了代码执行的效率。

我们再看一个可变类型对象的坑。
```python 
def my_function(param=[]):
    param.append("thing")
    return param
 
my_function() # returns ["thing"]
my_function() # returns ["thing", "thing"]
```
对于刚接触python的人来说，看到这个结果可能是一脸懵逼，what？为什么两次函数调用使用的是一个默认参数？这是因为，默认参数在函数声明的时候就已经被定义，此时变量`param`指向`[]`，在以后的调用，这种指向关系都不会改变，直到被重新赋值，python的这种值传递方式，叫对象引用传递，如果听起来有点模糊，可以读一读我上一篇文章，[Python变量赋值方式](http://www.blue7wings.com/python/Pythons-pass-by-object-reference.html)。

所以说，我们尽量不要使用可变类型对象作为函数的参数，如果想要使用，应当给予判断：

```python
def my_function2(param=None):
    if param is None:
        param = []
    param.append("thing")
    return param
```

## Reference
 - [PYTHON OBJECTS: MUTABLE VS. IMMUTABLE](https://codehabitude.com/2013/12/24/python-objects-mutable-vs-immutable/)
 - [Python变量赋值方式](http://www.blue7wings.com/python/Pythons-pass-by-object-reference.html)