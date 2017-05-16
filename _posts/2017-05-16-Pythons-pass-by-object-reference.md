---
layout: post
title: "Python变量赋值方式"
subtitle: "Python的变量赋值既不是值传递，也不是引用传递，而是对象引用传递"
date: 2017-04-06
author: LiamHsia
category: Python
finished: true
---

## 奇奇怪怪的Python变量
```
>>> list_a = [1, 2, 3]

>>> list_b = list_a

>>> print(list_b)
[1, 2, 3]

>>> list_a.append(4)

>>> print(list_a)
[1, 2, 3, 4]

>>> print(list_b)
>>> [1, 2, 3, 4]

>>> list_a = [5, 6, 7]

>>> print(list_b)
[1, 2, 3, 4]
```
我们看到这个结果，就感到很奇怪，如果是**值传递**的话，为什么`list_a.append(4)`操作之后，`list_b`的值也会变呢？那么肯定是**引用传递**，那么`list_a = [5, 6, 7]`操作之后，`list_b`的值也理所应当会改变，但事实并没有。

再来看一个函数的例子：

```
>>> def reassign(list):
      list = [0, 1]

>>> def append(list):
  	  list.append(1)

>>> list = [0]
>>> reassign(list)
>>> print(list) 
[0]

>>> append(list)
>>> print(list)
[0, 1]
```

## 值传递 & 引用传递 & 对象引用传递
> Hamlet was not written by Shakespeare; it was merely written by a man named Shakespeare.     
哈姆雷特并不是莎士比亚写的，而是一个名字叫做莎士比亚的人写的。

标签，事物，指向该事物的标签，这三者的区别显而易见，**名字叫做莎士比亚的人** 才是作者，**莎士比亚**仅仅只是个名字而已，这点放到Python中也是合情合理的。

```
a = []
```
`[]`是一个空的列表，`a`是指向该空列表的变量，但是`a`本身并不是空的, 我通常喜欢用包含对象的盒子来表示变量，就像下图那样：

![用来表示变量的盒子](http://ooyc2y4k2.bkt.clouddn.com/Uz0IE)

### 引用传递
如果是引用传递，盒子会被直接传递给函数，并且盒子里面的内容（对象）也会一并传递过去，在函数的上下文情景中，参数完全就是传递过来变量的别名。它们是同一个盒子，并且它们都指向内存中同一个对象。

![Untitled Image](http://ooyc2y4k2.bkt.clouddn.com/6ksYg)

函数无论是对变量，还是对象所做的操作，都是全局生效的，比如，函数可以改变变量的内容，甚至让它指向不同的对象：

![Untitled Image](http://ooyc2y4k2.bkt.clouddn.com/CupuX)

不需要重新赋值，也能达到同样效果：

![Untitled Image](http://ooyc2y4k2.bkt.clouddn.com/sarOm)

在引用传递的情况下，函数和调用方法使用同一个变量和对象。

### 值传递
在值传递的情况下，函数接受的只不过是，调用方法传递过来参数的拷贝，该拷贝变量存储在内存中新的地方，和原变量隔离。

![Untitled Image](http://ooyc2y4k2.bkt.clouddn.com/wbHpP)

此函数拥有新的盒子，只不过将传递过来的值放入其中，和原盒子没有任何关系，它们只是拥有相同的值而已，完全独立，一个盒子中的值改变不会影响到另一个盒子：

![Untitled Image](http://ooyc2y4k2.bkt.clouddn.com/Rzw3z)

所以，函数之外的变量不会收到任何影响：

![Untitled Image](http://ooyc2y4k2.bkt.clouddn.com/k1A1a)

### 对象引用传递
这也是Python所采用的变量赋值方式。函数接收调用函数传递过来的变量的引用，但是和值传递不同的是，函数所接受的并不是那个盒子（变量），而是自己创建了一个盒子，所指向同一个对象：

![Untitled Image](http://ooyc2y4k2.bkt.clouddn.com/LRxuY)

无论是函数和调用方法中的盒子都是指向同一个内存中的对象，所以当我们对列表进行`append`操作的时候，函数外的变量也会随之而改变，它们是**相同事物的不同名字**，**同一个对象包含在不同的盒子中**，对象引用传递也很容易理解了，**函数和调用方法用的是内存中的同一个对象，但是通过不同变量取得**。

在**引用传递**的情景中，本质上用的是同一个盒子，当你尝试对变量重新赋值，函数外的变量也会随之而改变，因为它们用的是同一个盒子，但是，**对象引用传递**完全是不一样的：

![Untitled Image](http://ooyc2y4k2.bkt.clouddn.com/1KU03)

此时，重新赋值，函数中的盒子（变量）会指向一个新的对象，但是函数外的盒子（变量）却不会改变，因为它们是不同的盒子。

## 总结
一个变量赋值给另一个变量，那么这两个变量拥有两个不同的盒子，却指向同一个对象，如果不进行赋值操作，那么它们始终都指向同一个对象，这也是为什么像`int`, `string`此类变量没有函数内外变量不一致的情况，因为它们不像列表是可变（mutable）的，它们都是不可变（immutable），没有诸如`append`的操作，只能赋值，一旦赋值，函数内盒子（变量）和函数外盒子（变量）就指向不同的内容（对象）了。


## 附录
### 可变 & 不可变 对象
![Untitled Image](http://ooyc2y4k2.bkt.clouddn.com/Zgj4g)
### Reference
- [http://stackoverflow.com/questions/32293011/the-difference-between-php-and-python-in-assignment](http://stackoverflow.com/questions/32293011/the-difference-between-php-and-python-in-assignment)

- [https://codehabitude.com/2013/12/24/python-objects-mutable-vs-immutable/](https://codehabitude.com/2013/12/24/python-objects-mutable-vs-immutable/)