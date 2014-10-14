title: Python数据结构和模块和输入输出
date: 2014-10-13 16:28:47
tags: python
categories:
---
http://blog.csdn.net/lufeng20/article/details/7404369
##数据结构

###关于列表更多的内容

* list.append(x) => 把一个元素添加到链表的结尾，相当于 a[len(a):] = [x] 。
* list.extend(L) => 将一个给定列表中的所有元素都添加到另一个列表中，相当于 a[len(a):] = L 。
* list.insert(i, x) => 在指定位置插入一个元素。第一个参数是准备插入到其前面的那个元素的索引，例如 a.insert(0, x) 会插入到整个链表之前，而 a.insert(len(a), x) 相当于 a.append(x) 。
* list.remove(x) => 删除链表中值为 x 的第一个元素。如果没有这样的元素，就会返回一个错误。
* list.pop([i]) => 从链表的指定位置删除元素，并将其返回。如果没有指定索引， a.pop() 返回最后一个元素。元素随即从链表中被删除。（方法中 i 两边的方括号表示这个参数是可选的，而不是要求你输入一对方括号，你会经常在 Python 库参考手册中遇到这样的标记。）
* list.index(x) => 返回链表中第一个值为 x 的元素的索引。如果没有匹配的元素就会返回一个错误。
* list.count(x) => 返回 x 在链表中出现的次数。
* list.sort() => 对链表中的元素就地进行排序。
* list.reverse() => 就地倒排链表中的元素。

####把链表当作队列使用

实现队列，使用 collections.deque ，它为在首尾两端快速插入和删除而设计。例如:
```python
>>> from collections import deque
>>> queue = deque(["Eric", "John", "Michael"])
>>> queue.append("Terry")           # Terry arrives
>>> queue.append("Graham")          # Graham arrives
>>> queue.popleft()                 # The first to arrive now leaves
'Eric'
>>> queue.popleft()                 # The second to arrive now leaves
'John'
>>> queue                           # Remaining queue in order of arrival
deque(['Michael', 'Terry', 'Graham'])
```

####函数式编程工具

对于链表来讲，有三个内置函数非常有用: filter(), map(), 以及 reduce()。

filter(function, sequence) 返回一个 sequence（序列），包括了给定序列中所有调用 function(item) 后返回值为 true 的元素。（如果可能的话，会返回相同的类型）。如果该 序列 （sequence） 是一个 string （字符串）或者 tuple （元组），返回值必定是同一类型，否则，它总是 list 。例如，以下程序可以计算部分素数:
```python
>>> def f(x): return x % 2 != 0 and x % 3 != 0
...
>>> filter(f, range(2, 25))
[5, 7, 11, 13, 17, 19, 23]
```

map(function, sequence) 为每一个元素依次调用 function(item) 并将返回值组成一个链表返回。例如，以下程序计算立方:
```python
>>> def cube(x): return x*x*x
...
>>> map(cube, range(1, 11))
[1, 8, 27, 64, 125, 216, 343, 512, 729, 1000]
```
可以传入多个序列，函数也必须要有对应数量的参数，执行时会依次用各序列上对应的元素来调用函数（如果某些序列比其它的短，就用 None 来代替）。如果把 None 做为一个函数传入，则直接返回参数做为替代。例如:
```python
>>> seq = range(8)
>>> def add(x, y): return x+y
...
>>> map(add, seq, seq)
[0, 2, 4, 6, 8, 10, 12, 14]
```
reduce(function, sequence) 返回一个单值，它是这样构造的：首先以序列的前两个元素调用函数 function，再以返回值和第三个参数调用，依次执行下去。例如，以下程序计算 1 到 10 的整数之和:
```python
>>> def add(x,y): return x+y
...
>>> reduce(add, range(1, 11))
55
```
如果序列中只有一个元素，就返回它，如果序列是空的，就抛出一个异常。
可以传入第三个参数作为初始值。如果序列是空的，就返回初始值，否则函数会先接收初始值和序列的第一个元素，然后是返回值和下一个元素，依此类推。

####列表推导式

列表推导式为从序列中创建列表提供了一个简单的方法。 普通的应用程序通过将一些操作应用于序列的每个成员并通过返回的元素创建列表，或者通过满足特定条件的元素创建子序列。

例如, 假设我们创建一个 squares 列表, 可以像下面方式:
```python
>>> squares = []
>>> for x in range(10):
...     squares.append(x**2)
...
>>> squares
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```
我们同样能够达到目的采用下面的方式:
```python
squares = [x**2 for x in range(10)]
```
这也相当于 `squares = map(lambda x: x**2, range(10))`, 但是上面的方式显得简洁以及具有可读性.

列表推导式由包含一个表达式的括号组成，表达式后面跟随一个 for 子句，之后可以有零或多个 for 或 if 子句。 结果是一个列表，由表达式依据其后面的 for 和 if 子句上下文计算而来的结果构成。

