title: coffeescript学习
date: 2014-10-13 09:13:33
tags: js
categories:
---
CoffeeScript 是一门编译到 JavaScript 的小巧语言. 在 Java 般笨拙的外表下, JavaScript 其实有着一颗华丽的心脏. CoffeeScript 尝试用简洁的方式展示 JavaScript 优秀的部分.
CoffeeScript 的指导原则是: "她仅仅是 JavaScript". 代码一一对应地编译到 JS, 不会在编译过程中进行解释. 已有的 JavaScript 类库可以无缝地和 CoffeeScript 搭配使用, 反之亦然. 编译后的代码是可读的, 且经过美化, 能在所有 JavaScript 环境中运行, 并且应该和对应手写的 JavaScript 一样快或者更快.

Official Website: 

* http://coffeescript.org/ (English)
* http://coffee-script.org/ (Chinese)

书籍：深入浅出Coffeescript

##安装

```bash
npm install -g coffee-script
```
如果你希望安装 master 分支上最新的 CoffeeScript, 你可以从源码仓库 克隆 CoffeeScript, 或直接下载源码. 还有通过 npm 方式安装 master 分支最新的 CoffeeScript 编译器:
```bash
npm install -g http://github.com/jashkenas/coffee-script/tarball/master
```
或者你想将其安装到 /usr/local, 而不用 npm 进行管理, 进入 coffee-script 目录执行:
```bash
sudo bin/cake install
```

##用法
安装之后, 你应该可以运行 coffee 命令以执行脚本, 编译 .coffee 文件到 .js 文件, 和提供一个交互式的 REPL.  coffee 命令有下列参数:

