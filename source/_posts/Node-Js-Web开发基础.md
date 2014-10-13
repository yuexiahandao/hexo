title: Node.Js Web开发基础
date: 2014-10-12 09:28:16
tags: NodeJs
categories: NodeJs
---
##web开发基础知识

最早实现动态网页的方法是使用Perl 和 CGI。在 Perl 程序中输出 HTML 内容,由 HTTP服务器调用 Perl 程序,将结果返回给客户端。但问题在于如果 HTML 内容比较多,维护非常不方便。大概在 2000 年左右,以 ASP、PHP、JSP 的为代表的以模板为基础的语言出现了,这种语言的使用方法与 CGI 相反,是在以 HTML 为主的模板中插入程序代码 2 。这种方式在2002年前后非常流行,但它的问题是页面和程序逻辑紧密耦合,任何一个网站规模变大以后,都会遇到结构混乱,难以处理的问题。为了解决这种问题,以 MVC 架构为基础的平台逐渐兴起,著名的 Ruby on Rails、Django、Zend Framework 都是基于 MVC 架构的。我们称 PHP、ASP、JSP 为“模板为中心的架构”.Web 开发架构对比:

|特性|模板为中心架构|MVC 架构|
|:-----------|:---------:|-----------:|
|页面产生方式| 执行并替换标签中的语句| 由模板引擎生成 HTML 页面|
|路径解析|对应到文件系统 |由控制器定义|
|数据访问|通过 SQL 语句查询或访问文件系统|对象关系模型|
|架构中心|脚本语言是静态 HTTP 服务器的扩展|静态 HTTP 服务器是脚本语言的补充|
|适用范围|小规模网站|大规模网站|
|学习难度|容易|较难|

Node.js 本质上和 Perl 或 C++ 一样,都可以作为 CGI 扩展被调用,但它还可以跳过 HTTP服务器,因为它本身就是。传统的架构中 HTTP 服务器的角色会由 Apache、Nginx、IIS 之类的软件来担任,而 Node.js 不需要 。Node.js 提供了 http 模块,它是由 C++ 实现的,性能可靠,可以直接应用到生产环境。
Node.js 和其他的语言相比的另一个显著区别,在于它的原始封装程度较低。例如 PHP 中你可以访问 $_REQUEST 获取客户端的 POST 或 GET 请求,通常不需要直接处理 HTTP 协议。这些语言要求由 HTTP 服务器来调用,因此你需要设置一个 HTTP 服务器来处理客户端的请求,HTTP 服务器通过 CGI 或其他方式调用脚本语言解释器,将运行的结果传递回HTTP 服务器,最终再把内容返回给客户端。而在 Node.js 中,很多工作需要你自己来做(并不是都要自己动手,因为有第三方框架的帮助)。
Node.js 虽然提供了 http 模块,却不是让你直接用这个模块进行 Web 开发的。http 模块仅仅是一个 HTTP 服务器内核的封装,你可以用它做任何 HTTP 服务器能做的事情,不仅仅是做一个网站,甚至实现一个 HTTP 代理服务器都行。你如果想用它直接开发网站,那么就必须手动实现所有的东西了,小到一个 POST 请求,大到 Cookie、会话的管理。当你用这种方式建成一个网站的时候,你就几乎已经做好了一个完整的框架了。

##Express 框架

http://expressjs.jser.us/

