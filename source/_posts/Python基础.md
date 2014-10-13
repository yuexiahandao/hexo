title: Python基础
date: 2014-10-13 14:21:23
tags: python
categories:
---
网上中文资料：

* http://woodpecker.org.cn/diveintopython/index.html
* http://www.pythondoc.com/pythontutorial27/index.html

##Python的简介

可以源码安装也可以安装包安装。Python 扮演着两种角色。首先它是一个脚本解释器，可以从命令行运行脚本，也可以在脚本上双击，像运行其他应用程序一样。它还是一个交互 shell，可以执行任意的语句和表达式。这一点对调试、快速组建和测试相当有用。我甚至知道一些人把 Python 的交互 shell 当作计算器来使用！

下面的例子中，输入和输出分别由大于号和句号提示符（ >>> 和 ... ）标注：如果想重现这些例子，就要在解释器的提示符后，输入（提示符后面的）那些不包含提示符的代码行。需要注意的是在练习中遇到的从属提示符表示***你需要在最后多输入一个空行***，解释器才能知道这是一个多行命令的结束。

Python的注释以#开头。

## 将 Python 当做计算器

复数也得到支持；带有后缀 j 或 J 就被视为虚数。带有非零实部的复数写为 (real+imagj) ，或者可以用 complex(real, imag) 函数创建。复数的实部和虚部总是记为两个浮点数。要从复数 z 中提取实部和虚部，使用 z.real 和 z.imag 。函数 abs(z) 用于获取其模（浮点数）.交互模式中，最近一个表达式的值赋给变量 _ 。这样我们就可以把它当作一个桌面计算器，很方便的用于连续计算.此变量对于用户是只读的。不要尝试给它赋值 —— 你只会创建一个独立的同名局部变量，它屏蔽了系统内置变量的魔术效果。

##字符串