例如，如下的列表推导式结合两个列表的元素，如果元素之间不相等的话:
```python
>>> [(x, y) for x in [1,2,3] for y in [3,1,4] if x != y]
[(1, 3), (1, 4), (2, 3), (2, 1), (2, 4), (3, 1), (3, 4)]
```

#####嵌套的列表推导式

列表推导式可以嵌套。
```python
>>> matrix = [
...     [1, 2, 3, 4],
...     [5, 6, 7, 8],
...     [9, 10, 11, 12],
... ]
>>> [[row[i] for row in matrix] for i in range(4)]
[[1, 5, 9], [2, 6, 10], [3, 7, 11], [4, 8, 12]]
```
在实际中，你应该更喜欢使用内置函数组成复杂流程语句。 对此种情况 zip() 函数将会做的更好:
```python
>>> list(zip(*matrix))
[(1, 5, 9), (2, 6, 10), (3, 7, 11), (4, 8, 12)]
```

###del 语句

有个方法可以从列表中按给定的索引而不是值来删除一个子项： del 语句。它不同于有返回值的 pop() 方法。语句 del 还可以从列表中删除切片或清空整个列表（我们以前介绍过一个方法是将空列表赋值给列表的切片）。例如:
```python
>>> a = [-1, 1, 66.25, 333, 333, 1234.5]
>>> del a[0]
>>> a
[1, 66.25, 333, 333, 1234.5]
>>> del a[2:4]
>>> a
[1, 66.25, 1234.5]
>>> del a[:]
>>> a
[]
```
del 也可以删除整个变量:
```python
>>> del a
```
此后再引用命名 a 会引发错误（直到另一个值赋给它为止）。

###元组和序列

我们知道链表和字符串有很多通用的属性，例如索引和切割操作。它们是 序列 类型（参见 typesseq ）中的两种。因为 Python 是一个在不断进化的语言，也可能会加入其它的序列类型，这里介绍另一种标准序列类型： 元组 。
一个元组由数个逗号分隔的值组成，例如:
```python
>>> t = 12345, 54321, 'hello!'
>>> t[0]
12345
>>> t
(12345, 54321, 'hello!')
>>> # Tuples may be nested:
... u = t, (1, 2, 3, 4, 5)
>>> u
((12345, 54321, 'hello!'), (1, 2, 3, 4, 5))
>>> # Tuples are immutable:
... t[0] = 88888
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
>>> # but they can contain mutable objects:
... v = ([1, 2, 3], [3, 2, 1])
>>> v
([1, 2, 3], [3, 2, 1])
```
如你所见，元组在输出时总是有括号的，以便于正确表达嵌套结构。在输入时可以有或没有括号，不过经常括号都是必须的（如果元组是一个更大的表达式的一部分）。不能给元组的一个独立的元素赋值（尽管你可以通过联接和切割来模拟）。还可以创建包含可变对象的元组，例如链表。虽然元组和列表很类似，它们经常被用来在不同的情况和不同的用途。元组有很多用途。例如 (x, y) 坐标对，数据库中的员工记录等等。元组就像字符串，不可改变。

这个调用等号右边可以是任何线性序列，称之为 序列拆封 非常恰当。序列拆封要求左侧的变量数目与序列的元素个数相同。要注意的是可变参数（multiple assignment ）其实只是元组封装和序列拆封的一个结合。
```python
>>> x, y, z = t
```

###集合

Python 还包含了一个数据类型 set （集合） 。集合是一个无序不重复元素的集。基本功能包括关系测试和消除重复元素。集合对象还支持 union（联合），intersection（交），difference（差）和 sysmmetric difference（对称差集）等数学运算。
大括号或 set() 函数可以用来创建集合。 注意：想要创建空集合，你必须使用 set() 而不是 {} 。后者用于创建空字典。
```python
>>> basket = ['apple', 'orange', 'apple', 'pear', 'orange', 'banana']
>>> fruit = set(basket)               # create a set without duplicates
>>> fruit
set(['orange', 'pear', 'apple', 'banana'])
>>> 'orange' in fruit                 # fast membership testing
True
>>> 'crabgrass' in fruit
False
>>> # Demonstrate set operations on unique letters from two words
...
>>> a = set('abracadabra')
>>> b = set('alacazam')
>>> a                                  # unique letters in a
set(['a', 'r', 'b', 'c', 'd'])
>>> a - b                              # letters in a but not in b
set(['r', 'd', 'b'])
>>> a | b                              # letters in either a or b
set(['a', 'c', 'r', 'd', 'b', 'm', 'z', 'l'])
>>> a & b                              # letters in both a and b
set(['a', 'c'])
>>> a ^ b                              # letters in a or b but not both
set(['r', 'd', 'b', 'm', 'z', 'l'])
```
类似 for lists ，这里有一种集合推导式语法:注意这里是用大括号，这就代表需要组成集合。
```python
>>> a = {x for x in 'abracadabra' if x not in 'abc'}
>>> a
{'r', 'd'}
```

###字典

