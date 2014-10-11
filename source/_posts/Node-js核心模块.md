title: Node.js核心模块
date: 2014-10-11 08:33:19
tags: NodeJs
categories: NodeJs
---
##全局对象
JavaScript 中有一个特殊的对象,称为全局对象(Global Object),它及其所有属性都可以在程序的任何地方访问,即全局变量。在浏览器 JavaScript 中,通常 window 是全局对象,而 Node.js 中的全局对象是 global ,所有全局变量(除了 global 本身以外)都是 global对象的属性。
我们在 Node.js 中能够直接访问到对象通常都是 global 的属性,如 console 、 process等,下面逐一介绍。

###全局对象与全局变量

global 最根本的作用是作为全局变量的宿主。按照 ECMAScript 的定义,满足以下条件的变量是全局变量:

* 在最外层定义的变量;
* 全局对象的属性;
* 隐式定义的变量(未定义直接赋值的变量)。

当你定义一个全局变量时,这个变量同时也会成为全局对象的属性,反之亦然。需要注意的是,在 Node.js 中你不可能在最外层定义变量,因为所有用户代码都是属于当前模块的,而模块本身不是最外层上下文。
永远使用 var 定义变量以避免引入全局变量,因为全局变量会污染命名空间,提高代码的耦合风险。

###process

process 是一个全局变量,即 global 对象的属性。它用于描述当前 Node.js 进程状态的对象,提供了一个与操作系统的简单接口。通常在你写本地命令行程序的时候,少不了要和它打交道。下面将会介绍process 对象的一些最常用的成员方法。

* process.argv 是命令行参数数组,第一个元素是 node, 第二个元素是脚本文件名,从第三个元素开始每个元素是一个运行参数。`console.log(process.argv);`
* process.stdout 是标准输出流,通常我们使用的 console.log() 向标准输出打印字符,而 process.stdout.write() 函数提供了更底层的接口。
* process.stdin 是标准输入流,初始时它是被暂停的,要想从标准输入读取数据,你必须恢复流,并手动编写流的事件响应函数。
```
process.stdin.resume();
	process.stdin.on('data', function(data) {
	process.stdout.write('read from console: ' + data.toString());
});
```
* process.nextTick(callback) 的功能是为事件循环设置一项任务,Node.js 会在下次事件循环调响应时调用 callback 。
初学者很可能不理解这个函数的作用,有什么任务不能在当下执行完,需要交给下次事件循环响应来做呢?我们讨论过, Node.js 适合 I/O 密集型的应用,而不是计算密集型的应用,因为一个 Node.js 进程只有一个线程,因此在任何时刻都只有一个事件在执行。如果这个事件占用大量的 CPU 时间,执行事件循环中的下一个事件就需要等待很久,因此 Node.js 的一个编程原则就是尽量缩短每个事件的执行时间。 process.nextTick() 提供了一个这样的工具,可以把复杂的工作拆散,变成一个个较小的事件。
```
function doSomething(args, callback) {
	somethingComplicated(args);
	callback();
}
doSomething(function onEnd() {
	compute();
});
```
我们假设 compute() 和 somethingComplicated() 是两个较为耗时的函数,以上的程序在调用 doSomething() 时会先执行 somethingComplicated() ,然后立即调用回调函数,在 onEnd() 中又会执行 compute() 。下面用 process.nextTick() 改写上面的程序:
```
function doSomething(args, callback) {
	somethingComplicated(args);
	process.nextTick(callback);
}
doSomething(function onEnd() {
	compute();
});
```
改写后的程序会把上面耗时的操作拆分为两个事件,减少每个事件的执行时间,提高事件响应速度。不要使用 setTimeout(fn,0) 代替 process.nextTick(callback) ,前者比后者效率要低得多。我们探讨了 process 对象常用的几个成员,除此之外 process 还展示了 process.platform 、process.pid 、 process.execPath 、 process.memoryUsage() 等方法,以及 POSIX进程信号响应机制。有兴趣的读者可以访问 http://nodejs.org/api/process.html 了解详细内容。

###console

console 用于提供控制台标准输出,它是由 Internet Explorer 的 JScript 引擎提供的调试工具,后来逐渐成为浏览器的事实标准。Node.js 沿用了这个标准,提供与习惯行为一致的console 对象,用于向标准输出流( stdout )或标准错误流( stderr )输出字符。

