cuda程序编译器：NVCC（NVIDIA CUDA Compiler）

cuda程序中存在两种类型代码，一种是运行在cpu上的host代码，一种是运行在gpu上的device代码，所以NVCC编译器要保证两部分代码能够编译成二进制文件在不同的机器上执行。

nvcc通过命令行选项可以在不同阶段启动不同的工具完成编译工作，其目的在于隐藏了复杂的CUDA编译细节，并且它不是一个特殊的CUDA编译驱动而是在模仿一般的通用编译驱动如gcc，它接受一定的传统编译选项如宏定义，库函数路径以及编译过程控制等。所有非CUDA编译步骤扔给通用的C编译器，在Windows下是MS的cl编译器，而在Linux下是gcc。

nvcc的工作流程说明如下：

首先是对输入的cu文件有一个预处理过程，这一过程包括的步骤有将该源文件里的宏以及相关引用文件扩展开，然后将预编译已经产生的与C有关的CUDA系统定义的宏扩展开，并合并分支编译的结果。CUDA源文件经过预处理过程后会生成具有.cup后缀名的经过预处理的源文件，经过预处理的源文件是再现程序bug的最好形式。通常，这个预处理过程是隐式完成的，如果想要显示执行，可以用命令nvcc -E x.cu -o x.cup或nvcc -E x.cu > x.cup来实现。

预处理后，首先将预处理的结果送给CUDA前端，即CUDAfe。通过CUDAfe分离源文件，然后调用不同的编译器分别编译。cudafe被称为CUDA frontend，会被调用两次，完成两个工作：一是将主机代码与设备代码分离，生成gpu文件，二是对gpu文件进行dead code analysis，传给nvopencc，未来的版本可能没有第二次调用。 Nvopencc生成ptx文件传给ptxas，最后将cubin或ptx传给fatbin组合成一个设备代码描述符。

同时，在编译阶段CUDA源代码对C语言所扩展的部分将被转成regular ANSI C的源文件，也就可以由一般的C编译器进行更多的编译和连接。device代码被编译成ptx（parallel thread execution）代码和/或二进制代码，host代码则以C文件形式输出，在编译时可将device代码描述符链接到所生成的host代码，将其中的cubin对象作为全局初始化数据数组包含进来，但此时，kernel执行配置也要被转换为CUDA运行启动代码，以加载和启动编译后的kernel。在调用CUDA驱动的API时，CUDA运行时系统会查看这个描述符，根据当前的GPU load一块具有合适ISA的image，然后便可单独执行ptx代码或cubin对象，而忽略nvcc编译得到的host代码。

另外，nvcc的各个编译阶段以及行为是可以通过组合输入文件名和选项命令进行选择的。它是不区分输入文件类型的，如object, library or resource files，仅仅把当前要执行编译阶段需要的文件传递给linker。

`GPUs are named sm_xy, where x denotes the GPU generation number, and y the version in that generation.`



![这里写图片描述](E:\knowledges\assets\SouthEast)



常用编译选项

- -c(--complie)最常使用，编译每个.c/.cc/.cpp/.cxx/.cu输入文件为object文件
- -cuda 编译全部.cu生成.cu.c
- -o 指定输出文件的位置和名称
- -dryrun 不执行nvcc产生的编译命令而只是列出它们
- -v: 列出nvcc产生的编译命令，不影响其执行
- -keep: 保留各步骤产生的中间文件
- -g 表示将CPU代码(host)编译成可调式版本
- -G 表示将GPU代码(kernel)编译成可调式版本
- –link 编译并链接所有的输入文件
- –run 编译链接文件后直接执行
- -m 指定32位还是64位机器
- –arch 指定虚拟计算架构，即nvcc编译到PTX阶段的目标架构
- -code 指定真实sm架构，即nvcc生成目标代码的目标GPU，如果没有指定缺省值就是-arch选项的值

`nvcc`可以保证编译出程序的`application compatibility`，但不能保证`binary compatibility`。在编译过程中，第一阶段产生`virtual GPU architecture code`，即`PTX`；第二阶段才编译出在真实`GPU`上运行的代码。因此真实的`GPU`必须实现了`virtual GPU`所要求的功能。因此，`From this it follows that the virtual architecture should always be chosen as low as possible, thereby maximizing the actual GPUs to run on. The real architecture should be chosen as high as possible (assuming that this always generates better code), but this is only possible with knowledge of the actual GPUs on which the application is expected to run.`。

![这里写图片描述](https://img-blog.csdn.net/20170518124856569?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTkzNDg4NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![img](http://hi.csdn.net/attachment/201106/7/0_1307423992q1er.gif)

参考

https://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/index.html#nvcc-examples