字典在某些语言中可能称为 联合内存（ associative memories ）或 联合数组（ associative arrays 。序列是以连续的整数为索引，与此不同的是，字典以 关键字 为索引，关键字可以是任意不可变类型，通常用字符串或数值。如果元组中只包含字符串和数字，它可以作为关键字，如果它直接或间接地包含了可变对象，就不能当做关键字。不能用链表做关键字，因为链表可以用索引、切割或者 append() 和 extend() 等方法改变。
理解字典的最佳方式是把它看做无序的键： 值对 （key:value pairs）集合，键必须是互不相同的（在同一个字典之内）。一对大括号创建一个空的字典： {} 。初始化链表时，在大括号内放置一组逗号分隔的键：值对，这也是字典输出的方式。
字典的主要操作是依据键来存储和析取值。也可以用 del 来删除键：值对（key:value）。如果你用一个已经存在的关键字存储值，以前为该关键字分配的值就会被遗忘。试图从一个不存在的键中取值会导致错误。对一个字典执行 keys() 将返回一个字典中所有关键字组成的无序列表（如果你想要排序，只需使用 sorted())。使用 in 关键字（指 Python 语法）可以检查字典中是否存在某个关键字（指字典）。
```python
>>> tel = {'jack': 4098, 'sape': 4139}
>>> tel['guido'] = 4127
>>> tel
{'sape': 4139, 'guido': 4127, 'jack': 4098}
>>> tel['jack']
4098
>>> del tel['sape']
>>> tel['irv'] = 4127
>>> tel
{'guido': 4127, 'irv': 4127, 'jack': 4098}
>>> tel.keys()
['guido', 'irv', 'jack']
>>> 'guido' in tel
True
```
dict() 构造函数可以直接从 key-value 对中创建字典:
```python
>>> dict([('sape', 4139), ('guido', 4127), ('jack', 4098)])
{'sape': 4139, 'jack': 4098, 'guido': 4127}
```
此外，字典推导式可以从任意的键值表达式中创建字典:(推导式的形式总是与具体的数据结构有关，通过python函数的设置，了解python语言的创作者设计python的原则！)
```python
>>> {x: x**2 for x in (2, 4, 6)}
{2: 4, 4: 16, 6: 36}
```
如果关键字都是简单的字符串，有时通过关键字参数指定 key-value 对更为方便:
```python
>>> dict(sape=4139, guido=4127, jack=4098)
{'sape': 4139, 'jack': 4098, 'guido': 4127}
```

###循环技巧

在序列中循环时，索引位置和对应值可以使用 enumerate() 函数同时得到:
```python
>>> for i, v in enumerate(['tic', 'tac', 'toe']):
...     print(i, v)
...
0 tic
1 tac
2 toe
```
同时循环两个或更多的序列，可以使用 zip() 整体打包:
```python
>>> questions = ['name', 'quest', 'favorite color']
>>> answers = ['lancelot', 'the holy grail', 'blue']
>>> for q, a in zip(questions, answers):
...     print 'What is your {0}?  It is {1}.'.format(q, a)
...
What is your name?  It is lancelot.
What is your quest?  It is the holy grail.
What is your favorite color?  It is blue.
```
需要逆向循环序列的话，先正向定位序列，然后调用 reversed() 函数:
```python
>>> for i in reversed(xrange(1, 10, 2)):
...     print(i)
```
要按排序后的顺序循环序列的话，使用 sorted() 函数，它不改动原序列，而是生成一个新的已排序的序列:
```python
>>> basket = ['apple', 'orange', 'apple', 'pear', 'orange', 'banana']
>>> for f in sorted(set(basket)):
...     print f
...
apple
banana
orange
pear
```

###深入条件控制

比较操作符 in 和 not in 用来判断值是否在一个区间之内。操作符 is 和 is not 比较两个对象是否相同；这只和诸如链表这样的可变对象有关。所有的比较操作符具有相同的优先级，低于所有的数值操作。

比较操作可以传递。例如 a < b == c 判断是否 a 小于 b 并且 b 等于 c 。

比较操作可以通过逻辑操作符 and 和 or 组合，比较的结果可以用 not 来取反义。这些操作符的优先级又低于比较操作符，在它们之中，not 具有最高的优先级， or 优先级最低，所以 A and not B or C 等于 (A and (notB)) or C 。当然，括号也可以用于比较表达式。

逻辑操作符 and 和 or 也称作 短路操作符 ：它们的参数从左向右解析，一旦结果可以确定就停止。例如，如果 A 和 C 为真而 B 为假， A and B and C 不会解析 C 。作用于一个普通的非逻辑值时，短路操作符的返回值通常是最后一个变量。

可以把比较或其它逻辑表达式的返回值赋给一个变量，例如，
```python
>>> string1, string2, string3 = '', 'Trondheim', 'Hammer Dance'
>>> non_null = string1 or string2 or string3
>>> non_null
'Trondheim'
```

###比较序列和其它类型