* console.log() :向标准输出流打印字符并以换行符结束。 console.log 接受若干个参数,如果只有一个参数,则输出这个参数的字符串形式。如果有多个参数,则以类似于 C 语言 printf() 命令的格式输出。第一个参数是一个字符串,如果没有参数,只打印一个换行。
* console.error() :与 console.log() 用法相同,只是向标准错误流输出。
* console.trace() :向标准错误流输出当前的调用栈。

##常用工具 util
util 是一个 Node.js 核心模块,提供常用函数的集合,用于弥补核心 JavaScript 的功能过于精简的不足。

###util.inherits

util.inherits(constructor, superConstructor) 是一个实现对象间原型继承的函数。JavaScript 的面向对象特性是基于原型的,与常见的基于类的不同。JavaScript 没有提供对象继承的语言级别特性,而是通过原型复制来实现的,在这里我们只介绍 util.inherits 的用法,示例如下:
``` javascript
var util = require('util');
function Base() {
	this.name = 'base';
	this.base = 1991;
	this.sayHello = function() {
		console.log('Hello ' + this.name);
	};
}
Base.prototype.showName = function() {
	console.log(this.name);
};
function Sub() {
	this.name = 'sub';
}
util.inherits(Sub, Base);
var objBase = new Base();
objBase.showName();
objBase.sayHello();
console.log(objBase);

var objSub = new Sub();
objSub.showName();
//objSub.sayHello();
console.log(objSub);
```
我们定义了一个基础对象 Base 和一个继承自 Base 的 Sub , Base 有三个在构造函数内定义的属性和一个原型中定义的函数,通过 util.inherits 实现继承。
注意, Sub 仅仅继承了 Base 在原型中定义的函数,而构造函数内部创造的 base 属性和 sayHello 函数都没有被 Sub 继承。同时,在原型中定义的属性不会被 console.log 作为对象的属性输出。

###util.inspect

util.inspect(object,[showHidden],[depth],[colors]) 是一个将任意对象转换为字符串的方法,通常用于调试和错误输出。它至少接受一个参数 object ,即要转换的对象。
showHidden 是一个可选参数,如果值为 true ,将会输出更多隐藏信息。
depth 表示最大递归的层数,如果对象很复杂,你可以指定层数以控制输出信息的多少。如果不指定 depth ,默认会递归2层,指定为 null 表示将不限递归层数完整遍历对象。
如果 color 值为 true ,输出格式将会以 ANSI 颜色编码,通常用于在终端显示更漂亮的效果。
特别要指出的是, util.inspect 并不会简单地直接把对象转换为字符串,即使该对象定义了 toString 方法也不会调用。

除了以上我们介绍的几个函数之外, util 还提供了 util.isArray() 、 util.isRegExp() 、util.isDate() 、 util.isError() 四个类型测试工具,以及 util.format() 、 util.debug()等工具。有兴趣的读者可以访问 http://nodejs.org/api/util.html 了解详细内容。

##事件驱动 events

events 是 Node.js 最重要的模块,没有“之一”,原因是 Node.js 本身架构就是事件式的,而它提供了唯一的接口,所以堪称 Node.js 事件编程的基石。 events 模块不仅用于用户代码与 Node.js 下层事件循环的交互,还几乎被所有的模块依赖。

###事件发射器

events 模块只提供了一个对象: events.EventEmitter 。 EventEmitter 的核心就是事件发射与事件监听器功能的封装。 EventEmitter 的每个事件由一个事件名和若干个参数组成,事件名是一个字符串,通常表达一定的语义。对于每个事件, EventEmitter 支持若干个事件监听器。当事件发射时,注册到这个事件的事件监听器被依次调用,事件参数作为回调函数参数传递。
让我们以下面的例子解释这个过程:
```
var events = require('events');
var emitter = new events.EventEmitter();
emitter.on('someEvent', function(arg1, arg2) {
	console.log('listener1', arg1, arg2);
});
emitter.on('someEvent', function(arg1, arg2) {
	console.log('listener2', arg1, arg2);
});
emitter.emit('someEvent', 'byvoid', 1991);
```
运行的结果是:
listener1 byvoid 1991
listener2 byvoid 1991
运行结果中可以看到两个事件监听器回调函数被先后调用.
这就是 EventEmitter 最简单的用法。接下来我们介绍一下 EventEmitter 常用的API。