目前最稳定、使用最广泛,而且 Node.js 官方推荐的唯一一个 Web 开发框架。Express ( http://expressjs.com/ ) 除了为 http 模块提供了更高层的接口外,还实现了许多功能,其中包括:

* 路由控制;
* 模板解析支持;
* 动态视图;
* 用户会话;
* CSRF 保护;
* 静态文件服务;
* 错误控制器;
* 访问日志;
* 缓存;
* 插件支持。

需要指出的是,Express 不是一个无所不包的全能框架,像 Rails 或 Django 那样实现了模板引擎甚至 ORM (Object Relation Model,对象关系模型)。它只是一个轻量级的 Web 框架,多数功能只是对 HTTP 协议中常用操作的封装,更多的功能需要插件或者整合其他模块来完成。
```node
var express = require('express');
var app = express();
var bodyParser = require('body-parser');
app.use(bodyParser());
app.all('/', function(req, res) {
	console.log(req.query.title);
	console.log(req.query.text);
	//res.send(req.body.title + req.body.text);
	res.status(200).end(req.query.title + req.query.text);
});
app.listen(3000);
```

##Express快速开始

###安装 Express

为了使用这个工具,我们需要用全局模式安装Express,因为只有这样我们才能在命令行中使用它。运行以下命令:
```bash
$ npm install -g express
$ npm install -g express-generator//创建一个APP的功能分离出来为express-generator
```
等待数秒后安装完成,我们就可以在命令行下通过 express 命令快速创建一个项目了。在这之前先使用 express --help 查看帮助信息.Express 在初始化一个项目的时候需要指定模板引擎,认支持Jade和ejs.

###建立工程

通过以下命令建立网站基本结构:

```bash
express -e microblog
```
进入目录中运行 `npm install`,安装依赖的项目.它自动安装了依赖 ejs 和 express。这是为什么呢?检查目录中的 package.json 文件,文件中 dependencies 属性中有 express 和 ejs 。无参数的 `npm install` 的功能就是检查当前目录下的 package.json,并自动安装所有指定的依赖。
运行项目:`$ DEBUG=microblog ./bin/www` or `npm start`(package.json中的script属性)
注意node也分开发和产品模式。

###工程的结构

现在让我们回过头来看看 Express 都生成了哪些文件。除了 package.json,它只产生了两个 JavaScript 文件 app.js 和 routes/index.js。模板引擎 ejs 也有两个文件 index.ejs 和layout.ejs,此外还有样式表 style.css。

####app.js

app.js 是工程的入口,首先我们导入了 Express 模块,前面已经通过 npm 安装到了本地,在这里可以直接通过require 获取。 routes 是一个文件夹形式的本地模块,即 ./routes/index.js ,它的功能是为指定路径组织返回内容,相当于 MVC 架构中的控制器。通过 express()函数创建了一个应用的实例,后面的所有操作都是针对于这个实例进行的。
接下来是三个if,分别指定了通用、开发和产品环境下的参数。
app.set 是 Express 的参数设置工具,接受一个键(key)和一个值(value),可用的参数如下所示。

* basepath :基础地址,通常用于 res.redirect() 跳转。
* views :视图文件的目录,存放模板文件。
* view engine :视图模板引擎。
* view options :全局视图参数对象。
* view cache :启用视图缓存。
* case sensitive routes :路径区分大小写。
* strict routing :严格路径,启用后不会忽略路径末尾的“ / ”。
* jsonp callback :开启透明的 JSONP 支持。

Express 依赖于 connect,提供了大量的中间件,可以通过 app.use 启用.启用了5个中间件: bodyParser 、 methodOverride 、 router 、 static 以及 errorHandler 。bodyParser 的功能是解析客户端请求,通常是通过 POST 发送的内容。 methodOverride用于支持定制的 HTTP 方法.router 是项目的路由支持。 static 供了静态文件支持。errorHandler 是错误控制器。

app.get('/', routes.index); 是一个路由控制器,用户如果访问“ / ”路径,则由 routes.index 来控制。最后服务器通过 app.listen(3000); 启动,监听3000端口。

####routes/index.js

app.js 中通过 app.get('/', routes.index); 将“ / ”路径映射到 exports.index函数下。其中只有一个语句 res.render('index', { title: 'Express' }) ,功能是调用模板解析引擎,翻译名为 index 的模板,并传入一个对象作为参数,这个对象只有一个属性,即 title: 'Express'。

####index.ejs

index.ejs 是模板文件,即 routes/index.js 中调用的模板	.它的基础是 HTML 语言,其中包含了形如 <%= title %> 的标签,功能是显示引用的变量,即 res.render 函数第二个参数传入的对象的属性。

####layout.ejs

```html
<!DOCTYPE html>
<html>
	<head>
		<title><%= title %></title>
		<link rel='stylesheet' href='/stylesheets/style.css' />
	</head>
	<body>
		<%- body %>
	</body>
</html>
```
模板文件不是孤立展示的,默认情况下所有的模板都继承自 layout.ejs,即 <%- body %>部分才是独特的内容,其他部分是共有的,可以看作是页面框架。

##路由控制

###工作原理

有HTTP请求时，app 会解析请求的路径,调用相应的逻辑。app.js 中有一行内容是 app.get('/', routes.index) ,它的作用是规定路径为“ / ”的 GET 请求由 routes.index 函数处理。 routes.index 通过 res.render('index', { title: 'Express' }) 调用视图模板 index ,传递 title变量。最终视图模板生成 HTML 页面,返回给浏览器.
浏览器在接收到内容以后,经过分析发现要获取 /stylesheets/style.css,因此会再次向服务器发起请求。app.js 中并没有一个路由规则指派到 /stylesheets/style.css,但 app 通过app.use(express.static(__dirname + '/public')) 配置了静态文件服务器,因此/stylesheets/style.css 会定向到 app.js 所在目录的子目录中的文件 public/stylesheets/style.css.
![picture](http://ww2.sinaimg.cn/large/7dfb3940jw1el8cw7w3bsj20ep0db0t0.jpg)

###创建路由规则

打开 routes/index.js,在已有的路由规则 router.get('/index', routes.index) 后面添加:
```node
router.get('/hello', function(req, res) {
  res.render('hello', { title: 'Hello Express' });//can write your own string text,here use view/hello.ejs!
});
```

###路径匹配

上面的例子是为固定的路径设置路由规则,Express 还支持更高级的路径匹配模式。例如我们想要展示一个用户的个人页面,路径为 /user/[username],可以用下面的方法定义路由规则:
```node 
router.get('/user/:username', function(req, res) {
	res.send('user: ' + req.params.username);
});
```
![results](http://ww3.sinaimg.cn/large/7dfb3940jw1el8der3nfaj208002fwee.jpg)

路径规则 /user/:username 会被自动编译为正则表达式,类似于 \/user\/([^\/]+)\/?这样的形式。路径参数可以在响应函数中通过 req.params 的属性访问。路径规则同样支持 JavaScript 正则表达式,例如 router.get(\/user\/([^\/]+)\/?,callback) 。这样的好处在于可以定义更加复杂的路径规则,而不同之处是匹配的参数是匿名的,因此需要通过 req.params[0] 、 req.params[1] 这样的形式访问。

###REST 风格的路由规则

HTTP 协议定义了以下8种标准的方法。
* GET:请求获取指定资源。
* HEAD:请求指定资源的响应头。
* POST:向指定资源提交数据。
* PUT:请求服务器存储一个资源。
* DELETE:请求服务器删除指定资源。
* TRACE:回显服务器收到的请求,主要用于测试或诊断。
* CONNECT:HTTP/1.1 协议中预留给能够将连接改为管道方式的代理服务器。
* OPTIONS:返回服务器支持的HTTP请求方法。

其中我们经常用到的是 GET、POST、PUT 和 DELETE 方法。根据 REST 设计模式,这4种方法通常分别用于实现以下功能。

* GET:获取
* POST:新增
* PUT:更新
* DELETE:删除

REST风格HTTP 请求的特点:

|请求方式|安全|幂等|
|:---------:|:---------:|:---------:|
|GET|是|是|
|POST|否|否|
|PUT|否|是|
|DELETE|否|是|

所谓安全是指没有副作用,即请求不会对资源产生变动,连续访问多次所获得的结果不受访问者的影响。而幂等指的是重复请求多次与一次请求的效果是一样的,比如获取和更新操作是幂等的,这与新增不同。删除也是幂等的,即重复删除一个资源,和删除一次是一样的。

|请求方式|绑定函数|
|:----------:|:---------:|
|GET| app.get(path, callback)|
|POST| app.post(path, callback)|
|PUT |app.put(path, callback)|
|DELETE| app.delete(path, callback)|
|PATCH | app.patch(path, callback)|
|TRACE |app.trace(path, callback)|
|CONNECT| app.connect(path, callback)|
|OPTIONS |app.options(path, callback)|
|所有方法 |app.all(path, callback)|

需要注意的是 app.all 函数,它支持把所有的请求方式绑定到同一个响应函数,是一个非常灵活的函数,在后面我们可以看到许多功能都可以通过它来实现。

###控制权转移

Express 支持同一路径绑定多个路由响应函数.但当你访问任何被这两条同样的规则匹配到的路径时,会发现请求总是被前一条路由规则捕获,后面的规则会被忽略。原因是 Express 在处理路由规则时,会优先匹配先定义的路由规则,因此后面相同的规则被屏蔽.

Express 提供了路由控制权转移的方法,即回调函数的第三个参数 next ,通过调用next() ,会将路由控制权转移给后面的规则,例如:
```node
app.all('/user/:username', function(req, res, next) {
	console.log('all methods captured');
	next();//attention here
});
app.get('/user/:username', function(req, res) {
	res.send('user: ' + req.params.username);
});
```
**这是一个非常有用的工具,可以让我们轻易地实现中间件,而且还能提高代码的复用程度。**

## 模板引擎

模板引擎随着规模的扩大它会遇到许多问题,下面列举几个主要的:

* 页面功能逻辑与页面布局样式耦合,网站规模变大以后逐渐难以维护。
* 语法复杂,对于非技术的网页设计者来说门槛较高,难以学习。
* 功能过于全面,页面设计者可以在页面上编程,不利于功能划分,也使模板解析效率降低。

模板引擎的功能是将页面模板和要显示的数据结合起来生成 HTML 页面。它既可以运行在服务器端又可以运行在客户端,大多数时候它都在服务器端直接被解析为 HTML,解析完成后再传输给客户端,因此客户端甚至无法判断页面是否是模板引擎生成的。有时候模板引擎也可以运行在客户端,即浏览器中,典型的代表就是 XSLT,它以 XML 为输入,在客户端生成 HTML 页面。但是由于浏览器兼容性问题,XSLT 并不是很流行。目前的主流还是由服务器运行模板引擎。
模板引擎在 MVC 架构中的位置:
![pictrue](http://ww3.sinaimg.cn/large/7dfb3940jw1el8e2o6iowj20es0910t2.jpg)
基于 JavaScript 的模板引擎有许多种实现,我们推荐使用 ejs (Embedded JavaScript),因为它十分简单,而且与 Express 集成良好。由于它是标准 JavaScript 实现的,因此它不仅可以运行在服务器端,还可以运行在浏览器中。我们这一章的示例是在服务器端运行 ejs,这样减少了对浏览器的依赖,而且更符合传统架构的习惯。
我们在 app.js 中通过以下两个语句设置了模板引擎和页面模板的位置:
```node
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');
```
表明要使用的模板引擎是 ejs,页面模板在 views 子目录下。
```node
router.get('/hello', function(req, res) {
  res.render('hello', { title: 'Hello Express' });//调用模板引擎 
});
```
res.render 的功能是调用模板引擎,并将其产生的页面直接返回给客户端。它接受两个参数,第一个是模板的名称,即 views 目录下的模板文件名,不包含文件的扩展名;第二个参数是传递给模板的数据,用于模板翻译。
我们可以用它们实现页面模板系统能实现的任何内容。ejs 的标签系统非常简单,它只有以下3种标签。

* <% code %> :JavaScript 代码。
* <%= code %> :显示替换过 HTML 特殊字符的内容。
* <%- code %> :显示原始 HTML 内容。

###页面布局

Express4 use layout(default didn't use this).

1. 安装express-partials。运行cmd用npm install express-partials,或者在package.json里面的dependencies添加"express-partials": "*"。然后运行cmd并切换至项目目录运行npm install获得最新版。
2. app.js里面引用express-partials。
步骤一：添加引用var partials = require('express-partials');
步骤二：在app.set('view engine', 'ejs');下面添加app.use(partials());
3. 在需要引用模板的地方调用layout:'模版名称'
```node
app.get('/路径', function (req, res) {
	res.render('.ejs文件的名字', {title: '定义标题',layout:'模版名称'});
	//res.render('.ejs文件的名字', {title: '定义标题'});
});
```
按照以上方案执行后，确实可以正常引用layout模板了，在Express3.x中，新建一个layout.ejs后，通过<%-body %>来引用其它内容。

###片段视图

Express 的视图系统还支持片段视图 (partials),它就是一个页面的片段,通常是重复的内容,用于迭代显示。通过它你可以将相对独立的页面块分割出去,而且可以避免显式地使用 for 循环。
```ejs
<ul><%- partial('listitem', items) %></ul>
```
partial 是一个可以在视图中使用函数,它接受两个参数,第一个是片段视图的名称,第二个可以是一个对象或一个数组,如果是一个对象,那么片段视图中上下文变量引用的就是这个对象;如果是一个数组,那么其中每个元素依次被迭代应用到片段视图。片段视图中上下文变量名就是视图文件名.

###视图助手

Express 提供了一种叫做视图助手的工具,它的功能是允许在视图中访问一个全局的函数或对象,不用每次调用视图解析的时候单独传入。前面提到的 partial 就是一个视图助手。
视图助手有两类,分别是静态视图助手和动态视图助手。这两者的差别在于,静态视图助手可以是任何类型的对象,包括接受任意参数的函数,但访问到的对象必须是与用户请求无关的,而动态视图助手只能是一个函数,这个函数不能接受参数,但可以访问 req 和 res 对象。
静态视图助手可以通过 app.helpers() 函数注册,它接受一个对象,对象的每个属性名称为视图助手的名称,属性值对应视图助手的值。动态视图助手则通过 app.dynamicHelpers()注册,方法与静态视图助手相同,但每个属性的值必须为一个函数,该函数提供 req 和 res ,参见下面这个示例:
```node
var util = require('util');
app.helpers({
	inspect: function(obj) {
		return util.inspect(obj, true);
	}
});
app.dynamicHelpers({
	headers: function(req, res) {
		return req.headers;
	}
});
app.get('/helper', function(req, res) {
	res.render('helper', {
		title: 'Helpers'
	});
});
```
使用时：<%=inspect(headers)%>
视图助手的本质其实就是给所有视图注册了全局变量,因此无需每次在调用模板引擎时传递数据对象。当我们在后面使用 session 时会发现它是非常有用的。

##连接数据库

打开工程目录中的 package.json,在 dependencies 属性中添加一行代码:
```json
{
	"name": "microblog"
	, "version": "0.0.1"
	, "private": true
	, "dependencies": {
		"express": "2.5.8"
		, "ejs": ">= 0.0.1"
		, "mongodb": ">= 0.9.9"
	}
}
```

然后运行 npm install 更新依赖的模块。接下来在工程的目录中创建 settings.js 文件,这个文件用于保存数据库的连接信息。我们将用到的数据库命名为 microblog,数据库服务器在本地,因此Settings.js文件的内容如下:
```node
module.exports = {
	cookieSecret: 'microblogbyvoid',
	db: 'microblog',
	host: 'localhost',
};
```
其中, db 是数据库的名称, host 是数据库的地址。 cookieSecret 用于 Cookie 加密与数据库无关.
接下来在 models 子目录中创建 db.js,内容是:
```node
var settings = require('../settings');
var Db = require('mongodb').Db;
var Connection = require('mongodb').Connection;
var Server = require('mongodb').Server;
module.exports = new Db(settings.db, new Server(settings.host, Connection.DEFAULT_PORT, {}));
```
以上代码通过 module.exports 输出了创建的数据库连接,由于模块只会被加载一次,以后我们在其他文件中使用时均为这一个实例。

##会话支持(Session support)

会话是一种持久的网络协议,用于完成服务器和客户端之间的一些交互行为。会话是一个比连接粒度更大的概念,一次会话可能包含多次连接,每次连接都被认为是会话的一次操作。在网络应用开发中,有必要实现会话以帮助用户交互。例如网上购物的场景,用户浏览了多个页面,购买了一些物品,这些请求在多次连接中完成。许多应用层网络协议都是由会话支持的,如 FTP、 Telnet 等,而 HTTP 协议是无状态的,本身不支持会话,因此在没有额外手段的帮助下,前面场景中服务器不知道用户购买了什么。
为了在无状态的 HTTP 协议之上实现会话,Cookie 诞生了。Cookie 是一些存储在客户端的信息,每次连接的时候由浏览器向服务器递交,服务器也向浏览器发起存储 Cookie 的请求,依靠这样的手段服务器可以识别客户端。我们通常意义上的 HTTP 会话功能就是这样实现的。具体来说,浏览器首次向服务器发起请求时,服务器生成一个唯一标识符并发送给客户端浏览器,浏览器将这个唯一标识符存储在 Cookie 中,以后每次再发起请求,客户端浏览器都会向服务器传送这个唯一标识符,服务器通过这个唯一标识符来识别用户。
对于开发者来说,我们无须关心浏览器端的存储,需要关注的仅仅是如何通过这个唯一标识符来识别用户。很多服务端脚本语言都有会话功能,如 PHP,把每个唯一标识符存储到文件中。Express 也提供了会话中间件,默认情况下是把用户信息存储在内存中,但我们既然已经有了 MongoDB,不妨把会话信息存储在数据库中,便于持久维护。为了使用这一功能,我们首先要获得一个叫做 connect-mongo 的模块,在 package.json 中添加一行代码:
```json
{
	"name": "microblog"
	, "version": "0.0.1"
	, "private": true
	, "dependencies": {
		"express": "2.5.8"
		, "ejs": ">= 0.0.1"
		, "connect-mongo": ">= 0.1.7"
		, "mongodb": ">= 0.9.9"
	}
}
```
运行 npm install 获得模块。然后打开 app.js,添加以下内容:
```node
var MongoStore = require('connect-mongo');
var settings = require('../settings');
app.configure(function(){
	app.set('views', __dirname + '/views');
	app.set('view engine', 'ejs');
	app.use(express.bodyParser());
	app.use(express.methodOverride());
	app.use(express.cookieParser());
	app.use(express.session({
		secret: settings.cookieSecret,
		store: new MongoStore({
			db: settings.db
		})
	}));
	app.use(app.router);
	app.use(express.static(__dirname + '/public'));
});
```
其中 express.cookieParser() 是 Cookie 解析的中间件。 express.session() 则提供会话支持,设置它的 store 参数为 MongoStore 实例,把会话信息存储到数据库中,以避免丢失。在后面的小节中,我们可以通过 req.session 获取当前用户的会话对象,以维护用户相关的信息。