比较操作按 字典序 进行：首先比较前两个元素，如果不同，就决定了比较的结果；如果相同，就比较后两个元素，依此类推，直到所有序列都完成比较。如果两个元素本身就是同样类 型的序列，就递归字典序比较。如果两个序列的所有子项都相等，就认为序列相等。如果一个序列是另一个序列的初始子序列，较短的一个序列就小于另一个。字符 串的字典序按照单字符的 ASCII 顺序。下面是同类型序列之间比较的一些例子:
```python
(1, 2, 3)              < (1, 2, 4)
[1, 2, 3]              < [1, 2, 4]
'ABC' < 'C' < 'Pascal' < 'Python'
(1, 2, 3, 4)           < (1, 2, 4)
(1, 2)                 < (1, 2, -1)
(1, 2, 3)             == (1.0, 2.0, 3.0)
(1, 2, ('aa', 'ab'))   < (1, 2, ('abc', 'a'), 4)
```
需要注意的是如果通过 < 或者 > 比较的对象只要具有合适的比较方法就是合法的。 比如，混合数值类型是通过它们的数值就行比较的，所以0是等于0.0。 否则解释器将会触发一个 TypeError 异常，而不是提供一个随意的结果。

##模块

模块的模块名（做为一个字符串）可以由全局变量 __name__ 得到。在当前目录下创建一个叫 fibo.py 的文件,在其中定义两个方法：fib;fib2,导入这个模块(如果打算频繁使用一个函数，你可以将它赋予一个本地变量:): 
```python
import fibo
>>> fibo.fib(1000)
1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987
>>> fibo.fib2(100)
[1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89]
>>> fibo.__name__
'fibo'
#函数赋给变量
>>> fib = fibo.fib
>>> fib(500)
1 1 2 3 5 8 13 21 34 55 89 144 233 377
```

###深入模块

除了包含函数定义外，模块也可以包含可执行语句。这些语句一般用来初始化模块。每个模块都有自己私有的符号表，被模块内所有的函数定义作为全局符号表使用。如果你确切的知道自己在做什么，你可以使用引用模块函数的表示法访问模块的全局变量， modname.itemname 。模块可以导入其他的模块。 一个（好的）习惯是将所有的 import 语句放在模块的开始（或者是脚本），这并非强制。 被导入的模块名会放入当前模块的全局符号表中。(类似于ruby的特性)
import 语句的一个变体直接从被导入的模块中导入命名到本模块的语义表中。例如:
```python
>>> from fibo import fib, fib2
>>> fib(500)
1 1 2 3 5 8 13 21 34 55 89 144 233 377
```
这样不会从局域语义表中导入模块名（如上所示， fibo 没有定义）。
甚至有种方式可以导入模块中的所有定义:
```python
>>> from fibo import *
>>> fib(500)
1 1 2 3 5 8 13 21 34 55 89 144 233 377
```
需要注意的是在实践中往往不鼓励从一个模块或包中使用 * 导入所有，因为这样会让代码变得很难读。不过，在交互式会话中这样用很方便省力。
Note : 出于性能考虑，每个模块在每个解释器会话中只导入一遍。因此，如果你修改了你的模块，需要重启解释器——或者，如果你就是想交互式的测试这么一个模块，可以用 reload() 重新加载，例如 reload(modulename) 。

####作为脚本来执行模块

当你使用以下方式运行 Python 模块时，模块中的代码便会被执行:
```python
python fibo.py <arguments>
```
模块中的代码会被执行，就像导入它一样，不过此时 __name__ 被设置为 "__main__" 。这相当于，如果你在模块后加入如下代码:
```python
if __name__ == "__main__":
    import sys
    fib(int(sys.argv[1]))
```
就可以让此文件像作为模块导入时一样作为脚本执行。此代码只有在模块作为 “main” 文件执行时才被调用:
```bash
$ python fibo.py 50
1 1 2 3 5 8 13 21 34
```
如果模块被导入，不会执行这段代码.
***这通常用来为模块提供一个便于测试的用户接口（将模块作为脚本执行测试需求）。***

#### 模块的搜索路径

导入一个叫 spam 的模块时，解释器先在当前目录中搜索名为 spam.py 的文件。如果没有找到的话，接着会到 sys.path 变量中给出的目录列表中查找。 sys.path 变量的初始值来自如下:

1.输入脚本的目录（当前目录）;
2.环境变量 PYTHONPATH 表示的目录列表中搜索 (这和 shell 变量 PATH 具有一样的语法，即一系列目录名的列表)；
3.Python 默认安装路径中搜索。

这样就允许 Python 程序了解如何修改或替换模块搜索目录。需要注意的是由于这些目录中包含有搜索路径中运行的脚本，所以这些脚本不应该和标准模块重名，否则在导入模块时 Python 会尝试把这些脚本当作模块来加载。这通常会引发错误。

####“编译的” Python 文件

