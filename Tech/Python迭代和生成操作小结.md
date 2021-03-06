---
title: Python迭代和生成操作小结
date: 2016-09-11 21:39:49
categories: -Python
tags: summary
---

最近在使用Python进行数据处理的过程中，使用for循环来进行迭代处理的次数比较多，于是萌发了要写一篇文章来总结python中迭代和生成操作，一方面加深对Python中这块知识的理解，另一方面也锻炼自己写技术类博文的能力，这篇文章算是一次尝试吧。
<!--more-->
## 迭代
### 迭代的概念
维基百科中 [迭代（Iteration）](https://en.wikipedia.org/wiki/Iteration) 的一个通用概念是：重复某个过程的行为，这个过程中的每次重复称为一次**迭代**。具体对应到Python编程中就是，对于一个可迭代对象，比如Python中的list、tuple、string、dictionary，set等，使用某种循环结构来遍历其中的元素，这种遍历就是迭代。

### 迭代的形式
#### while
python编程中可以使用while循环进行迭代。

```python
i = 0
total = 0
while i < 100:
     total = total + i
     i = i + 1
print(total)
```

#### for
在Python编程中，利用for循环进行迭代是比较常见的一种形式。Python使用for...in...的语法结构。这点和C、Java等语言利用下标进行迭代有点不一样。下面给出几个例子。
```python
for i in range(2):
    print(i)
# output
0
1
```

```python
for s in "Max":
    print(s)
# output
M
a
x
```

```python
for key in {"Python":90, "java"：88， "C++":85}:
    print(key)
# output
Python
java
C++
```

除了上面的几种比较常见的迭代使用形式，for...in...循环中还有一些tricks。下面总结的是一些比较常见的，不一定全面。

##### enumerate
当我们想在一次迭代中不仅返回迭代对象中的元素的值，同时还想返回该元素对应的下标，那么就可以使用enumerate()函数，该函数能在一次迭代中同时返回元素值和对应的下标。当想建立两个迭代对象元素之间的对应关系时，enumerate可以作为一种方法。

```python
for i, item in enumerate(['Tom', 'Tony', 'Max']):
    print("The index of {} is {}".format(item, i))
```

```python
sub = ['Math', 'English']
score = [90, 88]
for i, item in enumerate(sub):
    print("The subject {} got {}".format(item, score[i]))
```

##### zip
zip是python的一个內建函数，接收一系列可迭代对象作为参数，将对象中对应的元素打包成一个个tuple（元组），然后返回由这些元组组成的list列表。若传入的对象的长度不等，则返回的list与参数中长度最短的对象相同。

```python
z1 = [1,2,3]
z2 = [4,5,6]
for i,j in zip(z1,z2):
    print(i,j)
# output
1 4
2 5
3 6
```
##### item()
item()方法允许我们同时操作dictionary的key和value， 用起来也十分方便。

```python
sc = {"Python":90, "java"：88， "C++":85}
for key, value in sc.item():
    print(key)
    print(value)
```


### 迭代器（iterator）
迭代器是访问集合中元素的一种方式，从集合中的第一个元素开始访问，直到所有的元素都被访问一遍后结束。迭代器不能回退，只能往前进行迭代。迭代器的一个优点是不需要事先准备好集合中的所有元素，仅仅在迭代至某个元素时才计算该元素。适合用于遍历一些大的文件或集合。同时迭代器提供了一个统一的访问集合的接口，只要是定义了__iter__()方法的对象，就可以使用迭代器进行访问。可以被next()方法调用并不断返回下一个值的对象称为**迭代器**，换句话说，迭代器对象具有next()方法。生成器就是迭代器对象，list、tuple、str等虽然是可迭代对象（Iterable），但不是迭代器（Iterator）。要把一个可迭代对象转换成迭代器，可以使用iter()函数。另外可以使用isinstance()函数来判断一个对象是可迭代对象还是迭代器对象。

对于迭代器的理解，可以把迭代器看成是一个数据流，迭代器对象被next()函数调用不断返回下一个数据，直到没有数据时抛出StopIteration错误。把这个数据流看做是一个有序序列，但却不能提前知道这个数据流到底有多长，而对于list、tuple等可迭代对象来说，对象的长度是可知的。这也是可迭代对象和迭代器的区别所在。

```python
from collections import Iterator, Iterable
a = [1, 2, 3]
b = iter(a)
isinstance(a, Iterable)  
isinstance(a, Iterator)
isinstance(b, Iterable)
isinstance(b, Iterator)
isinstance((x*x for x in range(3)), Iterator)
isinstance((x*x for x in range(3)), Iterable)

# output
True
False
True
True
True
True
```

## 生成
生成的意思是说通过某种规则产生一定的序列，在生成的过程中可能也会用到迭代操作。
### 列表生成式（list comprehension）
列表生成式是一种方便简洁的创建List的方式。创建一个List的一般思路可能是先创建一个空的List，然后再使用for循环进行迭代，将每次迭代生成的值通过append方法添加到List中去。

```python
a = []
for i in range(6):
    a.append(i)
print(a)
```
如果使用列表生成式，代码则会相当简洁。
```python
a = [i*2 for i in range(6)]
b = [a + b for a in range(3) for b in range(4)]
```

### 生成器（generator）
使用列表生成式的确可以生成一定规则的列表，但同时由于内存限制，列表容量是有限的。有时我们可能不想让一个列表占用太大的内存空间，我们希望在循环的过程中值是不断推算不断生成的，不必一次性创建完整的序列，从而节省内存空间。Python中就有这种一边循环一边计算的机制，这种机制叫**生成器**。关于生成器，还有一种定义说，带有yield的函数就被称为生成器。带有yield的函数不再是一个普通函数，python解释器会把它视为生成器。值得注意的是，生成器是**可迭代对象**，也是迭代器对象。

先讲一种简单的创建一个生成器的方法，就是直接把列表生成式的[]换成（）就创建了一个生成器。生成器具有next()方法，用于输出每次迭代值。
```python
a = (x*x for x in range(4))
next(a)
next(a)
for i in a:
    print(i)
```
值得注意的是。使用next()方法，当迭代到最后一个元素，没有更多的元素时，生成器会抛出StopIteration错误。下面通过在函数中插入yield的方法来创建生成器。
```python
def fab(m):
    i, a, b = 0, 0, 1
    while i < m:
        yield b
        a, b = b, a+b
        i = i + 1
```
yield把fab函数变成了一个生成器，每次循环执行到语句 yield b 时，fab函数就返回一个迭代值，下次迭代时，代码会从 yield b 语句的下一条语句继续执行，就好像是函数执行过程中被yield中断了数次，每次中断都会通过yield返回当前的迭代值。这个过程可以通过在代码中嵌入打印一些标记来进行可视化。
```python
def foo():
    print("begin")
    for i in range(2):
        print("before yield", i)
        yield i
        print("after yield", i)
    print("end")

f = foo()
next(f)
next(f)
next(f)

# output
begin
before yield 0
0

after yield 0
before yield 1
1

after yield 1
end
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```
## 小结
可以使用for循环进行迭代的对象都是可迭代（Iterable）类型，可以调用next()方法的对象都是迭代器（Iterator）类型，生成器（generator）既是可迭代类型,也是迭代器类型。

## 参考
1. [Python Practice Book(iterator & Generator)](http://anandology.com/python-practice-book/iterators.html)
2. [Python迭代器 生成器](http://www.cnblogs.com/kaituorensheng/p/3826911.html)
3. [廖雪峰的Python教程](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143178254193589df9c612d2449618ea460e7a672a366000)
4. [Python的函数式编程指南（三）：迭代器](http://www.cnblogs.com/huxi/archive/2011/07/01/2095931.html)
