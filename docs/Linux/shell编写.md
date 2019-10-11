不能在windows下编写Linux运行的shell，两者换行符不同。或者使用专业的插件，如PyCharm的BashSupport插件



弱类型语言，无须定义变量的类型

Shell脚本语言是弱类型语言（无须定义变量的类型即可使用），在Unix/Linux中主要有两大类shell: 
一类是 Bourne  shell ,另一类是 C shell

Bourne shell 包括 Bourne shell (sh)、Korn shell(ksh)、Bourne Again Shell 三种类型。 
C shell包括csh、tcsh两种类型
查看系统默认的shell： echo  $SHELL
查看系统支持的shell： cat  /etc/shells



## Shell脚本的执行

Shell脚本的执行通常可以采用以下几种方式。

1、bash script-name 或 sh script-name     这是当脚本文件本身没有执行权限时常使用的方法

2、path/script-name 或./script-name    在当前路径下执行脚本，需要将脚本文件的权限改为可执行。然后使用脚本的绝对路径或相对路径就可以直接执行脚本了。

3、source script-name 或 . script-name 这种方法通常是使用source或 “.”(点号）读入或加载指定的Shell脚本文件，然后依次执行指定的Shell脚本中的语句。这些语句将在当前父 shell 脚本进程中执行（其他几种模式都会启用新的进程执行该脚本进程）。



第一行指定解释器

#! /bin/bash

bash 与 sh 的区别：sh 为 bash的软连接，大多数情况下，脚本使用“#！/bin/bash”和“#！/bin/sh”是没有区别的，但更规范的写法是在脚本的开头使用    #！/bin/bash 



变量

对于常规变量的字符串定义变量值应加双引号，并且等号前后不能有空格，需要强引用的，则用单引号（‘’），如果是命令的引用，则用反引号（``）。

- 双引号：允许通过$符号引用其他变量值
- 单引号：禁止引用其他变量值，$视为普通字符
- 反撇号：命令替换，提取命令执行后的输出结果 全局变量的定义方法 export 变量名

![img](https://img-blog.csdn.net/20181010193422821?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



位置参数

位置参数是一种在调用 Shell 程序的命令行中按照各自的位置决定的变量，是在程序名之后输入的参数。 位置参数之间用空格分隔，Shell取第一个位置参数替换程序文件中的 $1，第二个替换 $2 , 依次类推。$0 是一个特殊变量，它的内容是当前这个shell程序的文件名，所以 $0 不是一个位置参数。

假如我现在有一个 1.sh脚本文件，内容如下

#! /bin/bash

echo $1

echo $(($2+$3))

当我执行时，我在文件名后加3个参数
![img](https://img-blog.csdn.net/20181008212407723?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



预定义变量

预定义变量和环境变量相类似，也是在Shell一开始就定义的变量，不同的是，用户只能根据shell的定义来使用这些变量，所有预定义变量都是由符号“$”和另一个符号组成。 常见的Shell预定义变量有以下几种。

- $# ：位置参数的数量
- $* ：所有位置参数的内容
- $? ：命令执行后返回的状态，0表示没有错误，非0表示有错误
- $$ ：当前进程的进程号
- $! ：后台运行的最后一个进程号
- $0 ：当前执行的进程

假如我现在有一个 1.sh脚本文件，内容如下

#! /bin/bash

echo $1

echo ${2}+${3} 

echo $#             #打印出位置参数的数量

echo $*             #打印出位置参数的内容

echo $?             #打印出命令执行后返回的状态

echo $$             #打印出当前进程的进程号

echo $0             #打印出当前进程的进程名


当我执行时，我在文件名后加3个参数

![img](https://img-blog.csdn.net/20181008203947438?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



参考：

https://blog.csdn.net/qq_36119192/article/details/82964713#Shell%E4%B8%AD%E7%9A%84%E5%8F%98%E9%87%8F