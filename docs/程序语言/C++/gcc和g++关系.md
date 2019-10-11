gcc 最开始的时候是 GNU C Compiler, 如你所知，就是一个c编译器。但是后来因为这个项目里边集成了更多其他不同语言的编译器，GCC就代表 the GNU Compiler Collection，所以表示一堆编译器的合集。 g++则是GCC的c++编译器。

现在你在编译代码时调用的gcc，已经不是当初那个c语言编译器了，更确切的说他是一个驱动程序，根据代码的后缀名来判断调用c编译器还是c++编译器 (g++)。比如你的代码后缀是*.c，他会调用c编译器还有linker去链接c的library。如果你的代码后缀是cpp, 他会调用g++编译器，当然library call也是c++版本的。

当然我说了这么多你可能感到有些混乱，没关系，你就把gcc当成c语言编译器，g++当成c++语言编译器用就是了。

作者：李锋

链接：https://www.zhihu.com/question/20940822/answer/16667772

来源：知乎

著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



作者：wuxinliulei

链接：https://www.zhihu.com/question/20940822/answer/69547180

来源：知乎

著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

**一：gcc与g++比较**

编译c/c++代码的时候，有人用gcc，有人用g++，于是各种说法都来了，譬如c代码用gcc，而 c++代码用g++，或者说编译用gcc，链接用g++，一时也不知哪个说法正确，如果再遇上个extern "C"，分歧就更多了，这里我想作个了结，毕竟知识的目的是令人更清醒，而不是更糊涂。

**误区一:gcc只能编译c代码,g++只能编译c++代码**
两者都可以，但是请注意：
1.**后缀为.c的，gcc把它当作是C程序，而g++当作是c++程序；后缀为.cpp的，两者都会认为是c++程序，注意，虽然c++是c的超集，但是两者对语法的要求是有区别的，**例如：

```text
#include<stdio.h>
int main(int argc, char* argv[]) {
   if(argv == 0) return;
   printString(argv);
   return 0;
}
int printString(char* string) {
  sprintf(string, "This is a test.\n");
}
```

如果按照C的语法规则，OK，没问题，但是，一旦把后缀改为cpp，立刻报三个错：“printString未定义”；
“cannot convert `char**' to `char*”；
”return-statement with no value“；
分别对应前面红色标注的部分。可见C++的语法规则更加严谨一些。
2.编译阶段，g++会调用gcc，对于c++代码，两者是等价的，但是因为gcc命令不能自动和C＋＋程序使用的库联接，所以通常用g++来完成链接，为了统一起见，干脆编译/链接统统用g++了，这就给人一种错觉，好像cpp程序只能用g++似的。

误区二:gcc不会定义__cplusplus宏，而g++会
实际上，这个宏只是标志着编译器将会把代码按C还是C++语法来解释，如上所述，如果后缀为.c，并且采用gcc编译器，则该宏就是未定义的，否则，就是已定义。

误区三:编译只能用gcc，链接只能用g++
严格来说，这句话不算错误，但是它混淆了概念，应该这样说：编译可以用gcc/g++，而链接可以用g++或者gcc -lstdc++。因为gcc命令不能自动和C＋＋程序使用的库联接，所以通常使用g++来完成联接。但在编译阶段，g++会自动调用gcc，二者等价。

误区四:extern "C"与gcc/g++有关系
实际上并无关系，无论是gcc还是g++，用extern "c"时，都是以C的命名方式来为symbol命名，否则，都以c++方式命名。试验如下：
me.h：
extern "C" void CppPrintf(void);

me.cpp:
#include 
#include "me.h"
using namespace std;
void CppPrintf(void)
{
     cout << "Hello\n";
}

test.cpp:
#include 
#include 
#include "me.h"        
int main(void)
{
    CppPrintf();
    return 0;
}

1. 先给me.h加上extern "C"，看用gcc和g++命名有什么不同
[root@root G++]# g++ -S me.cpp
[root@root G++]# less me.s
.globl _Z9CppPrintfv        //注意此函数的命名
        .type   CppPrintf, @function
[root@root GCC]# gcc -S me.cpp
[root@root GCC]# less me.s
.globl _Z9CppPrintfv        //注意此函数的命名
        .type   CppPrintf, @function
完全相同！

