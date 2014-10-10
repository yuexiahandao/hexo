title: NodeJs简介
date: 2014-10-10 21:04:33
tags: NodeJs
categories: NodeJs
---
##一、NodeJs概述
Node.js 是一个为实时 Web(Real-time Web)应用开发而诞生的平台,它从诞生之初就充分考虑了在实时响应、超大规模数据要求下架构的可扩展性。这使得它摒弃了传统平台依靠多线程来实现高并发的设计思路,而采用了单线程、异步式I/O、事件驱动式的程序设计模型。这些特性不仅带来了巨大的性能提升,还减少了多线程程序设计的复杂性,进而提高了开发效率。
    
Node.js 有着强大而灵活的 包管,目前已经有上万个第三方模块,其中有网站开发框架,理器 (node package manager,npm)有 MySQL、PostgreSQL、MongoDB 数据库接口,有模板语言解析、CSS 生成工具、邮件、加密、图形、调试支持,甚至还有图形用户界面和操作系统 API工具。由 VMware 公司建立的云计算平台 Cloud Foundry 率先支持了 Node.js。2011年6月,微软宣布与 Joyent 公司合作,将 Node.js 移植到 Windows,同时 Windows Azure 云计算平台也支持 Node.js。Node.js 目前还处在迅速发展阶段,相信在不久的未来它一定会成为流行的Web应用开发平台。让我们从现在开始,一同探索 Node.js 的美妙世界吧!
    
Node.js 是一个让 JavaScript 运行在浏览器之外的平台。它实现了诸如文件系统、模块、包、操作系统 API、网络通信等 Core JavaScript 没有或者不完善的功能。历史上将JavaScript移植到浏览器外的计划不止一个,但Node.js 是最出色的一个。随着 Node.js 的成功,各种浏览器外的 JavaScript 实现逐步兴起,因此产生了 CommonJS 规范。CommonJS 试图拟定一套完整的 JavaScript 规范,以弥补普通应用程序所需的 API,譬如文件系统访问、命令行、模块管理、函数库集成等功能。CommonJS 制定者希望众多服务端 JavaScript 实现遵循CommonJS 规范,以便相互兼容和代码复用。Node.js 的部份实现遵循了CommonJS规范,但由于两者还都处于诞生之初的快速变化期,也会有不一致的地方。

Node.js 的 JavaScript 引擎是 V8,来自 Google Chrome 项目。V8 号称是目前世界上最快的 JavaScript 引擎,经历了数次引擎革命,它的 JIT(Just-in-time Compilation,即时编译)执行速度已经快到了接近本地代码的执行速度。Node.js 不运行在浏览器中,所以也就不存在 JavaScript 的浏览器兼容性问题,你可以放心地使用 JavaScript 语言的所有特性。
    
##二、NodeJs能做什么

正如 JavaScript 为客户端而生,Node.js 为网络而生。Node.js 能做的远不止开发一个网站那么简单,使用 Node.js,你可以轻松地开发:
        * 具有复杂逻辑的网站;
        * 基于社交网络的大规模 Web 应用;
        * Web Socket 服务器;
        * TCP/UDP 套接字应用程序;
        * 命令行工具;
        * 交互式终端程序;
        * 带有图形用户界面的本地应用程序;
        * 单元测试工具;
        * 客户端 JavaScript 编译器。
        
##三、异步式 I/O 与事件驱动

``` bash
db.query('SELECT * from some_table', function(res) {
    res.output();
});
```

这段代码中 db.query 的第二个参数是一个函数,我们称为 回调函数 。进程在执行到db.query 的时候,不会等待结果返回,而是直接继续执行后面的语句,直到进入事件循环。当数据库查询结果返回时,会将事件发送到事件队列,等到线程进入事件循环以后,才会调用之前的回调函数继续执行后面的逻辑。Node.js 的异步机制是基于事件的,所有的磁盘 I/O、网络通信、数据库查询都以非阻塞.

Node.js 的异步机制是基于事件的,所有的磁盘 I/O、网络通信、数据库查询都以非阻塞的方式请求,返回的结果由事件循环来处理。图1-1 描述了这个机制。Node.js 进程在同一时刻只会处理一个事件,完成后立即进入事件循环检查并处理后面的事件。这样做的好处是,CPU 和内存在同一时间集中处理一件事,同时尽可能让耗时的 I/O 操作并行执行。对于低速连接攻击,Node.js 只是在事件队列中增加请求,等待操作系统的回应,因而不会有任何多线程开销,很大程度上可以提高 Web 应用的健壮性,防止恶意攻击。

这种异步事件模式的弊端也是显而易见的,因为它不符合开发者的常规线性思路,往往需要把一个完整的逻辑拆分为一个个事件,增加了开发和调试难度。针对这个问题,Node.js第三方模块提出了很多解决方案,我们会详细讨论。

##四、Node.js 的性能

Node.js 用异步式 I/O 和事件驱动代替多线程,带来了可观的性能提升。Node.js 除了使用 V8 作为JavaScript引擎以外,还使用了高效的 libev 和 libeio 库支持事件驱动和异步式 I/O。
图1-2 是 Node.js 架构的示意图。

Node.js 的开发者在 libev 和 libeio 的基础上还抽象出了层 libuv。对于 POSIX 1 操作系统,libuv 通过封装 libev 和 libeio 来利用 epoll 或 kqueue。而在 Windows 下, libuv 使用了 Windows的 IOCP(Input/Output Completion Port,输入输出完成端口)机制,以在不同平台下实现同样的高性能。

##五、CommonJS

正如当年为了统一 JavaScript 语言标准,人们制定了 ECMAScript 规范一样,如今为了统一 JavaScript 在浏览器之外的实现,CommonJS 诞生了。CommonJS 试图定义一套普通应用程序使用的API,从而填补 JavaScript 标准库过于简单的不足。CommonJS 的终极目标是制定一个像 C++ 标准库一样的规范,使得基于 CommonJS API 的应用程序可以在不同的环境下运行,就像用 C++ 编写的应用程序可以使用不同的编译器和运行时函数库一样。为了保持中立,CommonJS 不参与标准库实现,其实现交给像 Node.js 之类的项目来完成。

CommonJS 规范包括了模块(modules)、包(packages)、系统(system)、二进制(binary)、控制台(console)、编码(encodings)、文件系统(filesystems)、套接字(sockets)、单元测试(unit testing)等部分。目前大部分标准都在拟定和讨论之中,已经发布的标准有Modules/1.0、Modules/1.1、Modules/1.1.1、Packages/1.0、System/1.0。

Node.js 是目前 CommonJS 规范最热门的一个实现,它基于 CommonJS 的 Modules/1.0 规范实现了 Node.js 的模块,同时随着 CommonJS 规范的更新,Node.js 也在不断跟进。由于目前 CommonJS 大部分规范还在起草阶段,Node.js 已经率先实现了一些功能,并将其反馈给CommonJS 规范制定组织,但 Node.js 并不完全遵循 CommonJS 规范。这是所有规范制定者都会遇到的尴尬局面,因为规范的制定总是滞后于技术的发展。