* EventEmitter.on(event, listener) 为指定事件注册一个监听器,接受一个字符串 event 和一个回调函数 listener 。
* EventEmitter.emit(event, [arg1], [arg2], [...]) 发射 event 事件,传递若干可选参数到事件监听器的参数表。
* EventEmitter.once(event, listener) 为指定事件注册一个单次监听器,即监听器最多只会触发一次,触发后立刻解除该监听器。
* EventEmitter.removeListener(event, listener) 移除指定事件的某个监听器, listener 必须是该事件已经注册过的监听器。
* EventEmitter.removeAllListeners([event]) 移除所有事件的所有监听器,如果指定 event ,则移除指定事件的所有监听器。
更详细的 API 文档参见 http://nodejs.org/api/events.html。

###error 事件

EventEmitter 定义了一个特殊的事件 error ,它包含了“错误”的语义,我们在遇到异常的时候通常会发射 error 事件。当 error 被发射时, EventEmitter 规定如果没有响应的监听器, Node.js 会把它当作异常,退出程序并打印调用栈。我们一般要为会发射 error事件的对象设置监听器,避免遇到错误后整个程序崩溃。例如:
```javascript
var events = require('events');
var emitter = new events.EventEmitter();
emitter.emit('error');
```

###继承 EventEmitter

大多数时候我们不会直接使用 EventEmitter ,而是在对象中继承它。包括 fs 、 net 、http 在内的,只要是支持事件响应的核心模块都是 EventEmitter 的子类。
为什么要这样做呢?原因有两点。首先,具有某个实体功能的对象实现事件符合语义,事件的监听和发射应该是一个对象的方法。其次 JavaScript 的对象机制是基于原型的,支持部分多重继承,继承 EventEmitter 不会打乱对象原有的继承关系。

##文件系统 fs

fs 模块是文件操作的封装,它提供了文件的读取、写入、更名、删除、遍历目录、链接等 POSIX 文件系统操作。与其他模块不同的是, fs 模块中所有的操作都提供了异步的和同 步 的 两 个 版 本 , 例 如 读 取 文 件 内 容 的 函 数 有 异 步 的 fs.readFile() 和 同 步 的fs.readFileSync() 。我们以几个函数为代表,介绍 fs 常用的功能,并列出 fs 所有函数的定义和功能。

###fs.readFile

fs.readFile(filename,[encoding],[callback(err,data)]) 是最简单的读取文件的函数。它接受一个必选参数 filename ,表示要读取的文件名。第二个参数 encoding是可选的,表示文件的字符编码。 callback 是回调函数,用于接收文件的内容。如果不指定 encoding ,则 callback 就是第二个参数。回调函数提供两个参数 err 和 data , err 表示有没有错误发生, data 是文件内容。如果指定了 encoding , data 是一个解析后的字符串,否则 data 将会是以 Buffer 形式表示的二进制数据。
```javascript
var fs = require('fs');
//fs.readFile('content.txt', 'utf-8', function(err, data) {
fs.readFile('content.txt', function(err, data) {
	if (err) {
		console.error(err);
	} else {
		console.log(data);
	}
});
```
Node.js 的异步编程接口习惯是以函数的最后一个参数为回调函数,通常一个函数只有一个回调函数。回调函数是实际参数中第一个是 err ,其余的参数是其他返回的内容。如果没有发生错误, err 的值会是 null 或undefined 。如果有错误发生, err 通常是 Error 对象的实例。

###fs.readFileSync

fs.readFileSync(filename, [encoding]) 是 fs.readFile 同步的版本。它接受的参数和 fs.readFile 相同,而读取到的文件内容会以函数返回值的形式返回。如果有错误发生, fs 将会抛出异常,你需要使用 try 和 catch 捕捉并处理异常。与同步 I/O 函数不同,Node.js 中异步函数大多没有返回值。

###fs.open

fs.open(path, flags, [mode], [callback(err, fd)]) 是 POSIX open 函数的封装,与 C 语言标准库中的 fopen 函数类似。它接受两个必选参数, path 为文件的路径,flags 可以是以下值。

* r :以读取模式打开文件。
* r+ :以读写模式打开文件。
* w :以写入模式打开文件,如果文件不存在则创建。
* w+ :以读写模式打开文件,如果文件不存在则创建。
* a :以追加模式打开文件,如果文件不存在则创建。
* a+ :以读取追加模式打开文件,如果文件不存在则创建