2. 去掉me.h中extern "C"，看用gcc和g++命名有什么不同
[root@root GCC]# gcc -S me.cpp
[root@root GCC]# less me.s
.globl _Z9CppPrintfv        //注意此函数的命名
        .type   _Z9CppPrintfv, @function
[root@root G++]# g++ -S me.cpp
[root@root G++]# less me.s
.globl _Z9CppPrintfv        //注意此函数的命名
        .type   _Z9CppPrintfv, @function
完全相同！
【结论】完全相同，可见extern "C"与采用gcc/g++并无关系，以上的试验还间接的印证了前面的说法：在编译阶段，g++是调用gcc的。

**二：gcc和ｇ++的包含头文件库文件方法**

-l参数就是用来指定程序要链接的库，-l参数紧接着就是库名，那么库名跟真正的库文件名有什么关系呢？就拿数学库来说，他的库名是m，他的库文件名是libm.so，很容易看出，把库文件名的头lib和尾.so去掉就是库名了。

好了现在我们知道怎么得到库名，当我们自已要用到一个第三方提供的库名字libtest.so，那么我们只要把 libtest.so拷贝到/usr/lib里，编译时加上-ltest参数，我们就能用上libtest.so库了（当然要用libtest.so库里 的函数，我们还需要与libtest.so配套的头文件）

放在/lib和/usr/lib和/usr/local/lib里的库直接用-l参数就能链接了，但如果库文件没放 在这三个目录里，而是放在其他目录里，这时我们只用-l参数的话，链接还是会出错，出错信息大概是：“/usr/bin/ld: cannot find -lxxx”，也就是链接程序ld在那3个目录里找不到libxxx.so，这时另外一个参数-L就派上用场了，比如常用的X11的库，它在/usr /X11R6/lib目录下，我们编译时就要用-L/usr/X11R6/lib -lX11参数，-L参数跟着的是库文件所在的目录名。再比如我们把libtest.so放在/aaa/bbb/ccc目录下，那链接参数就是-L /aaa/bbb/ccc -ltest

另外，大部分libxxxx.so只是一个链接，以RH9为例，比如libm.so它链接到/lib/libm.so.x，/lib/libm.so.6又链接到/lib/libm-2.3.2.so，

如果没有这样的链接，还是会出错，因为ld只会找libxxxx.so，所以如果你要用到xxxx库，而只有libxxxx.so.x或者libxxxx-x.x.x.so，做一个链接就可以了ln -s libxxxx-x.x.x.so libxxxx.so

手工来写链接参数总是很麻烦的，还好很多库开发包提供了生成链接参数的程序，名字一般叫xxxx-config，一般放在/usr/bin目录下，比如

gtk1.2的链接参数生成程序是gtk-config，执行gtk-config --libs就能得到以下输出"-L/usr/lib -L/usr/X11R6/lib -lgtk -lgdk -rdynamic

-lgmodule -lglib -ldl -lXi -lXext -lX11 -lm"，这就是编译一个gtk1.2程序所需的gtk链接参数，xxx-config除了--libs参数外还有一个参数是--cflags用来生成头 文件包含目录的，也就是-I参数，在下面我们将会讲到。你可以试试执行gtk-config --libs --cflags，看看输出结果

现在的问题就是怎样用这些输出结果了，最笨的方法就是复制粘贴或者照抄，聪明的办法是在编译命令行里加入这个 `xxxx-config --libs --cflags`，比如编译一个gtk程序：gcc gtktest.c `gtk-config --libs --cflags`这样就差不多了。注意`不是单引号，而是1键左边那个键。

5、-include和-I参数

-include用来包含头文件，但一般情况下包含头文件都在源码里用#include xxxxxx实现，-include参数很少用。-I参数是用来指定头文件目录，/usr/include目录一般是不用指定的，gcc知道去那里找，但 是如果头文件不在/usr/include里我们就要用-I参数指定了，比如头文件放在/myinclude目录里，那编译命令行就要加上-I /myinclude参数了，如果不加你会得到一个"xxxx.h: No such file or directory"的错误。-I参数可以用相对路径，比如头文件在当前目录，可以用-I.来指定。
**结论例子：**

g++ curltest.cpp -o curltest -L/mnt/hgfs/windows/curl-7.19.5/lib/.libs -lcurl -I/mnt/hgfs/windows/curl-7.19.5/include