对于引用了大量标准模块的短程序，有一个提高启动速度的重要方法，如果在 spam.py 所在的目录下存在一个名为 spam.pyc 的文件，它会被视为 spam 模块的预“编译”（ byte-compiled ，二进制编译）版本。用于创建 spam.pyc 的这一版 spam.py 的修改时间记录在 spam.pyc 文件中，如果两者不匹配，.pyc 文件就被忽略。spam.pyc 文件的内容是平台独立的，所以 Python 模块目录可以在不同架构的机器之间共享。
部分高级技巧:

* 以 -O 参数调用 Python 解释器时，会生成优化代码并保存在 .pyo 文件中。现在的优化器没有太多帮助；它只是删除了断言（ assert ）语句。使用 -O 参数， 所有 的字节码（ bytecode ）都会被优化； .pyc 文件被忽略， .py 文件被编译为优化代码。
* 向 Python 解释器传递两个 -O 参数（ -OO ）会执行完全优化的二进制优化编译，这偶尔会生成错误的程序。现在的优化器，只是从字节码中删除了 __doc__ 符串，生成更为紧凑的 .pyo 文件。因为某些程序依赖于这些变量的可用性，你应该只在确定无误的场合使用这一选项。
* 来自 .pyc 文件或 .pyo 文件中的程序不会比来自 .py 文件的运行更快； .pyc 或 .pyo 文件只是在它们加载的时候更快一些。
* 通过脚本名在命令行运行脚本时，不会将为该脚本创建的二进制代码写入 .pyc 或 .pyo 文件。当然，把脚本的主要代码移进一个模块里，然后用一个小的启动脚本导入这个模块，就可以提高脚本的启动速度。也可以直接在命令行中指定一个 .pyc 或 .pyo 文件。
* 对于同一个模块（这里指例程 spam.py －－译者），可以只有 spam.pyc 文件（或者 spam.pyo ，在使用 -O 参数时）而没有 spam.py 文件。这样可以打包发布比较难于逆向工程的 Python 代码库。
* compileall 模块 可以为指定目录中的所有模块创建 .pyc 文件（或者使用 -O 参数创建 .pyo 文件）。

###标准模块

Python 带有一个标准模块库，并发布有独立的文档，名为 Python 库参考手册（此后称其为“库参考手册”）。有一些模块内置于解释器之中，这些操作的访问接口不是语言内核的一部分，但是已经内置于解释器了。这既是为了提 高效率，也是为了给系统调用等操作系统原生访问提供接口。这类模块集合是一个依赖于底层平台的配置选项。例如，winreg 模块只提供在 Windows 系统上才有。有一个具体的模块值得注意： sys ，这个模块内置于所有的 Python 解释器。变量 sys.ps1 和 sys.ps2 定义了主提示符和辅助提示符字符串:
```python
>>> import sys
>>> sys.ps1
'>>> '
>>> sys.ps2
'... '
>>> sys.ps1 = 'C> '
C> print 'Yuck!'
Yuck!
C>
```
变量 sys.path 是解释器模块搜索路径的字符串列表。它由环境变量 PYTHONPATH 初始化，如果没有设定 PYTHONPATH ，就由内置的默认值初始化。你可以用标准的字符串操作修改它:(可用于动态的增加模块).
```
>>> import sys
>>> sys.path.append('/ufs/guido/lib/python')
```

###dir() 函数

内置函数 dir() 用于按模块名搜索模块定义，它返回一个字符串类型的存储列表:(显示模块中有哪些常量即变量和函数).无参数调用时， dir() 函数返回当前定义的命名:
```python
>>> import fibo, sys
>>> dir(fibo)
['__name__', 'fib', 'fib2']
>>> dir(sys)
>>> dir()
```
注意该列表列出了所有类型的名称：变量，模块，函数，等等。
dir() 不会列出内置函数和变量名。如果你想列出这些内容，它们在标准模块 __builtin__ 中定义:
```python
>>> import builtins
>>> dir(builtins)
```

###包

包通常是使用用“圆点模块名”的结构化模块命名空间。例如，名为 A.B 的模块表示了名为 A 的包中名为 B 的子模块。正如同用模块来保存不同的模块架构可以避免全局变量之间的相互冲突.
你的包可能会是这个样子（通过分级的文件体系来进行分组）.当导入这个包时，Python通过 sys.path 搜索路径查找包含这个包的子目录。
为了让 Python 将目录当做内容包，目录中必须包含 __init__.py 文件。 这是为了避免一个含有烂俗名字的目录无意中隐藏了稍后在模块搜索路径中出现的有效模块，比如 string 。 最简单的情况下，只需要一个空的 __init__.py 文件即可。 当然它也可以执行包的初始化代码，或者定义稍后介绍的 __all__ 变量。
用户可以每次只导入包里的特定模块，例如:
```python
import sound.effects.echo
sound.effects.echo.echofilter(input, output, delay=0.7, atten=4)
```
这样就导入了 sound.effects.echo 子模块。它必需通过完整的名称来引用。
导入包时有一个可以选择的方式:
这样就加载了 echo 子模块，并且使得它在没有包前缀的情况下也可以使用，所以它可以如下方式调用
```python
from sound.effects import echo
echo.echofilter(input, output, delay=0.7, atten=4)
```
还有另一种变体用于直接导入函数或变量:
```python
from sound.effects.echo import echofilter
echofilter(input, output, delay=0.7, atten=4)
```
这样就又一次加载了 echo 子模块，但这样就可以直接调用它的 echofilter() 函数:

