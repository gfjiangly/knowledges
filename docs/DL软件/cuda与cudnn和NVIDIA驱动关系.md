CUDA的官方文档介绍：a general purpose parallel computing platform and programming model that leverages the parallel compute engine in NVIDIA GPUs to solve many complex computational problems in a more efficient way than on a CPU. 

换句话说CUDA是NVIDIA推出的用于自家GPU的并行计算框架，也就是说CUDA只能在NVIDIA的GPU上运行，而且只有当要解决的计算问题是可以大量并行计算的时候才能发挥CUDA的作用。

在 CUDA 的架构下，一个程序分为两个部分：host 端和 device 端。Host 端是指在 CPU 上执行的部份，而 device 端则是在显示芯片上执行的部份。Device 端的程序又称为 “kernel”。通常 host 端程序会将数据准备好后，复制到显卡的内存中，再由显示芯片执行 device 端程序，完成后再由 host 端程序将结果从显卡的内存中取回。

在 CUDA 架构下，显示芯片执行时的最小单位是**thread**。数个 thread 可以组成一个**block**。一个 block 中的 thread 能存取同一块共享的内存，而且可以快速进行同步的动作。每一个 block 所能包含的 thread 数目是有限的。不过，执行相同程序的 block可以组成**grid**。不同 block 中的 thread 无法存取同一个共享的内存，因此无法直接互通或进行同步。因此，不同 block 中的 thread 能合作的程度是比较低的。不过，利用这个模式，可以让程序不用担心显示芯片实际上能同时执行的 thread 数目限制。例如，一个具有很少量执行单元的显示芯片，可能会把各个 block 中的 thread 顺序执行，而非同时执行。不同的 grid 则可以执行不同的程序（即 kernel）。

![cuda执行单元](https://img-blog.csdn.net/20170817202814746?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxNDM4MDE2NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



**cuDNN**（CUDA Deep Neural Network library）：是NVIDIA打造的针对深度神经网络的加速库，是一个用于深层神经网络的GPU加速库。如果你要用GPU训练模型，cuDNN不是必须的，但是一般会采用这个加速库。

能够很大程度的把加载到显卡上的网络层数据进行**优化计算**。cudnn是在cuda的基础上进行神经网络计算优化。