mode 参数用于创建文件时给文件指定权限,默认是 0666。回调函数将会传递一个文件描述符fd 。

###fs.read

fs.read(fd, buffer, offset, length, position, [callback(err, bytesRead,buffer)]) 是 POSIX read 函数的封装,相比 fs.readFile 提供了更底层的接口。 fs.read的功能是从指定的文件描述符 fd 中读取数据并写入 buffer 指向的缓冲区对象。 offset 是buffer 的写入偏移量。 length 是要从文件中读取的字节数。 position 是文件读取的起始位置,如果 position 的值为 null ,则会从当前文件指针的位置读取。回调函数传递bytesRead 和 buffer ,分别表示读取的字节数和缓冲区对象。
以下是一个使用 fs.open 和 fs.read 的示例。
```javascript
var fs = require('fs');
fs.open('content.txt', 'r', function(err, fd) {
	if (err) {
		console.error(err);
		return;
	}
	var buf = new Buffer(8);
	fs.read(fd, buf, 0, 8, null, function(err, bytesRead, buffer) {
	if (err) {
		console.error(err);
		return;
	}
	console.log('bytesRead: ' + bytesRead);
		console.log(buffer);
	})
});
```
一般来说,除非必要,否则不要使用这种方式读取文件,因为它要求你手动管理缓冲区和文件指针,尤其是在你不知道文件大小的时候,这将会是一件很麻烦的事情。
列出了 fs 所有函数的定义和功能:
***

| 功能     | 异步函数 | 同步函数 |
| ------------- |:-------------:| -----:|
| 打开文件  | fs.open(path,flags, [mode], [callback(err,fd)]) | fs.openSync(path, flags, [mode]) |
| 关闭文件 | fs.close(fd, [callback(err)]) | fs.closeSync(fd) |
| 读取文件(文件描述符) | fs.read(fd,buffer,offset,length,position,[callback(err, bytesRead, buffer)]) | fs.readSync(fd, buffer, offset,length, position) |
| 写入文件(文件描述符) | fs.write(fd,buffer,offset,length,position,[callback(err, bytesWritten, buffer)]) | fs.writeSync(fd, buffer, offset,length, position) |
| 读取文件内容 | fs.readFile(filename,[encoding],[callback(err, data)]) | fs.readFileSync(filename,[encoding]) |
| 写入文件内容 | fs.writeFile(filename,data,[encoding],[callback(err)]) | fs.writeFileSync(filename, data,[encoding]) |
| 删除文件 | fs.unlink(path, [callback(err)]) | fs.unlinkSync(path) |
| 创建目录 | fs.mkdir(path, [mode], [callback(err)]) | fs.mkdirSync(path, [mode]) |
| 删除目录 | fs.rmdir(path, [callback(err)]) | fs.rmdirSync(path) |
| 读取目录 | fs.readdir(path, [callback(err, files)]) | fs.readdirSync(path) |
| 获取真实路径 | fs.realpath(path, [callback(err,resolvedPath)]) | fs.realpathSync(path) |
| 更名 | fs.rename(path1, path2, [callback(err)]) | fs.renameSync(path1, path2) |
| 截断 | fs.truncate(fd, len, [callback(err)]) | fs.truncateSync(fd, len) |
| 更改所有权 | fs.chown(path, uid, gid, [callback(err)]) | fs.chownSync(path, uid, gid) |
| 更改所有权(文件描述符) | fs.fchown(fd, uid, gid, [callback(err)]) | fs.fchownSync(fd, uid, gid) |
| 更改所有权(不解析符号链接) | fs.lchown(path, uid, gid, [callback(err)]) | fs.lchownSync(path, uid, gid) |
| 更改权限 | fs.chmod(path, mode, [callback(err)]) | fs.chmodSync(path, mode) |
| 更改权限(文件描述符) | fs.fchmod(fd, mode, [callback(err)]) |fs.fchmodSync(fd, mode) |
| 更改权限(不解析符号链接) | fs.lchmod(path, mode, [callback(err)]) | fs.lchmodSync(path, mode) |
| 获取文件信息 | fs.stat(path, [callback(err, stats)]) | fs.statSync(path) |
| 获取文件信息(文件描述符) | fs.fstat(fd, [callback(err, stats)]) | fs.fstatSync(fd) |
| 获取文件信息(不解析符号链接) | fs.lstat(path, [callback(err, stats)]) | fs.lstatSync(path) |
| 创建硬链接 | fs.link(srcpath, dstpath, [callback(err)]) | fs.linkSync(srcpath, dstpath) |
| 创建符号链接 | fs.symlink(linkdata, path, [type],[callback(err)]) | fs.symlinkSync(linkdata, path,[type]) |
| 读取链接 | fs.readlink(path, [callback(err,linkString)]) | fs.readlinkSync(path) |
| 修改文件时间戳 | fs.utimes(path, atime, mtime, [callback(err)]) | fs.utimesSync(path, atime, mtime) |
| 修改 文件时间戳(文件描述符) | fs.futimes(fd, atime, mtime, [callback(err)]) | fs.futimesSync(fd, atime, mtime) |
| 同步磁盘缓存 | fs.fsync(fd, [callback(err)]) | fs.fsyncSync(fd) |

