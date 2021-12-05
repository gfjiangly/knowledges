![img](https://img2018.cnblogs.com/blog/1093303/201809/1093303-20180919122932879-1946399786.png)

GPU面对的则是类型高度统一的、相互无依赖的大规模数据和不需要被打断的纯净的计算环境，GPU有非常多核心（费米架构就有512核），虽然其核心的能力远没有CPU的核心强，但是胜在多，
 在处理简单计算任务时呈现出“人多力量大”的优势，这就是并行计算的魅力。

整理一下两者特点就是：

- CPU：擅长流程控制和逻辑处理，不规则数据结构，不可预测存储结构，单线程程序，分支密集型算法
- GPU：擅长数据并行计算，规则数据结构，可预测存储模式

![img](../../assets/1093303-20180919123018799-1605248744.png)

CUDA的线程模型从小往大来总结就是：

1. Thread：线程，并行的基本单位，轻量化
2. Thread Block：线程块，互相合作的线程组，线程块有如下几个特点：

	- 允许彼此同步
	- 可以通过共享内存快速交换数据
	- 以1维、2维或3维组织

3. Grid：一组线程块

	- 以1维、2维组织
	- 共享全局内存



Kernel：在GPU上执行的核心程序，这个kernel函数是运行在某个Grid上的。

	- One kernel <-> One Grid

 kernel所启动的所有线程称为一个网格（grid），同一个网格上的线程共享相同的全局内存空间


每一个block和每个thread都有自己的ID，我们通过相应的索引找到相应的线程和线程块。

	- threadIdx，blockIdx
	- Block ID: 1D or 2D
	- Thread ID: 1D, 2D or 3D
SP和SM

- SP：最基本的处理单元，streaming processor，也称为CUDA core。最后具体的指令和任务都是在SP上处理的。GPU进行并行计算，也就是很多个SP同时做处理。
- SM：多个SP加上其他的一些资源组成一个streaming multiprocessor。也叫GPU大核，其他资源如：warp scheduler，register，shared memory等。SM可以看做GPU的心脏（对比CPU核心），register和shared memory是SM的稀缺资源。CUDA将这些资源分配给所有驻留在SM中的threads。因此，这些有限的资源就使每个SM中active warps有非常严格的限制，也就限制了并行能力。

需要指出，每个SM包含的SP数量依据GPU架构而不同，Fermi架构GF100是32个，GF10X是48个，Kepler架构都是192个，Maxwell都是128个。SP是线程执行的硬件单位，SM中包含多个SP，一个GPU可以有多个SM（比如16个），最终一个GPU可能包含有上千个SP。这么多核心“同时运行”，速度可想而知，这个引号只是想表明实际上，软件逻辑上是所有SP是并行的，但是物理上并不是所有SP都能同时执行计算。比如我们只有8个SM却有1024个线程块需要调度处理，因此有些会处于挂起，就绪等其他状态，这有关GPU的线程调度。

![img](https://img2018.cnblogs.com/blog/1093303/201809/1093303-20180919123034967-1110899742.png)

一个kernel一次只能在一个GPU上执行

block是软件概念，一个block只会由一个sm调度，程序员在开发时，通过设定block的属性，告诉GPU硬件，我有多少个线程，线程怎么组织。而具体怎么调度由sm的warps scheduler负责，block一旦被分配好SM，该block就会一直驻留在该SM中，直到执行结束。一个SM可以同时拥有多个blocks，但需要序列执行。下图显示了GPU内部的硬件架构：

![img](https://img2018.cnblogs.com/blog/1093303/201809/1093303-20180919123048002-1383369419.png)



## CUDA内存模型

CUDA中的内存模型分为以下几个层次：

- 每个线程都用自己的registers（寄存器）
- 每个线程都有自己的local memory（局部内存）
- 每个线程块内都有自己的shared memory（共享内存），所有线程块内的所有线程共享这段内存资源
- 每个grid都有自己的global memory（全局内存），不同线程块的线程都可使用
- 每个grid都有自己的constant memory（常量内存）和texture memory（纹理内存），），不同线程块的线程都可使用

 

线程访问这几类存储器的速度是register > local memory > shared memory > global memory



下面这幅图表示就是这些内存在计算机架构中的所在层次。

![img](https://img2018.cnblogs.com/blog/1093303/201809/1093303-20180919123101933-1651940595.png)