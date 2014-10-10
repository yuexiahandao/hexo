title: NodeJs快速入门
date: 2014-10-10 21:03:22
tags: NodeJs
categories: NodeJs
---
##一、开始用Node.js编程
###Hello World
好了,让我们开始实现第一个 Node.js 程序吧。打开你常用的文本编辑器,在其中输入:

    console.log('Hello World');
    
将文件保存为 helloworld.js,打开终端,进入 helloworld.js 所在的目录,执行以下命令:

    node helloworld.js
    
如果一切正常,你将会在终端中看到输出 Hello World 。很简单吧?下面让我们来解释一下这个程序的细节。 console 是 Node.js 提供的控制台对象,其中包含了向标准输出写入的操作,如 console.log 、 console.error 等。 console.log 是我们最常用的输出指令,它和 C 语言中的 printf 的功能类似,也可以接受任意多个参数,支持 %d 、 %s 变量引用,例如:

    console.log('%s: %d', 'Hello', 25);
    
###Node.js 命令行工具

输入 `node --help`可以看到详细的帮助信息.
其中显示了 node 的用法,运行 Node.js 程序的基本方法就是执行 `node script.js` ,其中 script.js 1 是脚本的文件名。除了直接运行脚本文件外, `node --help` 显示的使用方法中说明了另一种输出 Hello World 的方式:

    $ node -e "console.log('Hello World');"
    
我们可以把要执行的语句作为 node -e 的参数直接执行。

**使用 node 的 REPL 模式**

REPL (Read-eval-print loop),即输入—求值—输出循环。如果你用过 Python,就会知道在终端下运行无参数的 python 命令或者使用 Python IDLE 打开的 shell,可以进入一个即时求值的运行环境。Node.js 也有这样的功能,运行无参数的 node 将会启动一个 JavaScript的交互式 shell.

###建立HTTP服务器
建立一个名为 app.js 的文件,内容为:

    var http = require('http');
    http.createServer(function(req, res) {
        res.writeHead(200, {'Content-Type': 'text/html'});
        res.write('<h1>Node.js</h1>');
        res.end('<p>Hello World</p>');
    }).listen(3000);
    console.log("HTTP server is listening at port 3000.");
接下来运行 `node app.js` 命令,打开浏览器访问 http://127.0.0.1:3000.

用 Node.js 实现的最简单的 HTTP 服务器就这样诞生了。这个程序调用了 Node.js 提供的http 模块,对所有 HTTP 请求答复同样的内容并监听 3000 端口。在终端中运行这个脚本时,我们会发现它并不像 Hello World 一样结束后立即退出,而是一直等待,直到按下 Ctrl +C 才会结束。这是因为 listen 函数中创建了事件监听器,使得 Node.js 进程不会退出事件循环。我们会在后面的章节中详细介绍这其中的奥秘。
**小技巧——使用 supervisor**
如果你有 PHP 开发经验,会习惯在修改 PHP 脚本后直接刷新浏览器以观察结果,而你在开发 Node.js 实现的 HTTP 应用时会发现,无论你修改了代码的哪一部份,都必须终止Node.js 再重新运行才会奏效。这是因为 Node.js 只有在第一次引用到某部份时才会去解析脚本文件,以后都会直接访问内存,避免重复载入,而 PHP 则总是重新读取并解析脚本(如果没有专门的优化配置)。Node.js的这种设计虽然有利于提高性能,却不利于开发调试,因为我们在开发过程中总是希望修改后立即看到效果,而不是每次都要终止进程并重启。
supervisor 可以帮助你实现这个功能,它会监视你对代码的改动,并自动重启 Node.js。使用方法很简单,首先使用 npm 安装 supervisor:

    $ npm install -g supervisor
    
接下来,使用 supervisor 命令启动 app.js:

    $ supervisor app.js
    
当代码被改动时,运行的脚本会被终止,然后重新启动。supervisor 这个小工具可以解决开发中的调试问题。