##HTTP 服务器与客户端

Node.js 标准库提供了 http 模块,其中封装了一个高效的 HTTP 服务器和一个简易的HTTP 客户端。 http.Server 是一个基于事件的 HTTP 服务器,它的核心由 Node.js 下层 C++部分实现,而接口由 JavaScript 封装,兼顾了高性能与简易性。 http.request 则是一个HTTP 客户端工具,用于向 HTTP 服务器发起请求,例如实现 Pingback 1 或者内容抓取。

###HTTP 服务器

http.Server 是 http 模块中的 HTTP 服务器对象,用 Node.js 做的所有基于 HTTP 协议的系统,如网站、社交应用甚至代理服务器,都是基于 http.Server 实现的。它提供了一套封装级别很低的 API,仅仅是流控制和简单的消息解析,所有的高层功能都要通过它的接口来实现。

####http.Server 的事件

http.Server 是一个基于事件的 HTTP 服务器,所有的请求都被封装为独立的事件,开发者只需要对它的事件编写响应函数即可实现 HTTP 服务器的所有功能。它继承自EventEmitter ,提供了以下几个事件。

* request :当客户端请求到来时,该事件被触发,提供两个参数 req 和 res ,分别是http.ServerRequest 和 http.ServerResponse 的实例,表示请求和响应信息。
* connection :当 TCP 连接建立时,该事件被触发,提供一个参数 socket ,为net.Socket 的实例。 connection 事件的粒度要大于 request ,因为客户端在Keep-Alive 模式下可能会在同一个连接内发送多次请求。
* close :当服务器关闭时,该事件被触发。注意不是在用户连接断开时。
除此之外还有 checkContinue 、 upgrade 、 clientError 事件,通常我们不需要关心,只有在实现复杂的 HTTP 服务器的时候才会用到。
在 这 些 事 件 中 , 最 常 用 的 就 是 request 了 , 因 此 http 提 供 了 一 个 捷 径 :http.createServer([requestListener]) , 功 能 是 创 建 一 个 HTTP 服 务 器 并 将requestListener 作为 request 事件的监听函数,这也是我们前面例子中使用的方法。事实上它显式的实现方法是:
```node
var http = require('http');
var server = new http.Server();
server.on('request', function(req, res) {
	res.writeHead(200, {'Content-Type': 'text/html'});
	res.write('<h1>Node.js</h1>');
	res.end('<p>Hello World</p>');
});
server.listen(3000);
console.log("HTTP server is listening at port 3000.");
```

####http.ServerRequest

http.ServerRequest 是 HTTP 请求的信息,是后端开发者最关注的内容。它一般由http.Server 的 request 事件发送,作为第一个参数传递,通常简称 request 或 req 。ServerRequest 提供一些属性,表 4-2 中列出了这些属性。
HTTP 请求一般可以分为两部分:请求头(Request Header)和请求体(Requset Body)。以上内容由于长度较短都可以在请求头解析完成后立即读取。而请求体可能相对较长,需要一定的时间传输,因此 http.ServerRequest 提供了以下3个事件用于控制请求体传输。

* data :当请求体数据到来时,该事件被触发。该事件提供一个参数 chunk ,表示接收到的数据。如果该事件没有被监听,那么请求体将会被抛弃。该事件可能会被调用多次。
* end :当请求体数据传输完成时,该事件被触发,此后将不会再有数据到来。
* close : 用户当前请求结束时,该事件被触发。不同于 end ,如果用户强制终止了传输,也还是调用 close 。