需要注意的是使用 from package import item 方式导入包时，这个子项（item）既可以是包中的一个子模块（或一个子包），也可以是包中定义的其它命名，像函数、类或变量。import 语句首先核对是否包中有这个子项，如果没有，它假定这是一个模块，并尝试加载它。如果没有找到它，会引发一个 ImportError 异常。相反，使用类似 import item.subitem.subsubitem 这样的语法时，这些子项必须是包，最后的子项可以是包或模块，但不能是前面子项中定义的类、函数或变量。

####从 * 导入包

那么当用户写下 from sound.effects import * 时会发生什么事？理想中，总是希望在文件系统中找出包中所有的子模块，然后导入它们。这可能会花掉很长时间，并且出现期待之外的边界效应，导出了希望只能显式导入的包。
对于包的作者来说唯一的解决方案就是给提供一个明确的包索引。 import 语句按如下条件进行转换：执行 from package import * 时，如果包中的 __init__.py 代码定义了一个名为 __all__ 的列表，就会按照列表中给出的模块名进行导入。新版本的包发布时作者可以任意更新这个列表。如果包作者不想 import * 的时候导入他们的包中所有模块，那么也可能会决定不支持它（import *）。例如， sounds/effects/__init__.py 这个文件可能包括如下代码:
```python
__all__ = ["echo", "surround", "reverse"]
```
这意味着 `from sound.effects import *` 语句会从 sound 包中导入以上三个已命名的子模块。
如果没有定义 __all__ ， from sound.effects import * 语句 不会 从 sound.effects 包中导入所有的子模块。无论包中定义多少命名，只能确定的是导入了 sound.effects 包（可能会运行 __init__.py 中的初始化代码）以及包中定义的所有命名会随之导入。这样就从 __init__.py 中导入了每一个命名（以及明确导入的子模块）。同样也包括了前述的 import 语句从包中明确导入的子模块.

####包内引用

如果包中使用了子包结构（就像示例中的 sound 包），可以按绝对位置从相邻的包中引入子模块。例如，如果 sound.filters.vocoder 包需要使用 sound.effects 包中的 echo 模块，它可以 `from sound.effects import echo`。

你可以用这样的形式 `from module import name`来写显式的相对位置导入。那些显式相对导入用点号标明关联导入当前和上级包。以 surround 模块为例，你可以这样用:
```python
from . import echo
from .. import formats
from ..filters import equalizer
```
需要注意的是显式或隐式相对位置导入都基于当前模块的命名。因为主模块的名字总是 "__main__" ，***Python 应用程序的主模块应该总是用绝对导入***。

####多重目录中的包

包支持一个更为特殊的特性， __path__ 。 在包的 __init__.py 文件代码执行之前，该变量初始化一个目录名列表。该变量可以修改，它作用于包中的子包和模块的搜索功能。

这个功能可以用于扩展包中的模块集，不过它不常用。
***事实上函数定义既是“声明”又是“可执行体”；执行体由函数在模块全局语义表中的命名导入。***

##输入和输出

###格式化输出

