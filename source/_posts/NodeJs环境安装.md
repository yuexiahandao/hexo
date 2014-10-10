title: NodeJs环境安装
date: 2014-10-10 21:05:51
tags: NodeJs
categories: NodeJs
---
#一、安装包安装

NodeJs在很多的系统下有独立的安装包，而且安装之后会随之连同安装npm！

#二、编译源代码

Node.js从0.6版本开始已经实现了源代码级别的跨平台,因此我们可以使用不同的编译命令将同一份源代码的基础上编译为不同平台下的原生可执行代码。在编译之前,要先获取源码包。我们建议访问[http://nodejs.org](http://nodejs.org),点击Download链接,然后选择Source Code,下载正式发布的源码包。如果你需要开发中的版本,可以通过[https://github.com/joyent/node/zipball/master](https://github.com/joyent/node/zipball/master) 获得,或者在命令行下输入 

    $ git clone git://github.com/joyent/node.git

从git获得最新的分支。

#三、安装 Node 包管理器

Node 包管理器(npm)是一个由 Node.js 官方提供的第三方包管理工具,就像 PHP 的Pear、Python 的 PyPI 一样。npm 是一个完全由 JavaScript 实现的命令行工具,通过 Node.js 执行,因此严格来讲它不属于 Node.js 的一部分。在最初的版本中,我们需要在安装完 Node.js以后手动安装npm。但从 Node.js 0.6 开始,npm 已包含在发行包中了,我们在 Windows、Mac 上安装包和源代码包时会自动同时安装 npm。

#四、安装多版本管理器

迄今为止Node.js 更新速度还很快,有时候新版本还会将旧版本的一些 API 废除,以至于写好的代码不能向下兼容。有时候你可能想要尝试一下新版本有趣的特性,但又想要保持一个相对稳定的环境。基于这种需求,Node.js 的社区开发了多版本管理器,用于在一台机器上维护多个版本的 Node.js 实例,方便按需切换。Node 多版本管理器(Node VersionManager,nvm)是一个通用的叫法,它目前有许多不同的实现。通常我们说的 nvm 是指https://github.com/creationix/nvm 或者 https://github.com/visionmedia/n 。笔者根据个人偏好推荐使用 visionmedia/n,此小节就以它为例子介绍 Node 多版本管理器的用法。

n 是一个十分简洁的 Node 多版本管理器,就连它的名字也不例外。它的名字就是 n ,没错,就一个字母。

如果你已经安装好了 Node.js 和 npm 环境,就可以直接使用 npm install -g n 命令来安装 n 。当然你可能会问:如果我想完全通过 n 来管理 Node.js,那么没安装之前哪来的 npm呢?事实上, n 并不需要 Node.js 驱动,它只是 bash 脚本,使用 npm 安装只是采取一种简便的方式而已。我们可以在 https://github.com/visionmedia/n 下载它的代码,然后使用 makeinstall 命令安装。

关于 n 的更多细节,请访问它的项目主页 https://github.com/visionmedia/n 获取信息。