ServerRequest 的属性

|名称     |    含义 |
| ------------- |:-------------:|
|complete|客户端请求是否已经发送完成|
|httpVersion|HTTP 协议版本,通常是 1.0 或 1.1|
|method |HTTP 请求方法,如 GET、POST、PUT、DELETE 等|
|url | 原始的请求路径,例如 /static/image/x.jpg 或 /user?name=byvoid|
|headers | HTTP 请求头 |
|trailers | HTTP 请求尾(不常见)|
|connection | 当前 HTTP 连接套接字,为 net.Socket 的实例|
|socket | connection 属性的别名|
|client | client 属性的别名|

####获取 GET 请求内容

注意, http.ServerRequest 提供的属性中没有类似于 PHP 语言中的 $_GET 或$_POST 的属性,那我们如何接受客户端的表单请求呢?由于 GET 请求直接被嵌入在路径中, URL是完整的请求路径,包括了 ? 后面的部分,因此你可以手动解析后面的内容作为 GET请求的参数。Node.js 的 url 模块中的 parse 函数提供了这个功能,例如:
``` node
var http = require('http');
var url = require('url');
var util = require('util');
http.createServer(function(req, res) {
	res.writeHead(200, {'Content-Type': 'text/plain'});
	res.end(util.inspect(url.parse(req.url, true)));
}).listen(3000);
```
在浏览器中访问 http://127.0.0.1:3000/user?name=byvoid&email=byvoid@byvoid.com, 我们可以看到浏览器返回的结果.
通过 url.parse ,原始的 path 被解析为一个对象,其中 query 就是我们所谓的 GET请求的内容,而路径则是 pathname 。

####获取 POST 请求内容

HTTP 协议 1.1 版本提供了8种标准的请求方法,其中最常见的就是 GET 和 POST。相比GET 请 求 把 所 有 的 内 容 编 码 到 访 问 路 径 中 , POST 请 求 的 内 容 全 部 都 在 请 求 体 中 。http.ServerRequest 并没有一个属性内容为请求体,原因是等待请求体传输可能是一件耗时的工作,譬如上传文件。而很多时候我们可能并不需要理会请求体的内容,恶意的 POST请求会大大消耗服务器的资源。所以 Node.js 默认是不会解析请求体的,当你需要的时候,需要手动来做。让我们看看实现方法:
```node
var http = require('http');
var querystring = require('querystring');
var util = require('util');
http.createServer(function(req, res) {
	var post = '';
	req.on('data', function(chunk) {
		post += chunk;
	});
	req.on('end', function() {
		post = querystring.parse(post);
		res.end(util.inspect(post));
	});
}).listen(3000);
```
上面代码并没有在请求响应函数中向客户端返回信息,而是定义了一个 post 变量,用于在闭包中暂存请求体的信息。通过 req 的 data 事件监听函数,每当接受到请求体的数据,就累加到 post 变量中。在 end 事件触发后,通过 querystring.parse 将 post 解析为真正的 POST 请求格式,然后向客户端返回。
不要在真正的生产应用中使用上面这种简单的方法来获取 POST 请求,因为它有严重的效率问题和安全问题,这只是一个帮助你理解的示例。

####http.ServerResponse

http.ServerResponse 是返回给客户端的信息,决定了用户最终能看到的结果。它也是由 http.Server 的 request 事件发送的,作为第二个参数传递,一般简称为response 或 res 。http.ServerResponse 有三个重要的成员函数,用于返回响应头、响应内容以及结束请求。

* response.writeHead(statusCode, [headers]) :向请求的客户端发送响应头。statusCode 是 HTTP 状态码,如 200 (请求成功)、404 (未找到)等。 headers是一个类似关联数组的对象,表示响应头的每个属性。该函数在一个请求内最多只能调用一次,如果不调用,则会自动生成一个响应头。
* response.write(data, [encoding]) :向请求的客户端发送响应内容。 data 是一个 Buffer 或字符串,表示要发送的内容。如果 data 是字符串,那么需要指定encoding 来说明它的编码方式,默认是 utf-8 。在 response.end 调用之前,response.write 可以被多次调用。
* response.end([data], [encoding]) :结束响应,告知客户端所有发送已经完成。当所有要返回的内容发送完毕的时候,该函数 必须 被调用一次。它接受两个可选参数,意义和 response.write 相同。如果不调用该函数,客户端将永远处于等待状态。

