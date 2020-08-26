---
title: Python高级语法
categories: 笔记
tags:
  - Python
  - 语法
abbrlink: 33390
date: 2018-11-27 21:10:31
---
<iframe src="https://trinket.io/embed/python/b970f02d2e" width="100%" height="356" frameborder="0" marginwidth="0" marginheight="0" allowfullscreen></iframe>
## 前言

本篇博客记录的是一些python的高级用法，更加深刻的理解Python的语法，。
<!--more-->

## 基本操作进阶

### 各式各样的解析

问题：如何更高效的筛选数据？

![](https://i.loli.net/2018/11/28/5bfe1d9518aa1.png)

### 命名元组

利用 `collections` 模块中的 `namedtuple` 函数

```python
>>> from collections import namedtuple
>>> Student = namedtuple('Student', ['name', 'age', 'sex', 'email'])
>>> s = Student('Jim', 21, 'male', '123@qq.com')
>>> s.name
'Jim'
>>> s.age
21
```

### 统计元素的频度

![](https://i.loli.net/2018/11/28/5bfe1d6b9d859.png)

### 元组排序

元组是可以比较大小的，两个元组比较大小是依次比较，即如果第一个元素不相同，根据第一个元素的比较结果作为最终结果，否则比较第二个元素。

![](https://i.loli.net/2018/11/28/5bfe21c741eb9.png)

### 根据字典中的值排序

方法一：利用zip把字典转换为元组(值在前，键在后)，然后sorted排序。

![](https://i.loli.net/2018/11/28/5bfe2088135b1.png)

方法二：

利用sorted的key参数。字典的items返回由键值组成的元组列表（键在前，值在后），无法直接用sorted排序。我们可以用key指定使用列表里面每一个元组的第二个元素排序，这里使用lambda表达式。

![](https://i.loli.net/2018/11/28/5bfe2346f11b7.png)

### 多个字典的公共键

![](https://i.loli.net/2018/11/28/5bfe67aacc470.png)

### 有序字典

![](https://i.loli.net/2018/11/28/5bfe68f632e28.png)

### 历史记录功能

![](https://i.loli.net/2018/11/28/5bfe69ad64e17.png)

## 迭代器

### 迭代器

迭代(iteration)指的是去获取元素的一种方式，一个接一个。当你显式或隐式的使用循环来遍历某个元素集的时候，那就是迭代。

在Python里面，可迭代对象(iterable)和迭代器(iterator)有着特殊的含义。

- `iterable`是实现了`__iter__()`方法的对象，该方法会返回一个`iterator`对象
- `iterator`是实现了`__iter__()`和`__next__()`方法的对象，`__iter__()`方法返回的是`iterator`对象本身

由此可见，`iterable`和`iterator`的本质区别就是后者多了一个`__next__()`方法。 也就是说一个`iterator`对象必定是一个`iterable`对象。

 当你使用一个`for`循环或者`map`，或着一个列表推导，那么会先通过iter()获取相应的迭代器， 然后每次循环自动通过`next`方法调用这个迭代器(iterator)，从中获取每一个元素，从而完成迭代过程。

凡是可作用于for循环的对象都是Iterable类型；

凡是可作用于next()函数的对象都是Iterator类型，它们表示一个惰性计算的序列；

集合数据类型如list、dict、str等是Iterable但不是Iterator，不过可以通过iter()函数获得一个Iterator对象。

 

```python
# 首先获得Iterator对象:
it = iter([1, 2, 3, 4, 5])
# 循环:
while True:
    try:
        # 获得下一个值:
        x = next(it)
    except StopIteration:
        # 遇到StopIteration就退出循环
        break
```

### iteritem迭代大数据字典

迭代大数据字典时，如果是使用 items() 方法，那么在迭代之前，迭代器迭代前需要把数据完整地加载到内存，这种方式不仅处理非常慢而且浪费内存，下面代码约占1.6G内存

```python
d = {i: i * 2 for i in xrange(10000000)}
for key, value in d.items():
    print("{0} = {1}".format(key, value))
```

而使用 iteritem() 方法替换 items() ，最终实现的效果一样，但是消耗的内存降低50%，为什么差距那么大呢？因为 items() 返回的是一个 list，list 在迭代的时候会预先把所有的元素加载到内存，而 iteritem() 返回的一个迭代器(iterators)，迭代器在迭代的时候，迭代元素逐个的生成。

```python
d = {i: i * 2 for i in xrange(10000000)}
for key, value in d.iteritem():
    print("{0} = {1}".format(key, value))
```



## 生成器

当我们调用一个普通的函数时，执行过程从第一条语句开始，直到碰到一个`return`语句或者遇到一个异常抛出， 再或者到了函数最后一条语句(实际上相对于一个隐式的`return None`)的时候结束。 一旦这个函数返回后将控制权交还给它的调用者，它里面所有的局部变量值都消失了，当你重新调用它的时候，一切又将重新开始。

这就是我们通常意义上面所认识的函数（或者说是子程序），但有时候我们需要创建某个函数，它并不简单的返回一个值， 而是可以不断的释放一个值的序列。那么这个特殊的函数就需要能够“保存”它的状态。

在Python中，有这种能力的“函数”被称为生成器，它们相当有用。生成器（yield语句）刚开始被引入进来主要是用来方便的生成序列值。

### 迭代器切片操作

![](https://i.loli.net/2018/11/28/5bfe6c5f45dea.png)

### 迭代器迭代多个对象

![](https://i.loli.net/2018/11/28/5bfe6ce95f64a.png)

## 字符串

### 拆分多种分隔符的字符串

![1543400851321](/tmp/1543400851321.png)

## 类

### slots节省内存

Python允许在定义class的时候，定义一个特殊的**slots**变量，来限制该class实例能添加的属性

![](https://i.loli.net/2018/11/28/5bfe74f6cd7c4.png)

```python
class Student(object):
    __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称
```

### 使用@property

python最佳编程实践推荐我们不要像java那样去调用getter和setter，而是使用装饰器@property

Python内置的@property装饰器就是负责把一个方法变成属性调用

把一个getter方法变成属性，只需要加上@property就可以了， 此时，@property本身又创建了另一个装饰器@score.setter，负责把一个setter方法变成属性赋值

```python
class Student(object):
    @property
    def score(self):
        return self._score

    @score.setter
    def score(self, value):
        if not isinstance(value, int):
            raise ValueError('score must be an integer!')
        if value < 0 or value > 100:
            raise ValueError('score must between 0 ~ 100!')
        self._score = value
s = Student()
s.score = 60
s.score
```

还可以定义只读属性，只定义getter方法，不定义setter方法就是一个只读属性

## 函数装饰器

### 斐波那契数列

装饰器本质上是一个Python函数，它可以让其他函数在**不需要做任何代码变动的前提下增加额外功能**，装饰器的返回值也是一个函数对象。它经常用于有切面需求的场景，比如：插入日志、性能测试、事务处理、缓存、权限校验等场景。装饰器是解决这类问题的绝佳设计，有了装饰器，我们就可以抽离出大量与函数功能本身无关的雷同代码并继续重用。

下面是一个简单的求斐波那契数列的算法，用的递归，很简单：

```python
def fibonacci(n):
    if n <= 1:
        return 1
    else:
        return  fibonacci(n - 1) + fibonacci(n - 2)
```

### Memoization技术

但是执行fibonacci(50)却需要花费很多时间。这是因为递归求解的时候计算了很多重复子序列。我们可以把求解斐波那契看成是一个二叉树，如下图所示：

![](https://i.loli.net/2018/11/27/5bfd36ef08617.png)

一种想法是以空间换时间，计算一次，然后存储，下次需要的时候直接取出，不需要redo前面的计算，称之为Memoization 技术。加入缓存后的程序如下：

```python
def fibonacci(n, chche=None):
    if chche is None:
        chche = {}

    if n in chche.keys():
        return chche[n]

    if n <= 1:
        return 1
    else:
        chche[n] = fibonacci(n - 1, chche) + fibonacci(n - 2, chche)

    return chche[n]
```

### 加入装饰器

这下就执行的非常快了。但是还有一个问题，就是加入我们有很多这样的函数，每个这样写很麻烦，这就引入了装饰器。加入装饰器后的代码如下：

```python
def memo(func):
    cache = {}
    def wrap(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]
    return wrap

@memo
def fibonacci(n):

    if n <= 1:
        return 1
    else:
        return  fibonacci(n - 1) + fibonacci(n - 2)


if __name__ == '__main__':
    print(fibonacci(50))
```

但是这个时候如果我们调用下面的这句代码：

```python
print(fibonacci.__name__)
#wrap
```

### 更新元数据

看来原函数的元数据被装饰器改变了。解决方法就是使用functools中的装饰器wraps装饰内部包裹函数，可以吧原函数的属性更新到包裹函数上。

```python
from functools import update_wrapper

def memo(func):
    cache = {}
    def wrap(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]
    update_wrapper(wrap,func)
    return wrap
```

主要代码是：

```python
from functools import update_wrapper
update_wrapper(wrap,func)
```

还有一种更简便的写法,和上面的代码效果相同，就是用装饰器去装饰我们写的装饰器：

```python
from functools import wraps

def memo(func):
    cache = {}
    @wraps(func)
    def wrap(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]
    return wrap
```

### 带参数的装饰器

下面实现的是一个参数类型检查器。

```python
from functools import wraps

from inspect import signature

def typeassert(*ty_args,**ty_kargs):
    def decorator(func):
        sig = signature(func)
        btypes =  sig.bind_partial(*ty_args,**ty_kargs).arguments
        def wrap(*args,**kwargs):
            for name,obj in sig.bind(*args,**kwargs).arguments.items():
                if name in btypes:
                    if not isinstance(obj,btypes[name]):
                        raise  TypeError('"%s" must be "%s"' %(name,btypes[name]))
            return func(*args,**kwargs)
        return wrap
    return decorator

def memo(func):
    cache = {}
    @wraps(func)
    def wrap(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]
    return wrap

@typeassert(int)
@memo
def fibonacci(n):

    if n <= 1:
        return 1
    else:
        return  fibonacci(n - 1) + fibonacci(n - 2)


if __name__ == '__main__':
    print(fibonacci('a'))
    
#Traceback (most recent call last):
#  File "/home/sunyan/PycharmProjects/sunyan/sunyan.py", line 37, in <module>
#    print(fibonacci('a'))
#  File "/home/sunyan/PycharmProjects/sunyan/sunyan.py", line 12, in wrap
#    raise  TypeError('"%s" must be "%s"' %(name,btypes[name]))
#TypeError: "n" must be "<class 'int'>"
```

这里我们输入了字符型变量，但是要求输入是一个int变量，所以程序报错，提示应该输入int类型。

### 属性可修改的函数装饰器

下面是一个测试函数运行时间的装饰器，并记录进入日志里面。

```python
import time
import logging
logging.basicConfig(level=logging.INFO)
def runtime(func):
    @wraps(func)
    def wrap(*args, **kwargs):
        start = time.time()
        used = time.time()-start
        msg = '%s used time: %s' %(func.__name__,used)
        logging.info(msg)
    return wrap
```

### 总体程序

```python
from functools import wraps

from inspect import signature

def typeassert(*ty_args,**ty_kargs):
    def decorator(func):
        sig = signature(func)
        btypes =  sig.bind_partial(*ty_args,**ty_kargs).arguments
        @wraps(func)
        def wrap(*args,**kwargs):
            for name,obj in sig.bind(*args,**kwargs).arguments.items():
                if name in btypes:
                    if not isinstance(obj,btypes[name]):
                        raise  TypeError('"%s" must be "%s"' %(name,btypes[name]))
            return func(*args,**kwargs)
        return wrap
    return decorator

import time
import logging
logging.basicConfig(level=logging.INFO)
def runtime(func):
    @wraps(func)
    def wrap(*args, **kwargs):
        start = time.time()
        res = func(*args, **kwargs)
        used = time.time()-start
        msg = '%s used time: %s' %(func.__name__,used)
        logging.info(msg)
        return res
    return wrap


def memo(func):
    cache = {}
    @wraps(func)
    def wrap(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]
    return wrap

@runtime
@typeassert(int)
@memo
def fibonacci(n):

    if n <= 1:
        return 1
    else:
        return  fibonacci(n - 1) + fibonacci(n - 2)


if __name__ == '__main__':
    print(fibonacci(500))
```

## 正则表达式
 [正则表达式](https://www.xncoding.com/2016/05/20/python/pyfunny.html)


## Reference

- [Python进阶强化教学视频](https://www.bilibili.com/video/av33736836)
- [一篇写python的博客](https://www.xncoding.com/2015/10/30/python/module.html)