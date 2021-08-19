---
toc: true
layout: post
description: Linux and shell.
categories: [markdown]
title: Linux常用命令与shell脚本编程
---

Linux是一个常用的基于UNIX的操作系统.平时我们可以使用X Windows提供的图形化界面来与系统交互.但是有时候,我们没办法使用图形化界面(如使用telne或ssh的时候),这时就需要我们掌握常用的Linux命令.
# Linux简介
## 系统组成
我们先来看看Linux系统的组成,Linux系统由三部分组成:内核(The kernel),壳(The shell)和程序(The programs).内核是操作系统的枢纽,它负责为程序分配时间和内存,处理文件储存和系统调用.壳则是一个命令行解释器,也是用户与内核交互的界面.每当一个用户登陆时,系统都会为这个用户启动一个shell,举个例子,当我们使用`rm myfile`这个命令行的时候,shell会搜索文件存储,找到存有程序rm的文件,然后通过系统调用请求内核执行rm,内核就会在myfile上执行rm,当进程处理完毕,shell会返回%给用户,表示已准备好接受下一个命令输入.
输入部分命令再按tab可以补全命令(如果命令在历史中),按上下键可以切换至上一条或下一条输入的命令.
## 文件和进程
UNIX中的所有东西都是一个进程或者一个文件.进程指的是由一个唯一的PID标志的执行程序,而文件则是数据的集合. 
## 目录结构
UNIX的文件系统是一个树形的层次结构.这个结构的根通常称为root  
特殊路径:
`~`:  
当前用户的根目录  
`..`:  
上一路径  
`.`:  
当前路径
# Linux常用命令
`ls`  
list,显示路径下所有文件(不包括隐藏文件)  
`ls -a`  
list --all.显示隐藏文件  
`ls -l`  
list --long.显示详细信息  
`mkdir`  
make directory,创建新路径  
`cd`  
change directory.可以用.和..表示当前路径和母路径.cd不加路径会回到~  
`pwd`  
print  working directory.输出当前工作目录  
`cp file1 file2`  
copy.将file1复制到file2  
`mv file1 file2`  
move.将file1移动到file2.如果在同一路径执行此操作,相当于rename  
`rm`  
remove.删除文件  
`rmdir`  
remove directory.删除一个空目录.(注意必须是空目录)  
`clear`  
clear screen.清除屏幕上的记录.  
`cat file`  
concatenate.将一个文件的所有内容显示到屏幕上.  
`read line`  
接受输入,读入一行,并将其输出到变量line中.  
eg:  
```sh 
cat a.txt|while read line  
do  
echo $line
done
```
`more`  
将一个文件的一页输出到屏幕上.可以使用空格来切换至下一页,按b切换至上一页.使用q来退出阅读模式.对于长文件,用more打开比用cat好.  
`less`  
less是more的加强版.在使用less时,我们可以通过'/+正则表达式'来搜索关键字,使用n可以查看下一个搜索结果.还可以使用page on和page down等.  
`head`  
将一个文件的头十行输出到屏幕上.  
`tail`  
将一个文件的最后十行输出到屏幕上.  
`grep`  
grep是UNIX工具中的一个,可以用来搜索文件中的某些关键词.  
eg:  
`grep science science.txt`  
会输出science.txt中所有包含science的行数.  
还有其他的选项:  
```sh 
-i 忽略大小写
-v 显示不匹配的行
-n 在每一行前显示行数
-c 只答应匹配的行数的总数
```
`wc`  
word count.也是一个小工具,统计词的总数.  
eg:  
`wc -w science.txt`输出文件中所有词的总数.  
`wc -l science.txt`输出文件的总行数.  
`wc -c science.txt`输出文件的字母数.  
`>`和`>>`  
`>`和`>>`可以作为对输出进行重定向的符号.在linux中,shell的标准输入是键盘,标准输出是屏幕,但实际上我们可以将文件作为输入和输出.  
eg1:  
`cat>list1`  
这个命令会创建一个新的文件list1,并将其作为输出,输入仍然是标准输入(即键盘).如果list1已存在,会将其覆盖.  
之后进行输入:  
`aaaaaa`  
`bbbbb`  
再以Ctrl+D结尾(文件结束符).此时,输入结束,可以看到多了一个list1文件,其中存储着我们输入的一串a和b.  
我们可以使用`cat list1`来读取list1到屏幕上.  
eg2:  
`cat >>list1`  
这个命令会打开旧的文件list1,然后将输入加到末尾.如果list1不存在,会新建一个list1.  
eg3:  
`cat list1 list2>list3`  
这个命令会把list1和list2作为输入输出到list3.  
`sort`  
`sort`可以对输入进行排序.  
'<'  
'<'可以对输出进行重定向.  
eg:  
`sort<list`  
会对list进行排序  
`sort<list>sortlist`  
会将list先排序,再输出到sortlist中.  
`who`  
查看当前系统的使用用户.  
`|`  
`|`是管道的符号,可以将一个命令的输出连接到另一个命令的输入.  
eg1:  
`who|sort`可以将当前的用户排序后输出.  
eg2:  
`who|wc -l`可以将当前用户数目输出.  
eg3:  
`cat list1 list2|grep a|sort`找出list1和list2中所有含p的行并排序  
'\*'和`?`  
通配符.`*`匹配任意个数的字符.`?`匹配单个字符.  
文件和路径的命令:  
尽量避免使用空格和/*%&,只使用.和_.文件通常以小写字母开头,以.加上按时文件类型的字母结尾(类似于后缀名)  
`man`  
manual.可以查看命令的帮助.  
`whatis`  
可以查看简单版的帮助.  
`apropos`  
可以查看帮助中带有此关键词的命令.  
**tips**:文件系统安全性:  
linux系统中的文件具有不同的执行权限.输入ls -l,可以看到如下信息:  
![linux_file_system](https://github.com/dayekuaipao/dayekuaipao.github.io/tree/master/images/linux_file_system.png)
rwx分别代表read,write和excute.  
对于文件,read表示可以读以及复制,write表示可以修改文件,excute表示可以可以执行这个文件.  
对于目录,read表示可以list这个路径,write表示可以删除这个目录或者移动文件到目录里面,excute表示可以进入目录下并对文件进行操作(如果有文件的相关权限的话).  
`chmod`  
change mode.修改文件权限.   
权限的相关选项如下:  

symbol|meaning  
---|---  
u|user  
g|group  
o|other  
a|all  
r|read  
w|write  
x|excute  
\+|add permission  
\-|take away permission  

**tips**:进程  
进程是一个由唯一PID(Process Identifier)标志的正在执行的程序.  
`ps`  
process status.查看进程的相关信息.  
进程有三种状态:前台运行,后台运行,以及挂起.通常来说,在一个进程执行完之前,shell不会返回提示符.但是有时候,有些进程的执行需要很长事件,从而占用终端.因此我们可以将进程的执行移到后台,这样就可以执行其他命令.  
`&`  
在命令最后加上&就可以使命令在后台运行.这个命令会返回当前的工作编号以及PID.  
`Ctrl+z`  
可以使当前进程挂起.  
`bg +%工作编号`  
background.可以将进程转到后台运行.  
`sleep`  
使当前进程休眠一段时间.  
`jobs`  
当进程运行,后台运行或者挂起时,会进入一个工作列表中.使用这个命令可以查看这个列表.  
`fg +%工作编号`  
foreground.可以将进程转入前台进行  
`Ctrl+c`  
可以杀死当前进程.  
`kill +PID或者%工作编号`  
杀死进程  
`kill -9 +PID或者%工作编号`  
强制杀死进程.  
`df`  
查看文件系统剩余空间  
`du`  
查看子目录的暂用空间  
`compress`  
压缩文件  
`gzip`  
压缩文件,更高效  
`file`  
查看路径下文件的类别(目录,txt等)  
`history`  
查看使用过的命令的记录.  
linux安装软件一般有如下步骤:  
	• 下载源码  
	• 解压源码文件  
	• 编译源码  
	• 安装可执行文件  
	• 设置路径  
其中常用的命令有:  
`tar -xvf`  
 解压.tar文件  
`make`  
编译文件  
`make install`  
安装文件  
# shell脚本编程  
## shell脚本的基本要素  
### 解释器
`#!/bin/sh`以`#!`开头,表示用shell来执行这个脚本(脚本文件都一样,例如,如果是python的话就写python解释器的路径)  
### 注释
`#this is the script`以`#`开头的是注释
### 命令
`echo "hello world!"`
## shell语法
### 变量
变量指可以用名字访问到存储内容的内存.  
先声明变量:  
`sum=0`
注意等号两边不能有空格  
接下来是使用变量:  
`echo $sum`
使用$来使用变量  
### 数据类型
shell中只有两种数据类型,数字和字符串  
如果我们要将变量作为数字进行运算,我们可以使用`let`或者(())  
eg:  
`let "sum2=$sum+1"`
`((sum2=$sum+1))`
如果不加,sum会被当做字符串运算,sum2会是1+1,而不是2.  
shell支持+-*/%这5种运算.  
### 流程控制  
#### if语句
第一种:  
```
# 条件为真执行；条件为假不做任何事情。
if condition ; then
commands
fi
```
第二种:  
```
# 条件为真执行command1；条件为假执行command2
if condition ; then
command1
else
command2
fi
```
第三种:   
```
# 条件1为真时执行command1；条件2为真时执行command2
if condition1 ; then
command1
elif condition2 ; then
command2
fi
```
#### test语句
第一种形式:  
`test expression`
第二种形式:  
`[ expression ]`中括号和语句间必须要有空格
如果表达式为真,test返回0.否则为1.在Linux中,程序返回值范围为0~255.其中0表示成功,其他值都表示失败.
#### exit语句
`exit 0`#成功
`exit 1`#失败
停止脚本并返回值
#### case语句
case语句用于匹配字符串
```
case word in
模式) 声明 ;;
esac
```
eg:
```
case $1 in
-h)  help;; 
*)  break;;
esac
```
$1表示传递给脚本的第一个参数 
#### 循环语句:
第一种形式:  
```
while condition; do
command
done
```
第二种形式:  
```
until condition; do
command
done
```
第三种形式:  
```
for var in list; do
command1
done
```
(list是一个字符串列表,其中的字符串用空格分开)

### 函数

shell中的函数定义如下:  

```
function()
{
}
```

注意没有形参,在函数中,形参是用$1,$2这样的变量代替的.分别表示第一个参数和第二个参数.  

### 断言assert

eg:  
`assert "$condition" $LINENO`  
condition为真时才会继续执行,并输出此时的行号($LINENO是自带变量.通常shell自带变量都是全部大写的,所以我们的自定义变量一般用小写)
---
到此,Linux命令和shell的语法部分的博客就结束了.这部分还是主要在实际使用中不断学习,这里只是一个简单的入门.以下列出两个参考文献,大家可以去看看.
参考文献:
> http://www.ee.surrey.ac.uk/Teaching/Unix/  
> <Linux就是这个范儿>  
> <鸟哥的Linux私房菜>  