###HTTP 客户端

http 模块提供了两个函数 http.request 和 http.get ,功能是作为客户端向 HTTP服务器发起请求。

* http.request(options, callback) 发起 HTTP 请求。接受两个参数, option 是一个类似关联数组的对象,表示请求的参数, callback 是请求的回调函数。 option常用的参数如下所示。
	* host :请求网站的域名或 IP 地址。
	* port :请求网站的端口,默认 80。
	* method :请求方法,默认是 GET。
	* path :请求的相对于根的路径,默认是“ / ”。 QueryString 应该包含在其中。例如 /search?query=byvoid 。
	* headers :一个关联数组对象,为请求头的内容。
callback 传递一个参数,为 http.ClientResponse 的实例。http.request 返回一个 http.ClientRequest 的实例。下面是一个通过 http.request 发送 POST 请求的代码:
```node
var http = require('http');
var querystring = require('querystring');
var contents = querystring.stringify({
	name: 'byvoid',
	email: 'byvoid@byvoid.com',
	address: 'Zijing 2#, Tsinghua University',
});
var options = {
	host: 'www.byvoid.com',
	path: '/application/node/post.php',
	method: 'POST',
	headers: {
		'Content-Type': 'application/x-www-form-urlencoded',
		'Content-Length' : contents.length
	}
};
var req = http.request(options, function(res) {
	res.setEncoding('utf8');
	res.on('data', function (data) {
		console.log(data);
	});
});
req.write(contents);
req.end();
```

* http.get(options, callback) http 模块还提供了一个更加简便的方法用于处理GET请求: http.get 。它是 http.request 的简化版,唯一的区别在于 http.get自动将请求方法设为了 GET 请求,同时不需要手动调用 req.end() 。
```node
var http = require('http');
http.get({host: 'www.byvoid.com'}, function(res) {
	res.setEncoding('utf8');
	res.on('data', function (data) {
		console.log(data);
	});
});
```

####http.ClientRequest

http.ClientRequest 是由 http.request 或 http.get 返回产生的对象,表示一个已经产生而且正在进行中的 HTTP 请求。它提供一个 response 事件,即 http.request或 http.get 第二个参数指定的回调函数的绑定对象。我们也可以显式地绑定这个事件的监听函数:
```node
var http = require('http');
var req = http.get({host: 'www.byvoid.com'});
req.on('response', function(res) {
	res.setEncoding('utf8');
	res.on('data', function (data) {
		console.log(data);
	});
});
```
http.ClientRequest 像 http.ServerResponse 一样也提供了 write 和 end 函数,用于向服务器发送请求体,通常用于 POST、 PUT 等操作。所有写结束以后必须调用 end函数以通知服务器,否则请求无效。 http.ClientRequest 还提供了以下函数。

* request.abort() :终止正在发送的请求。
* request.setTimeout(timeout, [callback]) :设置请求超时时间, timeout 为毫秒数。当请求超时以后, callback 将会被调用。
此外还有 request.setNoDelay([noDelay]) 、 request.setSocketKeepAlive([enable] , [initialDelay]) 等函数,具体内容请参见 Node.js 文档。

####http.ClientResponse

http.ClientResponse 与 http.ServerRequest 相似,提供了三个事件 data 、 end和 close ,分别在数据到达、传输结束和连接结束时触发,其中 data 事件传递一个参数chunk ,表示接收到的数据。
http.ClientResponse 也提供了一些属性,用于表示请求的结果状态:

|名称 | 含义 |
|---------|:----------:|
|statusCode | HTTP 状态码,如 200、404、500|
|httpVersion | HTTP 协议版本,通常是 1.0 或 1.1| 
|headers | HTTP 请求头  |
|trailers | HTTP 请求尾(不常见)|

http.ClientResponse 还提供了以下几个特殊的函数。

* response.setEncoding([encoding]) :设置默认的编码,当 data 事件被触发时,数据将会以 encoding 编码。默认值是 null ,即不编码,以 Buffer 的形式存储。常用编码为 utf8。
* response.pause() :暂停接收数据和发送事件,方便实现下载功能。
* response.resume() :从暂停的状态中恢复。