* -c, --compile   编译一个 .coffee 脚本到一个同名的 .js 文件.
* -m, --map	随 JavaScript 文件一起生成 source maps. 并且在 JavaScript 里加上 sourceMappingURL 指令.
* -i, --interactive	启动一个交互式的 CoffeeScript 会话用来尝试一些代码片段. 等同于执行 coffee 而不加参数.
* -o, --output [DIR]	将所有编译后的 JavaScript 文件写到指定文件夹. 与 --compile 或 --watch 搭配使用
* -j, --join [FILE]	编译之前, 按参数传入顺序连接所有脚本到一起, 编译后写到指定的文件. 对于编译大型项目有用.
* -w, --watch	监视文件改变, 任何文件更新时重新执行命令.
* -p, --print	JavaScript 直接打印到 stdout 而不是写到一个文件.
* -s, --stdio	将 CoffeeScript 传递到 STDIN 后从 STDOUT 获取 JavaScript. 对其他语言写的进程有好处. 比如: `cat src/cake.coffee | coffee -sc`
* -l, --literate	将代码作为 Literate CoffeeScript 解析. 只会在从 stdio 直接传入代码或者处理某些没有后缀的文件名需要写明这点.
* -e, --eval	直接从命令行编译和打印一小段 CoffeeScript. 比如:
coffee -e "console.log num for num in [10..1]"
* -b, --bare	编译到 JavaScript 时去掉顶层函数的包裹.
* -t, --tokens	不对 CoffeeScript 进行解析, 仅仅进行 lex, 打印出 token stream: [IDENTIFIER square] [ASSIGN =] [PARAM_START (] ...
* -n, --nodes	不对 CoffeeScript 进行编译, 仅仅 lex 和解析, 打印 parse tree:
* --nodejs	node 命令有一些实用的参数, 比如--debug, --debug-brk, --max-stack-size, 和 --expose-gc. 用这个参数直接把参数转发到 Node.js. 重复使用 --nodejs 来传递多个参数.

##Literate CoffeeScript

除了被作为一个普通的编程语言, CoffeeScript 也可以在 "literate" 模式下编写。 如果你以 .litcoffee 为扩展名命名你的文件, 你可以把它当作 Markdown 文件来编写 — 此文档恰好也是一份可执行的 CoffeeScript 代码, 编译器将会把所有的缩进块 (Markdown 表示源代码的方式) 视为代码, 其他部分则为注释.

##语言手册

首先, 一些基础, CoffeeScript 使用显式的空白来区分代码块. 你不需要使用分号 ; 来关闭表达式, 在一行的结尾换行就可以了(尽管分号依然可以用来把多行的表达式简写到一行里). 不需要再用花括号来 { } 包裹代码快, 在 函数, if 表达式, switch, 和 try/catch 当中使用缩进.
传入参数的时候, 你不需要再使用圆括号来表明函数被执行. 隐式的函数调用的作用范围一直到行尾或者一个块级表达式. 

##函数

函数通过一组可选的圆括号包裹的参数, 一个箭头, 一个函数体来定义. 一个空的函数像是这样:  ->
```coffeescript
square = (x) -> x * x
cube   = (x) -> square(x) * x
```
编译:
```javascript
var cube, square;
square = function(x) {
  return x * x;
};
cube = function(x) {
  return square(x) * x;
};
```
一些函数函数参数会有默认值, 当传入的参数的不存在 (null 或者 undefined) 时会被使用.
```coffeescript
fill = (container, liquid = "coffee") ->
  "Filling the #{container} with #{liquid}..."
```

##对象和数组

CoffeeScript 中对象和数组的字面量看起来很像在 JavaScript 中的写法. 如果单个属性被写在自己的一行里, 那么逗号是可以省略的. 和 YAML 类似, 对象可以用缩进替代花括号来声明.
```
song = ["do", "re", "mi", "fa", "so"]
singers = {Jagger: "Rock", Elvis: "Roll"}
bitlist = [
  1, 0, 1
  0, 0, 1
  1, 1, 0
]
kids =
  brother:
    name: "Max"
    age:  11
  sister:
    name: "Ida"
    age:  9
```
编译:
```
var bitlist, kids, singers, song;
song = ["do", "re", "mi", "fa", "so"];
singers = {
  Jagger: "Rock",
  Elvis: "Roll"
};
bitlist = [1, 0, 1, 0, 0, 1, 1, 1, 0];
kids = {
  brother: {
    name: "Max",
    age: 11
  },
  sister: {
    name: "Ida",
    age: 9
  }
};
```
JavaScript 里, 你不能使用不添加引号的保留字段作为属性名称, 比如 class. CoffeeScript 里作为键出现的保留字会被识别并补上引号, 所以你不用有额外的操心(比如说, 使用 jQuery 的时候).
```
$('.account').attr class: 'active'
log object.class
```
编译:
```
$('.account').attr({
  "class": 'active'
});
log(object["class"]);
```

##词法作用域和变量安全

CoffeeScript 编译器会考虑所有变量, 保证每个变量都在词法域里适当地被定义 — 你永远不需要自己去写 var.
```
outer = 1
changeNumbers = ->
  inner = -1
  outer = 10
inner = changeNumbers()
```
编译:
```
var changeNumbers, inner, outer;
outer = 1;
changeNumbers = function() {
  var inner;
  inner = -1;
  return outer = 10;
};
inner = changeNumbers();
```
尽管要说清楚会受到文档长度限制, 函数的所有 CoffeeScript 结果都被一个匿名函数包裹: (function(){ ... })(); 这层安全的封装, 加上自动生成的 var 关键字, 使得不小心污染全局命名空间很难发生.
如果你希望创建一个其他脚本也能使用的顶层变量, 那么将其作为赋值在 window 上, 或者在 CommonJS 里的 exports 上. 存在操作符(existential operator)可以帮你写出一个可靠的方式找到添加位置; 比如你的目标是同时满足 CommonJS 和浏览器: exports ? this

##if, else, unless 和条件赋值
```
mood = greatlyImproved if singing
if happy and knowsIt
  clapsHands()
  chaChaCha()
else
  showIt()
date = if friday then sue else jill
```
编译:
```
var date, mood;
if (singing) {
  mood = greatlyImproved;
}
if (happy && knowsIt) {
  clapsHands();
  chaChaCha();
} else {
  showIt();
}
date = friday ? sue : jill;
```

##变参(splats)...

使用 JavaScript 的 arguments 对象是一种处理接收不定数量个参数的函数常用办法. CoffeeScript 在函数定义和调用里提供了变参(splats) ... 的语法, 让不定个数的参数使用起来更愉悦一些.
```
gold = silver = rest = "unknown"
awardMedals = (first, second, others...) ->
  gold   = first
  silver = second
  rest   = others
contenders = [
  "Michael Phelps"
  "Liu Xiang"
  "Yao Ming"
  "Allyson Felix"
  "Shawn Johnson"
  "Roman Sebrle"
  "Guo Jingjing"
  "Tyson Gay"
  "Asafa Powell"
  "Usain Bolt"
]
awardMedals contenders...
alert "Gold: " + gold
alert "Silver: " + silver
alert "The Field: " + rest
```
编译:
```
var awardMedals, contenders, gold, rest, silver,
  __slice = [].slice;
gold = silver = rest = "unknown";
awardMedals = function() {
  var first, others, second;
  first = arguments[0], second = arguments[1], others = 3 <= arguments.length ? __slice.call(arguments, 2) : [];
  gold = first;
  silver = second;
  return rest = others;
};
contenders = ["Michael Phelps", "Liu Xiang", "Yao Ming", "Allyson Felix", "Shawn Johnson", "Roman Sebrle", "Guo Jingjing", "Tyson Gay", "Asafa Powell", "Usain Bolt"];
awardMedals.apply(null, contenders);
alert("Gold: " + gold);
alert("Silver: " + silver);
alert("The Field: " + rest);
```

##循环和推导式

你可以使用CoffeeScript将大多数的循环写成基于数组、对象或范围的推导式(comprehensions)。 推导式替代（编译为）for循环，并且可以使用可选的子句和数组索引值。 不同于for循环，数组的推导式是表达式，可以被返回和赋值。
```
# 吃午饭.
eat food for food in ['toast', 'cheese', 'wine']
# 精致的五道菜.
courses = ['greens', 'caviar', 'truffles', 'roast', 'cake']
menu i + 1, dish for dish, i in courses
# 注重健康的一餐.
foods = ['broccoli', 'spinach', 'chocolate']
eat food for food in foods when food isnt 'chocolate'
```
推导式可以适用于其他一些使用循环的地方，例如each/forEach, map，或者select/filter，例如： `shortNames = (name for name in list when name.length < 5)`
如果你知道循环的开始与结束，或者希望以固定的跨度迭代，你可以在范围推导式中 指定开始与结束。
```
countdown = (num for num in [10..1])
```
编译:
```
var countdown, num;
countdown = (function() {
  var _i, _results;
  _results = [];
  for (num = _i = 10; _i >= 1; num = --_i) {
    _results.push(num);
  }
  return _results;
})();
```
注意：上面的例子中我们展示了如何将推导式赋值给变量，CoffeeScript总是将 每个循环项收集到一个数组中。但是有时候以循环结尾的函数运行的目的就是 它们的副作用(side-effects)。这种情况下要注意不要意外的返回推导式的结果， 而是在函数的结尾增加一些有意义的返回值—例如true — 或 null。

在推导式中使用by子句，可以实现以固定跨度迭代范围值：  evens = (x for x in [0..10] by 2)

推导式也可以用于迭代对象中的key和value。在推导式中使用of 来取出对象中的属性，而不是数组中的值。
```
yearsOld = max: 10, ida: 9, tim: 11
ages = for child, age of yearsOld
  "#{child} is #{age}"
```
如果你希望仅迭代在当前对象中定义的属性，通过hasOwnProperty检查并 避免属性是继承来的，可以这样来写：
```
for own key, value of object
```
CoffeeScript仅提供了一种底层循环，即while循环。与JavaScript中的while 循环的主要区别是，在CoffeeScript中while可以作为表达式来使用， 而且可以返回一个数组，该数组包含每个迭代项的迭代结果。
```
if this.studyingEconomics
  buy()  while supply > demand
  sell() until supply > demand
```
为了更好的可读性，until关键字等同于while not, loop关键字 等同于while true。
局部参数的传递(很好的实例):
```
for filename in list
  do (filename) ->
    fs.readFile filename, (err, contents) ->
      compile filename, contents.toString()
```

##Array Slicing and Splicing with Ranges

3..6 ==>  (3, 4, 5, 6) || (3...6) ==> (3, 4, 5)
```
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9]
start   = numbers[0..2]
middle  = numbers[3...-2]
end     = numbers[-2..]
copy    = numbers[..]
numbers[3..6] = [-3, -4, -5, -6]
```
编译:
```
var copy, end, middle, numbers, start;
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9];
start = numbers.slice(0, 3);
middle = numbers.slice(3, -2);
end = numbers.slice(-2);
copy = numbers.slice(0);
[].splice.apply(numbers, [3, 4].concat(_ref = [-3, -4, -5, -6])), _ref;
```

##一切都是表达式（至少,尽可能）

方法返回的是最后执行语句的结果.你也可以使用return语句。
(three = 3) ==> 直接转换成变量的声明。
关于闭包，使用（）==> ()的内容作为一个闭包，执行后作为一个值返回给左边的参数！ ，传参使用[]
```
# 前十个全局属性(变量).
globals = (name for name of window)[0...10]
```
编译:
```
var globals, name;
globals = ((function() {
  var _results;
  _results = [];
  for (name in window) {
    _results.push(name);
  }
  return _results;
})()).slice(0, 10);
```
像try/catch的这类事物直接转换成方法调用:
```
alert(
  try
    nonexistent / undefined
  catch error
    "And the error is ... #{error}"
)
```
编译:
```
var error;
alert((function() {
  try {
    return nonexistent / void 0;
  } catch (_error) {
    error = _error;
    return "And the error is ... " + error;
  }
})());
```
在javascript中有少数的声明不会被转换成表达式，比如break、continue和return。

##操作符和别名

|CoffeeScript|JavaScript|
|:---------:|:-------:|
| is	| ===| 
| isnt	| !==| 
| not	| !| 
| and	| &&| 
| or	| |||
| true, yes, on	| true| 
| false, no, off	| false| 
| @, this	| this| 
| of	| in| 
| in	| no JS equivalent| 
| a ** b	| Math.pow(a, b)| 
| a // b	| Math.floor(a / b)| 
| a %% b	| (a % b + b) % b| 

##扩展操作符 => ?

变量名后面加上？如果该变量是null或者是undefined，则为false，否则为true。
```
solipsism = true if mind? and not world?
speed = 0
speed ?= 15
footprints = yeti ? "bear"
```
编译:
```
var footprints, solipsism, speed;
if ((typeof mind !== "undefined" && mind !== null) && (typeof world === "undefined" || world === null)) {
  solipsism = true;
}
speed = 0;
if (speed == null) {
  speed = 15;
}
footprints = typeof yeti !== "undefined" && yeti !== null ? yeti : "bear";
```
?()用于检查一个方法是否存在，?.用于判断字段是否为null，存在就返回否则就返回默认值0
```
zip = lottery.drawWinner?().address?.zipcode
```
编译:
```
var zip, _ref;
zip = typeof lottery.drawWinner === "function" ? (_ref = lottery.drawWinner().address) != null ? _ref.zipcode : void 0 : void 0;
```

##类、接口和Super

定义一个类：class
```coffee
class Animal
  constructor: (@name) ->
  move: (meters) ->
    alert @name + " moved #{meters}m."
class Snake extends Animal
  move: ->
    alert "Slithering..."
    super 5
class Horse extends Animal
  move: ->
    alert "Galloping..."
    super 45
sam = new Snake "Sammy the Python"
tom = new Horse "Tommy the Palomino"
sam.move()
tom.move()
```
原型链的写入使用操作符::
```
String::dasherize = ->
  this.replace /_/g, "-"
```
编译:
```
String.prototype.dasherize = function() {
  return this.replace(/_/g, "-");
};
```
有关元编程(定义类时使用可执行的代码。)，在定义类时写入原型链使用@操作符：@property: value定义原型链上的属性；@attr 'title', type: 'text'定义原型链上的函数。
```
class a
  @attr 'title', type: 'text'
```
编译:
```
var a;
a = (function() {
  function a() {}
  a.attr('title', {
    type: 'text'
  });
  return a;
})();
```

##结构赋值

平行赋值:
```
theBait   = 1000
theSwitch = 0
[theBait, theSwitch] = [theSwitch, theBait]
```
这对于返回数组的形式的函数很有用。
```
weatherReport = (location) ->
  # 发起一个 Ajax 请求获取天气...
  [location, 72, "Mostly Sunny"]
[city, temp, forecast] = weatherReport "Berkeley, CA"
```
对于多重深度的结构(数组或者对象)依然可以使用结构赋值。
```
futurists =
  sculptor: "Umberto Boccioni"
  painter:  "Vladimir Burliuk"
  poet:
    name:   "F.T. Marinetti"
    address: [
      "Via Roma 42R"
      "Bellagio, Italy 22021"
    ]
{poet: {name, address: [street, city]}} = futurists
```
Destructuring assignment can even be combined with splats.
```
tag = "<impossible>"
[open, contents..., close] = tag.split("")
#open='<',close='>',contents='impossible'
```
用于字符串的分割:
```
text = "Every literary critic believes he will
        outwit history and have the last word"
[first, ..., last] = text.split " "
```
结构赋值用于对象属性的赋值：
```
class Person
  constructor: (options) -> 
    {@name, @age, @height} = options
tim = new Person age: 4
```

##Function binding(函数作用域上下文)

使用=>将父类的Binding引入子类的Binding。类比于ruby的Binding。如果使用->替代=>就会使子Binding访问@variable失败！因为他会直接被翻译为this.variable，而此时的作用域中this没有父作用域中的那个属性：
```
Account = (customer, cart) ->
  @customer = customer
  @cart = cart
  $('.shopping_cart').bind 'click', (event) =>
    @customer.purchase @cart
```
编译:
```
var Account;
Account = function(customer, cart) {
  this.customer = customer;
  this.cart = cart;
  return $('.shopping_cart').bind('click', (function(_this) {
    return function(event) {
      return _this.customer.purchase(_this.cart);
    };
  })(this));
};
```

##内嵌的JavaScript

使用反引号内嵌javascript代码。
```
hi = `function() {
  return [document.title, "Hello JavaScript"].join(": ");
}`
```

##Switch/When/Else

coffeescript的switch语句不需要写break语句.
```
switch day
  when "Mon" then go work
  when "Tue" then go relax
  when "Thu" then go iceFishing
  when "Fri", "Sat"
    if day is bingoDay
      go bingo
      go dancing
  when "Sun" then go church
  else go work
```
switch语句条件中也可以使用比较表达式。
```
score = 76
grade = switch
  when score < 60 then 'F'
  when score < 70 then 'D'
  when score < 80 then 'C'
  when score < 90 then 'B'
  else 'A'
```
编译:
一个一个的比较，然后找到一个case来执行。
```
var grade, score;
score = 76;
grade = (function() {
  switch (false) {
    case !(score < 60):
      return 'F';
    case !(score < 70):
      return 'D';
    case !(score < 80):
      return 'C';
    case !(score < 90):
      return 'B';
    default:
      return 'A';
  }
})();
```

##Try/Catch/Finally

```
try
  allHellBreaksLoose()
  catsAndDogsLivingTogether()
catch error
  print error
finally
  cleanUp()
```

##链比较

这是继承python的特性，判断范围。
```
cholesterol = 127
healthy = 200 > cholesterol > 60 //healthy=true
```

##字符串插操作，块字符串和块注释

在字符串中插入变值，可以使用#{ ... }
```
author = "Wittgenstein"
quote  = "A picture is a fact. -- #{ author }"
sentence = "#{ 22 / 7 } is a decent approximation of π"
```
coffeescript支持字符串写在多行中，缩进被忽略。多行组成字符串时会加入一个空格，除非使用反斜线\。
```
mobyDick = "Call me Ishmael. Some years ago --\
  never mind how long precisely -- having little
  or no money in my purse, and nothing particular
  to interest me on shore, I thought I would sail
  about a little and see the watery part of the
  world..."
```
使用下面的形式，可以使块字符串保持原有的格式！
```
html = """
       <strong>
         cup of coffeescript
       </strong>
       """
```
以下是块注释的形式，#conetent是单行注释。
```
###
SkinnyMochaHalfCaffScript Compiler v1.0
Released under the MIT License
###
```

##块正则表达式

coffeescript支持块正则表达式，会忽略缩进和空格。开始和结尾使用///是块注释的形式。
```
OPERATOR = /// ^ (
  ?: [-=]>             # 函数
   | [-+*/%<>&|^!?=]=  # 复合赋值 / 比较
   | >>>=?             # 补 0 右移
   | ([-+:])\1         # 双写
   | ([&|<>])\2=?      # 逻辑 / 移位
   | \?\.              # soak 访问
   | \.{2,3}           # 范围或者 splat
) ///
```

##Cake, and Cakefiles

coffeescript提供了一个类似于make和rake的工具，称之为cake。用于编译和检测coffeescript代码的任务。任务被定义在cakefile的文件中。执行时可以使用 `cake [task] `的命令，要想查看所有的task可以直接输入 `cake` 命令。
任务的定义使用coffeescript来书写。所以你可以加入任意的代码。定义一个有名字，有详细说明，当任务运行时调用函数的任务.如果你的任务需要来自控制台的选项，你可以用或长或短的标志来定义这个选项并且它会在这个选项的对象中可以使用。这里就是一个任务，它使用node.js的api去重建CoffeeScript的解析器。
```
fs = require 'fs'
option '-o', '--output [DIR]', 'directory for compiled code'
task 'build:parser', 'rebuild the Jison parser', (options) ->
  require 'jison'
  code = require('./lib/grammar').parser.generate()
  dir  = options.output or 'lib'
  fs.writeFile "#{dir}/parser.js", code
```
如果你要在一个任务中包含其他的任务，你可以使用invoke函数：
```
invoke `build` 
```
,cake是将coffeescript函数提供给命令行的最简单方式。所以不要指望任何花哨内置。如果你需要依赖或者异步回调，最好的方式是将它们写在你的代码之中而不是在cake任务中。

##Source Maps

###What is Source Map

Source Map是一个独立的map文件，与源码在同一个目录下.JavaScript脚本正变得越来越复杂。大部分源码（尤其是各种函数库和框架）都要经过转换，才能投入生产环境。
常见的源码转换，主要是以下三种情况：

* 压缩，减小体积。比如jQuery 1.9的源码，压缩前是252KB，压缩后是32KB。
* 多个文件合并，减少HTTP请求数。
* 其他语言编译成JavaScript。最常见的例子就是CoffeeScript。

这三种情况，都使得实际运行的代码不同于开发代码，除错（debug）变得困难重重。通常，JavaScript的解释器会告诉你，第几行第几列代码出错。但是，这对于转换后的代码毫无用处。举例来说，jQuery 1.9压缩后只有3行，每行3万个字符，所有内部变量都改了名字。你看着报错信息，感到毫无头绪，根本不知道它所对应的原始位置。这就是Source map想要解决的问题。
简单说，Source map就是一个信息文件，里面储存着位置信息。也就是说，转换后的代码的每一个位置，所对应的转换前的位置。有了它，出错的时候，除错工具将直接显示原始代码，而不是转换后的代码。这无疑给开发者带来了很大方便。目前，暂时只有Chrome浏览器支持这个功能。在Developer Tools的Setting设置中，确认选中"Enable source maps"。

###如何启用Source map

正如前文所提到的，只要在转换后的代码尾部，加上一行就可以了。
```
　　//@ sourceMappingURL=/path/to/file.js.map
```
map文件可以放在网络上，也可以放在本地文件系统。

###如何生成Source map

最常用的方法是使用Google的Closure编译器。生成命令的格式如下：
```bash
java -jar compiler.jar \ 
　　　　--js script.js \
　　　　--create_source_map ./script-min.js.map \
　　　　--source_map_format=V3 \
　　　　--js_output_file script-min.js
```
各个参数的意义如下：

* js： 转换前的代码文件
* create_source_map： 生成的source map文件
* source_map_format：source map的版本，目前一律采用V3。
* js_output_file： 转换后的代码文件。

###Source map的格式

整个文件就是一个JavaScript对象，可以被解释器读取。它主要有以下几个属性：

* version：Source map的版本，目前为3。
* file：转换后的文件名。
* sourceRoot：转换前的文件所在的目录。如果与转换前的文件在同一目录，该项为空。
* sources：转换前的文件。该项是一个数组，表示可能存在多个文件合并。
* names：转换前的所有变量名和属性名。
* mappings：记录位置信息的字符串，下文详细介绍。

###mappings属性

下面才是真正有趣的部分：两个文件的各个位置是如何一一对应的。关键就是map文件的mappings属性。这是一个很长的字符串，它分成三层。

* 第一层是行对应，以分号（;）表示，每个分号对应转换后源码的一行。所以，第一个分号前的内容，就对应源码的第一行，以此类推。
* 第二层是位置对应，以逗号（,）表示，每个逗号对应转换后源码的一个位置。所以，第一个逗号前的内容，就对应该行源码的第一个位置，以此类推。
* 第三层是位置转换，以VLQ编码表示，代表该位置对应的转换前的源码位置。

Please go to [this](http://www.ruanyifeng.com/blog/2013/01/javascript_source_map.html) website to see more information!

CoffeeScript 1.6.1以上支持Source Map（告知你哪段js代码对应哪段coffeescript代码）。浏览器的调试器可以使用map来显示原始的代码。在生成js代码的同时生成map文件可以在编译命令中使用-m或者--map选项。

##"text/coffeescript"脚本标签

coffeescript代码可以直接在浏览器中运行，首先你要下载一个解释器extras/coffee-script.js，将其也加入到页面当中，它就可以编译并运行coffeescript代码。