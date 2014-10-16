title: Ubuntu下SVN的使用
date: 2014-10-16 09:18:32
tags: svn
categories:
---
##ubuntu SVN命令大全

the source article [http://blog.csdn.net/netwalk/article/details/14043657](http://blog.csdn.net/netwalk/article/details/14043657)

###将文件checkout到本地目录

svn checkout path（path 是服务器上的目录）
```bash
$ svn checkout svn://192.168.1.224/pro
$ svn co svn://192.168.1.224/pro (简写)
```

###往版本库中添加新的文件

```bash
$ svnadd file   
$ svn add test.php(添加test.php)   
$ svn add *.php(添加当前目录下所有的php文件)   
```

###将改动的文件提交到版本库

$ svn commit -m "LogMessage" [-N] [--no-unlock] PATH(如果选择了保持锁,就使用–no- unlock开关) 
```bash
$ svn commit -m 'add test file for my test' test.php 
$ svn c (简写)
```

###更新到某个版本

$ svn update -rm path 
例如：$ svn update 如果后面没有目录，默认将当前目录以及子目录下的所有文件都更新到最新版本。 
```bash
$ svn update -r 200 test.php(将版本库中的文件test.php还原到版本200)   
$ svn update test.php(更新，于版本库同步。如果在提交的时候提示过期的话，是因为冲突，需要先update，修改文 件，然后清除$ svn resolved，最后再提交commit) 
$ svn up (简写)
```

###删除文件

$ svn delete path -m 'delete test fle' 

```bash
$ svn delete test.php 然后再$ svn ci -m 'delete test file'
$ svn (del, remove, rm) ##(简写)
```

###比较差异

$ svn diff path(将修改的文件与基础版本比较) 
```bash
$ svn diff test.php
```

$ svn diff -r m:n path(对版本m和版本n比较差异) 
```bash 
$ svn diff -r 200:201 test.php 
$ svn di  (简写)
```

###查看文件或者目录状态

svn status path（目录下的文件和子目录的状态，正常状态不显示）
【?：不在svn的控制中；M：内容被修改；C：发生冲突；A：预定加入到版本库；K：被锁定】 

svn status -v path(显示 文件和子目录状态) 
第一列保持相同，第二列显示工作版本号，第三和第四列显示最后一次修改的版本号和修改人。 

注：svn status、svn diff和 svn revert 这三条命令在没有网络的情况下也可以执行的，原因是svn在本地的.svn中保留了本地版本的原始拷贝。 
简写：svn st 

###解决冲突

$ svn resolved: 移除工作副本的目录或文件的“冲突”状态。  
用法: $ resolved PATH… 
注意: 本子命令不会依语法来解决冲突或是移除冲突标记；它只是移除冲突的相关文件，然后让 PATH 可以再次提交。 

###查看日志（log）

```bash
$ svn log path
```

##同步更新[勾子]

be here first, You can update it later!

##为Eclipse配置SVN

http://subclipse.tigris.org/files/documents/906/38385/site-1.2.3.zip ，可以从这个地址下载Eclipse的插件，拷贝到plugins目录中以后，重启Eclipse就可以打开SVN的视图了。

也可以通过官方的 安装页面来进行在线安装：http://subclipse.tigris.org/install.html