我们有两种大相径庭的输出值方法： 表达式语句 和 print 语句。（第三种方法是使用文件对象的 write() 方法，标准文件输出可以参考 sys.stdout 。详细内容参见库参考手册。）
有两种方法可以格式化你的输出： 第一种方法是由你自己处理整个字符串，通过使用字符串切割和连接操作可以创建任何你想要的输出形式。第二种方法是使用 str.format() 方法。
标准模块 string 包括了一些操作，将字符串填充入给定列时，这些操作很有用。第二种方法是使用 Template 方法。当然，还有一个问题，如何将值转化为字符串？很幸运，Python 有办法将任意值转为字符串：将它传入 repr() 或 str() 函数。
函数 str() 用于将值转化为适于人阅读的形式，而 repr() 转化为供解释器读取的形式（如果没有等价的语法，则会发生 SyntaxError 异常） 某对象没有适于人阅读的解释形式的话， str() 会返回与 repr() 等同的值。很多类型，诸如数值或链表、字典这样的结构，针对各函数都有着统一的解读方式。字符串和浮点数，有着独特的解读方式。
有两种方式可以写平方和立方表:
```python
>>> for x in range(1, 11):
...     print repr(x).rjust(2), repr(x*x).rjust(3)
...     # Note use of 'end' on previous line
...     print repr(x*x*x).rjust(4)
...
 1   1    1
 2   4    8
 3   9   27
 4  16   64
 5  25  125
 6  36  216
 7  49  343
 8  64  512
 9  81  729
10 100 1000
#example2
>>> for x in range(1, 11):
...     print '{0:2d} {1:3d} {2:4d}'.format(x, x*x, x*x*x)
...
 1   1    1
 2   4    8
 3   9   27
 4  16   64
 5  25  125
 6  36  216
 7  49  343
 8  64  512
 9  81  729
10 100 1000
```
以上是一个 str.rjust() 方法的演示，它把字符串输出到一列，并通过向左侧填充空格来使其右对齐。类似的方法还有 str.ljust() 和 str.center() 。这些函数只是输出新的字符串，并不改变什么。如果输出的字符串太长，它们也不会截断它，而是原样输出，这会使你的输出格式变得混乱，不过总强过另一种选择（截断字符串），因为那样会产生错误的输出值。（如果你确实需要截断它，可以使用切割操作，例如： x.ljust(n)[:n] 。）
还有另一个方法， str.zfill() 它用于向数值的字符串表达左侧填充 0。该函数可以正确理解正负号:
```python
>>> '12'.zfill(5)
'00012'
>>> '-3.14'.zfill(7)
'-003.14'
>>> '3.14159265359'.zfill(5)
'3.14159265359'
```
方法 str.format() 的基本用法如下:
```python
>>> print 'We are the {} who say "{}!"'.format('knights', 'Ni')
We are the knights who say "Ni!"
```
大括号和其中的字符会被替换成传入 str.format() 的参数。大括号中的数值指明使用传入 str.format() 方法的对象中的哪一个:
```python
>>> print '{0} and {1}'.format('spam', 'eggs')
spam and eggs
>>> print '{1} and {0}'.format('spam', 'eggs')
eggs and spam
```
如果在 str.format() 调用时使用关键字参数，可以通过参数名来引用值:
```python
>>> print 'This {food} is {adjective}.'.format(
...       food='spam', adjective='absolutely horrible')
This spam is absolutely horrible.
```
定位和关键字参数可以组合使用:
```python
>>> print 'The story of {0}, {1}, and {other}.'.format('Bill', 'Manfred',other='Georg')
The story of Bill, Manfred, and Georg.
```
'!s' （应用 str() ） 和 '!r' （应用 repr() ） 可以在格式化之前转换值:
```python
>>> import math
>>> print 'The value of PI is approximately {}.'.format(math.pi)
The value of PI is approximately 3.14159265359.
>>> print 'The value of PI is approximately {!r}.'.format(math.pi)
The value of PI is approximately 3.141592653589793.
```
字段名后允许可选的 ':' 和格式指令。这允许对值的格式化加以更深入的控制。下例将 Pi 转为三位精度。
```python
>>> import math
>>> print('The value of PI is approximately {0:.3f}.'.format(math.pi))
The value of PI is approximately 3.142.
```
在字段后的 ':' 后面加一个整数会限定该字段的最小宽度，这在美化表格时很有用。
```python
>>> table = {'Sjoerd': 4127, 'Jack': 4098, 'Dcab': 7678}
>>> for name, phone in table.items():
...     print '{0:10} ==> {1:10d}'.format(name, phone)
...
Jack       ==>       4098
Dcab       ==>       7678
Sjoerd     ==>       4127
```
如果你有个实在是很长的格式化字符串，不想分割它。如果你可以用命名来引用被格式化的变量而不是位置就好了。有个简单的方法，可以传入一个字典，用中括号访问它的键:
```python
>>> table = {'Sjoerd': 4127, 'Jack': 4098, 'Dcab': 8637678}
>>> print 'Jack: {0[Jack]:d}; Sjoerd: {0[Sjoerd]:d}; '
          'Dcab: {0[Dcab]:d}'.format(table)
Jack: 4098; Sjoerd: 4127; Dcab: 8637678
```
也可以用 ‘**’ 标志将这个字典以关键字参数的方式传入。
```python
>>> table = {'Sjoerd': 4127, 'Jack': 4098, 'Dcab': 8637678}
>>> print 'Jack: {Jack:d}; Sjoerd: {Sjoerd:d}; Dcab: {Dcab:d}'.format(**table)
Jack: 4098; Sjoerd: 4127; Dcab: 8637678
```
这种方式与新的内置函数 vars() 组合使用非常有效。该函数返回包含所有局部变量的字典。
要进一步了解字符串格式化方法 str.format() ，参见 formatstrings 。

操作符 % 也可以用于字符串格式化。它以类似 sprintf()-style 的方式解析左参数，将右参数应用于此，得到格式化操作生成的字符串，例如:
```python
>>> import math
>>> print 'The value of PI is approximately %5.3f.' % math.pi
The value of PI is approximately 3.142.
```

###文件读写