##二、异步式 I/O 与事件式编程
###阻塞和线程
异步式 I/O (Asynchronous I/O)或非阻塞式 I/O (Non-blocking I/O)则针对所有 I/O 操作不采用阻塞的策略。当线程遇到 I/O 操作时,不会以阻塞的方式等待 I/O 操作的完成或数据的返回,而只是将 I/O 请求发送给操作系统,继续执行下一条语句。当操作系统完成 I/O 操作时,以事件的形式通知执行 I/O 操作的线程,线程会在特定时候处理这个事件。为了处理异步 I/O,线程必须有事件循环,不断地检查有没有未处理的事件,依次予以处理。
阻塞模式下,一个线程只能处理一项任务,要想提高吞吐量必须通过多线程。而非阻塞模式下,一个线程永远在执行计算操作,这个线程所使用的 CPU 核心利用率永远是 100%,I/O 以事件的方式通知。在阻塞模式下,多线程往往能提高系统吞吐量,因为一个线程阻塞时还有其他线程在工作,多线程可以让 CPU 资源不被阻塞中的线程浪费。而在非阻塞模式下,线程不会被 I/O 阻塞,永远在利用 CPU。多线程带来的好处仅仅是在多核 CPU 的情况下利用更多的核,而Node.js的单线程也能带来同样的好处。这就是为什么 Node.js 使用了单线程、非阻塞的事件编程模式。
单线程事件驱动的异步式 I/O 比传统的多线程阻塞式 I/O 究竟好在哪里呢?简而言之,异步式 I/O 就是少了多线程的开销。对操作系统来说,创建一个线程的代价是十分昂贵的,需要给它分配内存、列入调度,同时在线程切换的时候还要执行内存换页,CPU 的缓存被清空,切换回来的时候还要重新从内存中读取信息,破坏了数据的局部性。
当然,异步式编程的缺点在于不符合人们一般的程序设计思维,容易让控制流变得晦涩难懂,给编码和调试都带来不小的困难。习惯传统编程模式的开发者在刚刚接触到大规模的异步式应用时往往会无所适从,但慢慢习惯以后会好很多。尽管如此,异步式编程还是较为困难,不过可喜的是现在已经有了不少专门解决异步式编程问题的库(如 async )。
###回调函数
让我们看看在 Node.js 中如何用异步的方式读取一个文件,下面是一个例子:

    var fs = require('fs');
    fs.readFile('file.txt', 'utf-8', function(err, data) {
        if (err) {
            console.error(err);
        } else {
            console.log(data);
        }
    });
    console.log('end.');//end先被输出
    
Node.js 也提供了同步读取文件的 API:

    var fs = require('fs');
    var data = fs.readFileSync('file.txt', 'utf-8');
    console.log(data);
    console.log('end.');//end最后被打印   
    
fs.readFile 调用时所做的工作只是将异步式 I/O 请求发送给了操作系统,然后立即返回并执行后面的语句,执行完以后进入事件循环监听事件。当 fs 接收到 I/O 请求完成的事件时,事件循环会主动调用回调函数以完成后续工作。因此我们会先看到 end. ,再看到file.txt 文件的内容。（执行完成后回调函数并没有立即执行，而是加入了事件队列，按照事件队列的先后顺序执行）。

###事件
在开发者看来,事件由 EventEmitter 对象提供。前面提到的 fs.readFile 和 http.createServer 的回调函数都是通过 EventEmitter 来实现的。下面我们用一个简单的例子说明 EventEmitter的用法:

    var EventEmitter = require('events').EventEmitter;
    var event = new EventEmitter();
    event.on('some_event', function() {
        console.log('some_event occured.');
    });
    setTimeout(function() {
        event.emit('some_event');
    }, 1000);
    
运行这段代码,1秒后控制台输出了 some_event occured. 。其原理是 event 对象注册了事件 some_event 的一个监听器,然后我们通过 setTimeout 在1000毫秒以后向event 对象发送事件 some_event ,此时会调用 some_event 的监听器。
**Node.js 的事件循环机制**

