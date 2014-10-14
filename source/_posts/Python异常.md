title: Python异常
date: 2014-10-14 22:25:36
tags: python
categories:
---
##异常处理

通过编程处理选择的异常是可行的。 看一下下面的例子：它会一直要求用户输入，直到输入一个合法的整数为止，但允许用户中断这个程序（使用 Control-C 或系统支持的任何方法）。 注意：用户产生的中断会引发一个 KeyboardInterrupt 异常。
```python
>>> while True:
...     try:
...         x = int(raw_input("Please enter a number: "))
...         break
...     except ValueError:
...         print "Oops!  That was no valid number.  Try again..."
...
```
如果发生了一个异常，在 except 子句中没有与之匹配的分支，它就会传递到上一级 try 语句中。如果最终仍找不到对应的处理语句，它就成为一个 未处理异常 ，终止程序运行，显示提示信息。
一个 try 语句可能包含多个 except 子句，分别指定处理不同的异常。至多只会有一个分支被执行。异常处理程序只会处理对应的 try 子句中发生的异常，在同一个 try 语句中，其他子句中发生的异常则不作处理。一个 except 子句可以在括号中列出多个异常的名字，例如:
```python
... except (RuntimeError, TypeError, NameError):
...     pass
```
最后一个 except 子句可以省略异常名称，以作为通配符使用。 你需要慎用此法，因为它会轻易隐藏一个实际的程序错误！ 可以使用这种方法打印一条错误信息，然后重新抛出异常（允许调用者处理这个异常):
```python
import sys
try:
    f = open('myfile.txt')
    s = f.readline()
    i = int(s.strip())
except IOError as e:
    print "I/O error({0}): {1}".format(e.errno, e.strerror)
except ValueError:
    print "Could not convert data to an integer."
except:
    print "Unexpected error:", sys.exc_info()[0]
    raise
```
try ... except 语句可以带有一个 else子句 ，该子句只能出现在所有 except 子句之后。当 try 语句没有抛出异常时，需要执行一些代码，可以使用这个子句。使用 else 子句比在 try 子句中附加代码要好，因为这样可以避免 try ... except 意外的截获本来不属于它们保护的那些代码抛出的异常。
发生异常时，可能会有一个附属值，作为异常的 参数 存在。这个参数是否存在、是什么类型，依赖于异常的类型。
在异常名（列表）之后，也可以为 except 子句指定一个变量。这个变量绑定于一个异常实例，它存储在 instance.args 的参数中。为了方便起见，异常实例定义了 __str__() ，这样就可以直接访问过打印参数而不必引用 .args 。 这种做法不受鼓励。相反，更好的做法是给异常传递一个参数（如果要传递多个参数，可以传递一个元组），把它绑定到 message 属性。一旦异常发生，它会在抛出前绑定所有指定的属性。
```python
>>> try:
...    raise Exception('spam', 'eggs')
... except Exception as inst:
...    print type(inst)    # the exception instance
...    print inst.args     # arguments stored in .args
...    print inst          # __str__ allows args to be printed directly,
...                         # but may be overridden in exception subclasses
...    x, y = inst.args     # unpack args
...    print 'x =', x
...    print 'y =', y
...
<class 'Exception'>
('spam', 'eggs')
('spam', 'eggs')
x = spam
y = eggs
```

##抛出异常

raise 语句允许程序员强制抛出一个指定的异常。要抛出的异常由 raise 的唯一参数标识。它必需是一个异常实例或异常类（继承自 Exception 的类）。

##用户自定义异常

在程序中可以通过创建新的异常类型来命名自己的异常（Python 类的内容请参见 类 ）。异常类通常应该直接或间接的从 Exception 类派生，例如:
```python
>>> class MyError(Exception):
...     def __init__(self, value):
...         self.value = value
...     def __str__(self):
...         return repr(self.value)
...
>>> try:
...     raise MyError(2*2)
... except MyError as e:
...     print 'My exception occurred, value:', e.value
...
```
在这个例子中，Exception 默认的 __init__() 被覆盖。新的方式简单的创建 value 属性。这就替换了原来创建 args 属性的方式。

异常类中可以定义任何其它类中可以定义的东西，但是通常为了保持简单，只在其中加入几个属性信息，以供异常处理句柄提取。如果一个新创建的模块中需要抛出几种不同的错误时，一个通常的作法是为该模块定义一个异常基类，然后针对不同的错误类型派生出对应的异常子类.	
与标准异常相似，大多数异常的命名都以 “Error” 结尾。

##定义清理行为

try 语句经由 break ，continue 或 return 语句退 出也一样会执行 finally 子句。

##预定义清理行为

有些对象定义了标准的清理行为，无论对象操作是否成功，不再需要该对象的时 候就会起作用。以下示例尝试打开文件并把内容打印到屏幕上:
```python
for line in open("myfile.txt"):
    print line
```
这段代码的问题在于在代码执行完后没有立即关闭打开的文件。这在简单的脚本 里没什么，但是大型应用程序就会出问题。 with 语句使得文件之类的对象可以 确保总能及时准确地进行清理:
```python
with open("myfile.txt") as f:
    for line in f:
        print line
```
语句执行后，文件 f 总会被关闭，即使是在处理文件中的数据时出错也一样。 其它对象是否提供了预定义的清理行为要查看它们的文档。