print 语句可以生成可读性更好的输出。字符串文本有几种方法分行。可以使用反斜杠为行结尾的连续字符串，它表示下一行在逻辑上是本行的后续内容,需要注意的是，在字符串中写入 \n ——结尾的反斜杠会被忽略。另外，字符串可以标识在一对三引号中： """ 或 ''' 。三引号中，不需要行属转义，它们已经包含在字符串中.

字符串可以由 + 操作符连接（粘到一起），可以由 * 重复.

相邻的两个字符串文本自动连接在一起，前面那行代码也可以写为 word ='Help' 'A';它只用于两个字符串文本，不能用于字符串表达式.

字符串也可以被截取（检索）。类似于 C ，字符串的第一个字符索引为 0 。没有独立的字符类型，字符就是长度为 1 的字符串。可以用 切片标注 法截取字符串：由两个索引分割的复本。索引切片可以有默认值，切片时，忽略第一个索引的话，默认为 0，忽略第二个索引，默认为字符串的长度.Python 能够优雅地处理那些没有意义的切片索引：一个过大的索引值（即下标值大于字符串实际长度）将被字符串实际长度所代替，当上边界比下边界大时（即切片左值大于右值）就返回空字符串。索引也可以是负数，这将导致从右边开始计算。请注意 -0 实际上就是 0 ，所以它不会导致从右边开始计算！
```python
>>> word[4]
'A'
>>> word[0:2]
'He'
>>> word[2:4]
'lp'
>>> word[:2]    # The first two characters
'He'
>>> word[2:]    # Everything except the first two characters
'lpA'
```

Python 字符串不可变。向字符串文本的某一个索引赋值会引发错误.

内置函数 len() 返回字符串长度.
```python
len(word)
```

## 关于 Unicode

从 Python 2.0 起，程序员们有了一个新的，用来存储文本数据的类型：Unicode 对象。在 Python 中创建 Unicode 字符串和创建普通的字符串一样简单:
```python
>>> u'Hello World !'
u'Hello World !'
>>> u'Hello\u0020World !'
u'Hello World !'
```

特别的，和普通字符串一样， Unicode 字符串也有原始模式。可以在引号前加 “ur”，Python 会采用 Raw-Unicode-Escape 编码（原始 Unicode 转义——译者）。如果有前缀为 ‘u’ 的数值，它也只会显示为 uXXXX 。
```python
>>> ur'Hello\u0020World !'
u'Hello World !'
```

作为这些编码标准的一部分，Python 提供了基于已知编码来创建 Unicode 字符串的整套方法。内置函数 unicode() 可以使用所有注册的 Unicode 编码（ COders 和 DECoders ）。 众所周知， Latin-1 ， ASCII ， UTF-8 和 UTF-16 之类的编码可以互相转换.将一个 Unicode 字符串打印或写入到文件中，或者使用 str() 转换时，转换操作以此为默认编码。
```python
>>> u"abc"
u'abc'
>>> str(u"abc")
'abc'
>>> u"盲枚眉"
u'\xe4\xf6\xfc'
>>> str(u"盲枚眉")
Traceback (most recent call last):
  File "<stdin>", line 1, in ?
UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-2: ordinal not in range(128)
```
为了将一个 Unicode 字符串转换为一个使用特定编码的 8 位字符串， Unicode 对象提供一个 encode() 方法，它接受编码名作为参数。编码名应该小写。
```python
>>> u"盲枚眉".encode('utf-8')
'\xc3\xa4\xc3\xb6\xc3\xbc'
```
如果有一个其它编码的数据，希望可以从中生成一个 Unicode 字符串，你可以使用 unicode() 函数，它接受编码名作为第二参数。
```python
>>> unicode('\xc3\xa4\xc3\xb6\xc3\xbc', 'utf-8')
u'\xe4\xf6\xfc'
```

##列表(list)

就像字符串索引，列表从 0 开始检索。列表可以被切片和连接.所有的切片操作都会返回新的列表，包含求得的元素。这意味着以下的切片操作返回列表 a 的一个浅拷贝的副本.不像 不可变的 字符串，列表允许修改元素.
```python
>>> a[:]
['spam', 'eggs', 100, 1234]
>>> a
['spam', 'eggs', 100, 1234]
>>> a[2] = a[2] + 23
>>> a
['spam', 'eggs', 123, 1234]
```
也可以对切片赋值，此操作可以改变列表的尺寸，或清空它:
```python
>>>a[:] = []
>>>a[0:2] = [1, 12]
```
内置函数 len() 同样适用于列表,允许嵌套列表（创建一个包含其它列表的列表）.你可以在列表末尾添加内容:
```python
>>> q = [2, 3]
>>> p = [1, q, 4]
>>> len(p)
3
>>> p[1].append('xtra')
>>> p
[1, [2, 3, 'xtra'], 4]
>>> q
[2, 3, 'xtra']
```
注意最后一个例子中， p[1] 和 q 实际上指向同一个对象！ 

##交互式命令行基础

冒号代表是一个块；逗号是禁止换行。
```python
>>> a, b = 0, 1
>>> while b < 1000:
...     print b,
...     a, b = b, a+b
...
1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987
```

##深入 Python 流程控制

###if 语句

```python
>>> if x < 0:
...      x = 0
...      print 'Negative changed to zero'
... elif x == 0:
...      print 'Zero'
... elif x == 1:
...      print 'Single'
... else:
...      print 'More'
```

###for 语句

```python
>>> # Measure some strings:
... a = ['cat', 'window', 'defenestrate']
>>> for x in a:
...     print x, len(x)
...
cat 3
window 6
defenestrate 12
```
在迭代过程中修改迭代序列不安全（只有在使用链表这样的可变序列时才会有这样的情况）。如果你想要修改你迭代的序列（例如，复制选择项），你可以迭代 它的复本.
```python
>>> for x in a[:]: # make a slice copy of the entire list
...    if len(x) > 6: a.insert(0, x)
...
>>> a
['defenestrate', 'cat', 'window', 'defenestrate']
```

###range() 函数

如果你需要一个数值序列，内置函数 range() 会很方便，它生成一个等差级数链表,range(10) 生成了一个包含 10 个值的链表，它用链表的索引值填充了这个长度为 10 的列表，所生成的链表中不包括范围中的结束值。也可以让 range 操作从另一个数值开始，或者可以指定一个不同的步进值（甚至是负数，有时这也被称为 “步长”）:
```python
>>> range(10)
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> range(5, 10)
[5, 6, 7, 8, 9]
>>> range(0, 10, 3)
[0, 3, 6, 9]
>>> range(-10, -100, -30)
[-10, -40, -70]
```
需要迭代链表索引的话，如下所示结合使用 range() 和 len():
```python
>>> a = ['Mary', 'had', 'a', 'little', 'lamb']
>>> for i in range(len(a)):
...     print i, a[i]
```
不过，这种场合可以方便地使用 enumerate().

###break 和 continue 语句, 以及循环中的 else 子句

***循环可以有一个 else 子句；它在循环迭代完整个列表（对于 for ）后或执行条件为 false （对于 while ）时执行，但循环被 break 中止的情况下不会执行。***

###pass 语句

pass 语句什么也不做。它用于那些语法上必须要有什么语句，但程序什么也不做的场合.
这通常用于创建最小结构的类.另一方面， pass 可以在创建新代码时用来做函数或控制体的占位符。可以让你在更抽象的级别上思考。 

###定义函数

```python
>>> def fib(n):    # write Fibonacci series up to n
...     """Print a Fibonacci series up to n."""
...     a, b = 0, 1
...     while a < n:
...         print a,
...         a, b = b, a+b
...
>>> fib(2000)
0 1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987 1597
```
函数体的第一行语句可以是可选的字符串文本，这个字符串是函数的文档字符串，或者称为 docstring 。
函数 调用 会为函数局部变量生成一个新的符号表。 确切地说，所有函数中的变量赋值都是将值存储在局部符号表。 变量引用首先在局部符号表中查找，然后是包含函数的局部符号表，然后是全局符号表，最后是内置名字表。 因此，全局变量不能在函数中直接赋值（除非用 global 语句命名），尽管他们可以被引用。
函数引用的实际参数在函数调用时引入局部符号表，因此，实参总是 传值调用 （这里的 值 总是一个对象引用 ，而不是该对象的值）。 [1] 一个函数被另一个函数调用时，一个新的局部符号表在调用过程中被创建。
一个函数定义会在当前符号表内引入函数名。 函数名指代的值（即函数体）存在一个被 Python 解释器认定为 用户自定义函数 的类型。 这个值可以赋予其他的名字（即变量名），然后它也可以被当做函数使用。 这可以作为通用的重命名机制:
```python
>>> fib
<function fib at 10042ed0>
>>> f = fib
>>> f(100)
0 1 1 2 3 5 8 13 21 34 55 89
>>> print fib(0)
None
```
return 语句从函数中返回一个值，不带表达式的 return 返回 None 。过程结束后也会返回 None 。

####默认参数值

最常用的一种形式是为一个或多个参数指定默认值。 这会创建一个可以使用比定义时允许的参数更少的参数调用的函数，例如:
```python
def ask_ok(prompt, retries=4, complaint='Yes or no, please!'):
    while True:
        ok = raw_input(prompt)
        if ok in ('y', 'ye', 'yes'):
            return True
        if ok in ('n', 'no', 'nop', 'nope'):
            return False
        retries = retries - 1
        if retries < 0:
            raise IOError('refusenik user')
        print complaint
```
这个例子还介绍了 in 关键字。它测定序列中是否包含某个确定的值。
默认值在函数 定义 作用域被解析，如下所示:
```python
i = 5
def f(arg=i):
    print arg
i = 6
f() #print 5
```
***重要警告***: 默认值只被赋值一次。这使得当默认值是可变对象时会有所不同，比如列表、字典或者大多数类的实例。例如，下面的函数在后续调用过程中会累积（前面）传给它的参数:
```python
def f(a, L=[]):
    L.append(a)
    return L
print f(1) #output [1]
print f(2) #output [1,2]
print f(3) #output [1,2,3]
```
如果你不想在随后的调用中共享默认值，可以像这样写函数:
```python
def f(a, L=None):
    if L is None:
        L = []
    L.append(a)
    return L
```

#### 关键字参数

```python
def parrot(voltage, state='a stiff', action='voom', type='Norwegian Blue'):
    print "-- This parrot wouldn't", action,
    print "if you put", voltage, "volts through it."
    print "-- Lovely plumage, the", type
    print "-- It's", state, "!"
```
接受一个必选参数 ( voltage ) 以及三个可选参数 ( state , action , 和 type )。 可以用以下的任一方法调用:
```python
parrot(1000)                                          # 1 positional argument
parrot(voltage=1000)                                  # 1 keyword argument
parrot(voltage=1000000, action='VOOOOOM')             # 2 keyword arguments
parrot(action='VOOOOOM', voltage=1000000)             # 2 keyword arguments
parrot('a million', 'bereft of life', 'jump')         # 3 positional arguments
parrot('a thousand', state='pushing up the daisies')  # 1 positional, 1 keyword
```

####可变参数列表

引入一个形如 **name 的参数时，它接收一个字典（参见 typesmapping ） ，该字典包含了所有未出现在形式参数列表中的关键字参数。这里可能还会组合使用一个形如 *name （下一小节详细介绍） 的形式参数，它接收一个元组（下一节中会详细介绍），包含了所有没有出现在形式参数列表中的参数值。（ *name 必须在 **name 之前出现） 例如，我们这样定义一个函数:
```python
def cheeseshop(kind, *arguments, **keywords):
    print "-- Do you have any", kind, "?"
    print "-- I'm sorry, we're all out of", kind
    for arg in arguments:
        print arg
    print "-" * 40
    keys = sorted(keywords.keys())
    for kw in keys:
        print kw, ":", keywords[kw]
```
它可以像这样调用:
```python
cheeseshop("Limburger", "It's very runny, sir.",
           "It's really very, VERY runny, sir.",
           shopkeeper='Michael Palin',
           client="John Cleese",
           sketch="Cheese Shop Sketch")
```
当然它会按如下内容打印:
```python
-- Do you have any Limburger ?
-- I'm sorry, we're all out of Limburger
It's very runny, sir.
It's really very, VERY runny, sir.
----------------------------------------
client : John Cleese
shopkeeper : Michael Palin
sketch : Cheese Shop Sketch
```
注意在打印 关键字 参数字典的内容前先调用 sort() 方法。否则的话，打印参数时的顺序是未定义的。

####参数列表的分拆

另有一种相反的情况: 当你要传递的参数已经是一个列表，但要调用的函数却接受分开一个个的参数值。这时候你要把已有的列表拆开来。例如内建函数 range() 需要独立的 start , stop 参数。 你可以在调用函数时加一个 * 操作符来自动把参数列表拆开:
```python
>>> list(range(3, 6))            # normal call with separate arguments
[3, 4, 5]
>>> args = [3, 6]
>>> list(range(*args))            # call with arguments unpacked from a list
[3, 4, 5]
```
以同样的方式，可以使用 ** 操作符分拆关键字参数为字典:
```python
>>> def parrot(voltage, state='a stiff', action='voom'):
...     print "-- This parrot wouldn't", action,
...     print "if you put", voltage, "volts through it.",
...     print "E's", state, "!"
...
>>> d = {"voltage": "four million", "state": "bleedin' demised", "action": "VOOM"}
>>> parrot(**d)
-- This parrot wouldn't VOOM if you put four million volts through it. E's bleedin' demised !
```

####Lambda 形式

出于实际需要，有几种通常在函数式编程语言例如 Lisp 中出现的功能加入到了 Python 中。通过 lambda 关键字，可以创建短小的匿名函数。这里有一个函数返回它的两个参数的和： lambda a, b: a+b 。 Lambda 形式可以用于任何需要的函数对象。出于语法限制，它们只能有一个单独的表达式。语义上讲，它们只是普通函数定义中的一个语法技巧。类似于嵌套函数定义，lambda 形式可以从外部作用域引用变量:
```python
>>> def make_incrementor(n):
...     return lambda x: x + n
...
>>> f = make_incrementor(42)
>>> f(0)
42
>>> f(1)
43
```

##文档字符串

第一行应该是关于对象用途的简介。简短起见，不用明确的陈述对象名或类型，因为它们可以从别的途径了解到（除非这个名字碰巧就是描述这个函数操作的动词）。这一行应该以大写字母开头，以句号结尾。

如果文档字符串有多行，第二行应该空出来，与接下来的详细描述明确分隔。接下来的文档应该有一或多段描述对象的调用约定、边界效应等。

Python 的解释器不会从多行的文档字符串中去除缩进，所以必要的时候应当自己清除缩进。这符合通常的习惯。第一行之后的第一个非空行决定了整个文档的缩进格式。（我们不用第一行是因为它通常紧靠着起始的引号，缩进格式显示的不清楚。）留白“相当于”是字符串的起始缩进。每一行都不应该有缩进，如果有缩进的话，所有的留白都应该清除掉。留白的长度应当等于扩展制表符的宽度（通常是8个空格）。

以下是一个多行文档字符串的示例:
```python
>>> def my_function():
...     """Do nothing, but document it.
...
...     No, really, it doesn't do anything.
...     """
...     pass
...
>>> print my_function.__doc__
    Do nothing, but document it.

    No, really, it doesn't do anything.
```

##编码风格

* 使用 4 空格缩进，而非 TAB。在小缩进（可以嵌套更深）和大缩进（更易读）之间，4空格是一个很好的折中。TAB 引发了一些混乱，最好弃用。
* 折行以确保其不会超过 79 个字符。这有助于小显示器用户阅读，也可以让大显示器能并排显示几个代码文件。

* 使用空行分隔函数和类，以及函数中的大块代码。
* 可能的话，注释独占一行
* 使用文档字符串
* 把空格放到操作符两边，以及逗号后面，但是括号里侧不加空格： a = f(1, 2) + g(3, 4) 。
* 统一函数和类命名。推荐类名用 驼峰命名， 函数和方法名用 小写_和_下划线。总是用 self 作为方法的第一个参数（关于类和方法的知识详见 初识类 ）。
* 不要使用花哨的编码，如果你的代码的目的是要在国际化 环境。 Python 的默认情况下，UTF-8，甚至普通的 ASCII 总是工作的最好。
* 同样，也不要使用非 ASCII 字符的标识符，除非是不同语种的会阅读或者维护代码。