Node.js 在什么时候会进入事件循环呢?答案是 Node.js 程序由事件循环开始,到事件循环结束,所有的逻辑都是事件的回调函数,所以 Node.js 始终在事件循环中,程序入口就是事件循环第一个事件的回调函数。事件的回调函数在执行的过程中,可能会发出 I/O 请求或
直接发射(emit)事件,执行完毕后再返回事件循环,事件循环会检查事件队列中有没有未处理的事件,直到程序结束。图说明了事件循环的原理。

![图3-5](http://ww2.sinaimg.cn/large/7dfb3940jw1el6elz3diwj20cm0eyjru.jpg)

与其他语言不同的是, Node.js 没有显式的事件循环,类似 Ruby 的 EventMachine::run()
的函数在 Node.js 中是不存在的。Node.js 的事件循环对开发者不可见,由 libev库实现。libev支持多种类型的事件,如 ev_io 、 ev_timer 、 ev_signal 、 ev_idle 等,在 Node.js 中均被EventEmitter 封装。 libev 事件循环的每一次迭代,在 Node.js 中就是一次 Tick, libev 不断检查是否有活动的、可供检测的事件监听器,直到检测不到时才退出事件循环,进程结束。

##三、模块和包
模块(Module)和包(Package)是 Node.js 最重要的支柱。开发一个具有一定规模的程序不可能只用一个文件,通常需要把各个功能拆分、封装,然后组合起来,模块正是为了实现这种方式而诞生的。在浏览器 JavaScript 中,脚本模块的拆分和组合通常使用 HTML 的script 标签来实现。Node.js 提供了 require 函数来调用其他模块,而且模块都是基于文件的,机制十分简单。
我们经常把 Node.js 的模块和包相提并论,因为模块和包是没有本质区别的,两个概念也时常混用。如果要辨析,那么可以把包理解成是实现了某个功能模块的集合,用于发布和维护。对使用者来说,模块和包的区别是透明的,因此经常不作区分。

###什么是模块
模块是 Node.js 应用程序的基本组成部分,文件和模块是一一对应的。换言之,一个Node.js 文件就是一个模块,这个文件可能是 JavaScript 代码、JSON 或者编译过的 C/C++ 扩展。
在前面章节的例子中,我们曾经用到了 var http = require('http'), 其中 http是 Node.js 的一个核心模块,其内部是用 C++ 实现的,外部用 JavaScript 封装。我们通过require 函数获取了这个模块,然后才能使用其中的对象。
###创建及加载模块
####创建模块
Node.js 提供了 exports 和 require 两个对象,其中 exports 是模块公开的接口, require 用于从外部获取一个模块的接口,即所获取模块的 exports 对象。
让我们以一个例子来了解模块。创建一个 module.js 的文件,内容是:
```javascript
var name;
exports.setName = function(thyName) {
    name = thyName;
};
exports.sayHello = function() {
    console.log('Hello ' + name);
};
```
在同一目录下创建 getmodule.js,内容是:
```javascript
var myModule = require('./module');
myModule.setName('BYVoid');
myModule.sayHello();
```
在以上示例中,module.js 通过 exports 对象把 setName 和 sayHello 作为模块的访问接口,在 getmodule.js 中通过 require('./module') 加载这个模块,然后就可以直接访问 module.js 中 exports 对象的成员函数了。
这种接口封装方式比许多语言要简洁得多,同时也不失优雅,未引入违反语义的特性,符合传统的编程逻辑。在这个基础上,我们可以构建大型的应用程序,npm 提供的上万个模块都是通过这种简单的方式搭建起来的。
####单次加载
上面这个例子有点类似于创建一个对象,但实际上和对象又有本质的区别,因为require 不会重复加载模块,也就是说无论调用多少次 require, 获得的模块都是同一个。
我们在 getmodule.js 的基础上稍作修改:
```
var hello1 = require('./module');
hello1.setName('BYVoid');
var hello2 = require('./module');
hello2.setName('BYVoid 2');
hello1.sayHello();
```
运行后发现输出结果是 Hello BYVoid 2 ,这是因为变量 hello1 和 hello2 指向的是同一个实例,因此 hello1.setName 的结果被 hello2.setName 覆盖,最终输出结果是由后者决定的。
####覆盖 exports
有时候我们只是想把一个对象封装到模块中,例如:
```
function Hello() {
    var name;
    this.setName = function (thyName) {
        name = thyName;
    };
    this.sayHello = function () {
        console.log('Hello ' + name);
    };
};
exports.Hello = Hello;
```
此时我们在其他文件中需要通过 require('./singleobject').Hello来获取Hello对象,这略显冗余,可以用下面方法稍微简化:
```
function Hello() {
    var name;
    this.setName = function(thyName) {
        name = thyName;
    };
    this.sayHello = function() {
        console.log('Hello ' + name);
    };
};
module.exports = Hello;
```
这样就可以直接获得这个对象了:
```
var Hello = require('./hello');
hello = new Hello();
hello.setName('BYVoid');
hello.sayHello();
```
注意,模块接口的唯一变化是使用 module.exports = Hello 代替了 exports.Hello=Hello 。在外部引用该模块时,其接口对象就是要输出的 Hello 对象本身,而不是原先的exports 。
事实上, exports 本身仅仅是一个普通的空对象,即 {} ,它专门用来声明接口,本质上是通过它为模块闭包的内部建立了一个有限的访问接口。因为它没有任何特殊的地方,所以可以用其他东西来代替,譬如我们上面例子中的 Hello 对象。
不可以通过对 exports 直接赋值代替对 module.exports 赋值。exports 实际上只是一个和 module.exports 指向同一个对象的变量,它本身会在模块执行结束后释放,但 module 不会,因此只能通过指定module.exports 来改变访问接口。
####创建包
包是在模块基础上更深一步的抽象,Node.js 的包类似于 C/C++ 的函数库或者 Java/.Net的类库。它将某个独立的功能封装起来,用于发布、更新、依赖管理和版本控制。Node.js 根据 CommonJS 规范实现了包机制,开发了 npm(like ruby gem)来解决包的发布和获取需求。
Node.js 的包是一个目录,其中包含一个 JSON 格式的包说明文件 package.json。严格符合 CommonJS 规范的包应该具备以下特征:
* package.json 必须在包的顶层目录下;
* 二进制文件应该在 bin 目录下;
* JavaScript 代码应该在 lib 目录下;
* 文档应该在 doc 目录下;
* 单元测试应该在 test 目录下。

Node.js 对包的要求并没有这么严格,只要顶层目录下有 package.json,并符合一些规范即可。当然为了提高兼容性,我们还是建议你在制作包的时候,严格遵守 CommonJS 规范。
#####作为文件夹的模块
模块与文件是一一对应的。文件不仅可以是 JavaScript 代码或二进制代码,还可以是一个文件夹。最简单的包,就是一个作为文件夹的模块。下面我们来看一个例子,建立一个叫做 somepackage 的文件夹,在其中创建 index.js,内容如下:
```
exports.hello = function() {
    console.log('Hello.');
};
```
然后在 somepackage 之外建立 getpackage.js,内容如下:
```
var somePackage = require('./somepackage');
somePackage.hello();
```
运行 node getpackage.js,控制台将输出结果 Hello. 。
我们使用这种方法可以把文件夹封装为一个模块,即所谓的包。包通常是一些模块的集合,在模块的基础上提供了更高层的抽象,相当于提供了一些固定接口的函数库。通过定制package.json,我们可以创建更复杂、更完善、更符合规范的包用于发布。
#####package.json
在前面例子中的 somepackage 文件夹下,我们创建一个叫做 package.json 的文件,内容如下所示:
```
{
    "main" : "./lib/interface.js"
}
```
然后将 index.js 重命名为 interface.js 并放入 lib 子文件夹下。以同样的方式再次调用这个包,依然可以正常使用。
Node.js 在调用某个包时,会首先检查包中 package.json 文件的 main 字段,将其作为包的接口模块,如果 package.json 或 main 字段不存在,会尝试寻找 index.js 或 index.node 作为包的接口。
package.json 是 CommonJS 规定的用来描述包的文件,完全符合规范的 package.json 文件应该含有以下字段。
* name :包的名称,必须是唯一的,由小写英文字母、数字和下划线组成,不能包含空格。
* description :包的简要说明。
* version :符合语义化版本识别 规范的版本字符串。
* keywords :关键字数组,通常用于搜索。
* maintainers :维护者数组,每个元素要包含 name 、 email (可选)、 web (可选)字段。
* contributors :贡献者数组,格式与 maintainers 相同。包的作者应该是贡献者数组的第一个元素。
* bugs :提交bug的地址,可以是网址或者电子邮件地址。
* licenses :许可证数组,每个元素要包含 type (许可证的名称)和 url (链接到许可证文本的地址)字段。
* repositories :仓库托管地址数组,每个元素要包含 type (仓库的类型,如 git )、url (仓库的地址)和 path (相对于仓库的路径,可选)字段。
* dependencies :包的依赖,一个关联数组,由包名称和版本号组成。
下面是一个完全符合 CommonJS 规范的 package.json 示例:
```
{
    "name": "mypackage",
    "description": "Sample package for CommonJS. This package demonstrates the required elements of a CommonJS package.",
    "version": "0.7.0",
    "keywords": [
        "package",
        "example"
    ],
    "maintainers": [
        {
            "name": "Bill Smith",
            "email": "bills@example.com",
        }
    ],
    "contributors": [
        {
            "name": "BYVoid",
            "web": "http://www.byvoid.com/"
        }
    ],
    "bugs": {
        "mail": "dev@example.com",
        "web": "http://www.example.com/bugs"
    },
    "licenses": [
        {
            "type": "GPLv2",
            "url": "http://www.example.org/licenses/gpl.html"
        }
    ],
    "repositories": [
        {
            "type": "git",
            "url": "http://github.com/BYVoid/mypackage.git"
        }
    ],
    "dependencies": {
        "webkit": "1.2",
        "ssl": {
            "gnutls": ["1.0", "2.0"],
            "openssl": "0.9.8"
        }
    }
}
```
####Node.js包管理器
Node.js包管理器,即npm是 Node.js 官方提供的包管理工具 1 ,它已经成了 Node.js 包的标准发布平台,用于 Node.js 包的发布、传播、依赖控制。npm 提供了命令行工具,使你可以方便地下载、安装、升级、删除包,也可以让你作为开发者发布并维护包。
#####获取一个包
使用 npm 安装包的命令格式为:
    `npm [install/i] [package_name]`
例如你要安装 express ,可以在命令行运行:
    `$ npm install express`
或者:
    `$ npm i express`
#####本地模式和全局模式
npm在默认情况下会从http://npmjs.org 搜索或下载包,将包安装到当前目录的node_modules子目录下。
如果你熟悉 Ruby 的 gem 或者 Python 的 pip,你会发现 npm 与它们的行为不同,gem 或 pip 总是以全局模式安装,使包可以供所有的程序使用,而 npm 默认会把包安装到当前目录下。这反映了 npm 不同的设计哲学。如果把包安装到全局,可以提高程序的重复利用程度,避免同样的内容的多份副本,但坏处是难以处理不同的版本依赖。如果把包安装到当前目录,或者说本地,则不会有不同程序依赖不同版本的包的冲突问题,同时还减轻了包作者的 API 兼容性压力,但缺陷则是同一个包可能会被安装许多次。
在使用 npm 安装包的时候,有两种模式: 本地模式和全局模式。默认情况下我们使用 `npm install`命令就是采用本地模式,即把包安装到当前目录的 node_modules 子目录下。 Node.js的 require 在加载模块时会尝试搜寻 node_modules 子目录,因此使用 npm 本地模式安装的包可以直接被引用。npm 还有另一种不同的安装模式被成为全局模式,使用方法为:
`npm [install/i] -g [package_name]`
与本地模式的不同之处就在于多了一个参数 -g 。我们在 介绍 supervisor那个小节中使用了 `npm install -g supervisor` 命令,就是以全局模式安装 supervisor。
为什么要使用全局模式呢?多数时候并不是因为许多程序都有可能用到它,为了减少多重副本而使用全局模式,而是因为本地模式不会注册 PATH 环境变量。举例说明,我们安装supervisor 是为了在命令行中运行它,譬如直接运行 supervisor script.js,这时就需要在 PATH环境变量中注册 supervisor。npm 本地模式仅仅是把包安装到 node_modules 子目录下,其中的 bin 目录没有包含在 PATH 环境变量中,不能直接在命令行中调用。而当我们使用全局模式安装时, npm 会将包安装到系统目录,譬如 /usr/local/lib/node_modules/,同时 package.json 文件中 bin 字段包含的文件会被链接到 /usr/local/bin/。/usr/local/bin/ 是在 PATH 环境变量中默认定义的,因此就可以直接在命令行中运行 supervisor script.js 命令了。
使用全局模式安装的包并不能直接在 JavaScript 文件中用 require 获得,因为 require 不会搜索 /usr/local/lib/node_modules/。

#####创建全局链接

npm 提供了一个有趣的命令 npm link, 它的功能是在本地包和全局包之间创建符号链接。我们说过使用全局模式安装的包不能直接通过 require 使用,但通过 npm link 命令可以打破这一限制。举个例子,我们已经通过 npm install -g express 安装了 express ,这时在工程的目录下运行命令:
```
$ npm link express
```
我们可以在 node_modules 子目录中发现一个指向安装到全局的包的符号链接。通过这种方法,我们就可以把全局包当本地包来使用了。npm link 命令不支持 Windows。
除了将全局的包链接到本地以外,使用 npm link 命令还可以将本地的包链接到全局。使用方法是在包目录( package.json 所在目录)中运行 npm link 命令。如果我们要开发一个包,利用这种方法可以非常方便地在不同的工程间进行测试。
#####包的发布
npm 可以非常方便地发布一个包,比 pip、gem、pear 要简单得多。在发布之前,首先需要让我们的包符合 npm 的规范,npm 有一套以 CommonJS 为基础包规范,但与 CommonJS并不完全一致,其主要差别在于必填字段的不同。通过使用 npm init 可以根据交互式问答产生一个符合标准的 package.json,例如创建一个名为 byvoidmodule 的目录,然后在这个目录中运行 npm init:
这样就在 byvoidmodule 目录中生成一个符合 npm 规范的 package.json 文件。创建一个index.js 作为包的接口,一个简单的包就制作完成了。
在发布前,我们还需要获得一个账号用于今后维护自己的包,使用 npm adduser 根据提示输入用户名、密码、邮箱,等待账号创建完成。完成后可以使用 npm whoami 测验是否已经取得了账号。
接下来,在 package.json 所在目录下运行 npm publish ,稍等片刻就可以完成发布了。打开浏览器,访问 http://search.npmjs.org/ 就可以找到自己刚刚发布的包了。现在我们可以在世界的任意一台计算机上使用 npm install byvoidmodule 命令来安装它。图3-6 是npmjs.org 上包的描述页面。
如果你的包将来有更新,只需要在 package.json 文件中修改 version 字段,然后重新使用 npm publish 命令就行了。如果你对已发布的包不满意(比如我们发布的这个毫无意    义的包),可以使用 npm unpublish 命令来取消发布。

##四、调试

###命令行调试

Node.js 支持命令行下的单步调试。在命令行下执行 `node debug debug.js `,将会启动调试工具.我们可以用一些基本的命令进行单步跟踪调试,参见表3-3。
![biao3-3](http://ww3.sinaimg.cn/large/7dfb3940jw1el6gssm46pj20my0hpgnj.jpg)
###远程调试
V8 提供的调试功能是基于 TCP 协议的,因此 Node.js 可以轻松地实现远程调试。在命令行下使用以下两个语句之一可以打开调试服务器:
```
node --debug [= port ] script.js
node --debug-brk [= port ] script.js
```
node --debug 命令选项可以启动调试服务器,默认情况下调试端口是 5858,也可以使用 --debug=1234 指定调试端口为 1234。使用 --debug 选项运行脚本时,脚本会正常执行,但不会暂停,在执行过程中调试客户端可以连接到调试服务器。如果要求脚本暂停执行等待客户端连接,则应该使用 --debug-brk 选项。这时调试服务器在启动后会立刻暂停执行脚本,等待调试客户端连接。
当调试服务器启动以后,可以用命令行调试工具作为调试客户端连接,例如:
```
//在一个终端中
$ node --debug-brk debug.js
debugger listening on port 5858
//在另一个终端中
$ node debug 127.0.0.1:5858
```
事实上,当使用 node debug debug.js 命令调试时,只不过是用 Node.js 命令行工具将以上两步工作自动完成而已。

###使用 Eclipse 调试 Node.js

####配置调试环境

启动 Eclipse,选择菜单栏中 Help→Install New Software...,此时会打开一个安装对话框,点击右边的按钮Add...,接下来会打开一个标题为Add Repository的对话框,在 Location 中输入 http://chromedevtools.googlecode.com/svn/update/dev/, Name 中输入 Chrome Developer,然后点击OK按钮。
然后在Work with后面的组合框中选择刚刚添加的Chrome Developer,等待片刻,在列表中选中Google Chrome Developer Tools,然后点击Next.这时 Eclipse 会计算出所需安装的包和依赖,点击Next.阅读 License,选取I accept the terms of the license agreements,点击Next.接下来 Eclipse 会开始安装,稍等片刻.安装完成以后 Eclipse 会提示重新启动以应用更新,点击Restart Now,V8 调试工具就安装完成了.

####使用 Eclipse 调试 Node.js 程序

用 Eclipse 打开一个 Node.js 代码,选择Debug perspective进入调试视图,如图3-15所示。点击工具栏中 Debug 图标右边的向下三角形,选择Debug Configurations...(参见图3-16)。在配置窗口的左侧找到Standalone V8 VM,点击左上角的New图标,会产生一个新的配置。在配置中填写好Name,如NodeDebug,以及Host和Port。点击Apply应用配置.
接下来,通过 node --debug-brk=5858 debug.js 命令启动要调试脚本的调试服务器,然后在 Eclipse 的工具栏中点击调试按钮,即可启动调试.
接下来你就可以随心所欲地使用 Eclipse 这个强大的 IDE 来调试 Node.js 脚本了。如果你对 Eclipse 比较熟悉,你会惊喜地发现 Eclipse 的所有单步调试、断点、监视功能均可以非常方便地使用。

###使用 node-inspector 调试 Node.js

大部分基于 Node.js 的应用都是运行在浏览器中的,例如强大的调试工具 node-inspector。node-inspector 是一个完全基于 Node.js 的开源在线调试工具,提供了强大的调试功能和友好的用户界面,它的使用方法十分简便。
首先,使用 npm install -g node-inspector 命令安装 node-inspector,然后在终端中通过 node --debug-brk=5858 debug.js 命令连接你要除错的脚本的调试服务器,启动 node-inspector:
```
$ node-inspector
```
在浏览器中打开 http://127.0.0.1:8080/debug?port=5858 , 即可显示出优雅的 Web 调试工具.
node-inspector 的使用方法十分简单,和浏览器脚本调试工具一样,支持单步、断点、调用栈帧查看等功能。无论你以前有没有使用过调试工具,都可以在几分钟以内轻松掌握。
node-inspector 使用了 WebKit Web Inspector,因此只能在 Chrome、 Safari等 WebKit 内核的浏览器中使用,而不支持 Firefox 或 Internet Explorer