函数 open() 返回文件对象，通常的用法需要两个参数： open(filename, mode)。
```python
>>> f = open('workfile', 'w')
>>> print f
<open file 'workfile', mode 'w' at 80a0960>
```
模式 有： 'r' ，此选项使文件只读； 'w' ，此选项使文件只写（对于同名文件，该操作使原有文件被覆盖）； 'a' ，此选项以追加方式打开文件； 'r+' ，此选项以读写方式打开文件； 模式 参数是可选的。如果没有指定，默认为 'r' 模式。
在 Windows 平台上， 'b' 模式以二进制方式打开文件，所以可能会有类似于 'rb' ， 'wb' ， 'r+b' 等等模式组合。

####文件对象方法

要读取文件内容，需要调用 f.read(size) ，该方法读取若干数量的数据并以字符串形式返回其内容， size 是可选的数值，指定字符串长度。如果没有指定 size 或者指定为负数，就会读取并返回整个文件。当文件大小为当前机器内存两倍时，就会产生问题。反之，会尽可能按比较大的 size 读取和返回数据。如果到了文件末尾，f.read() 会返回一个空字符串（”“）。

f.readline() 从文件中读取单独一行，字符串结尾会自动加上一个换行符（ \n ），只有当文件最后一行没有以换行符结尾时，这一操作才会被忽略。这样返回值就不会有混淆，如果如果 f.readline() 返回一个空字符串，那就表示到达了文件末尾，如果是一个空行，就会描述为 '\n' ，一个只包含换行符的字符串。

f.readlines() 返回一个列表，其中包含了文件中所有的数据行。如果给定了 sizehint 参数，就会读入多于一行的比特数，从中返回多行文本。这个功能通常用于高效读取大型行文件，避免了将整个文件读入内存。这种操作只返回完整的行。

一种替代的方法是通过遍历文件对象来读取文件行。 这是一种内存高效、快速，并且代码简洁的方式:
```python
>>> for line in f:
...     print(line, end='')
...
This is the first line of the file.
Second line of the file
```
虽然这种替代方法更简单，但并不具备细节控制能力。 因为这两种方法处理行缓存的方式不同，千万不能搞混。

f.write(string) 方法将 string 的内容写入文件，并返回写入字符的长度。
```python
>>> f.write('This is a test\n')
15
```
想要写入其他非字符串内容，首先要将它转换为字符串.

f.tell() 返回一个整数，代表文件对象在文件中的指针位置，该数值计量了自文件开头到指针处的比特数。需要改变文件对象指针话话，使用 f.seek(offset,from_what) 。指针在该操作中从指定的引用位置移动 offset 比特，引用位置由 from_what 参数指定。 from_what 值为 0 表示自文件起始处开始，1 表示自当前文件指针位置开始，2 表示自文件末尾开始。 from_what 可以忽略，其默认值为零，此时从文件头开始。
```python
>>> f = open('/tmp/workfile', 'rb+')
>>> f.write(b'0123456789abcdef')
16
>>> f.seek(5)     # Go to the 6th byte in the file
5
>>> f.read(1)
b'5'
>>> f.seek(-3, 2) # Go to the 3rd byte before the end
13
>>> f.read(1)
b'd'
```
当你使用完一个文件时，调用 f.close() 方法就可以关闭它并释放其占用的所有系统资源。

用关键字 with 处理文件对象是个好习惯。它的先进之处在于文件用完后会自动关闭，就算发生异常也没关系。它是 try-finally 块的简写:
```python
>>> with open('/tmp/workfile', 'r') as f:
...     read_data = f.read()
>>> f.closed
True
```
文件对象还有一些不太常用的附加方法，比如 isatty() 和 truncate() 在库参考手册中有文件对象的完整指南。

####pickle 模块

我们可以很容易的读写文件中的字符串。数值就要多费点儿周折，因为 read() 方法只会返回字符串，应该将其传入 int() 这样的方法中，就可以将 '123' 这样的字符转为对应的数值 123。不过，当你需要保存更为复杂的数据类型，例如列表、字典，类的实例，事情就会变得更复杂了。

好在用户不必要非得自己编写和调试保存复杂数据类型的代码。 Python 提供了一个名为 pickle 的标准模块。这是一个令人赞叹的模块，几乎可以把任何 Python 对象 （甚至是一些 Python 代码段！）表达为为字符串，这一过程称之为封装 （ pickling ）。从字符串表达出重新构造对象称之为拆封（ unpickling ）。封装状态中的对象可以存储在文件或对象中，也可以通过网络在远程的机器之间传输。

如果你有一个对象 x ，一个以写模式打开的文件对象 f ，封装对象的最简单的方法只需要一行代码:
```python
pickle.dump(x, f)
```
如果 f 是一个以读模式打开的文件对象，就可以重装拆封这个对象:
```python
x = pickle.load(f)
```

pickle 是存储 Python 对象以供其它程序或其本身以后调用的标准方法。提供这一组技术的是一个 持久化 对象（ persistent object ）。因为 pickle 的用途很广泛，很多 Python 扩展的作者都非常注意类似矩阵这样的新数据类型是否适合封装和拆封。