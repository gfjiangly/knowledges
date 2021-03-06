每个进程各自有不同的用户地址空间,任何一个进程的全局变量在另一个进程中都看不到，所以进程之间要交换数据必须通过**内核**。

在内核中开辟一块缓冲区,进程A把数据从用户空间拷到内核缓冲区，进程B再从内核缓冲区把数据读走（这实际上是传统Unix的IPC），内核提供的类似这种机制称为进程间通信(**IPC，InterProcess Communication**)。

内核提供了8种进程间通信方式：匿名管道（pipe）、有名管道（named pipe）、高级管道（popen）、消息队列、信号量（semophore ）、信号（sinal）、共享内存、套接字(socket)。

### 共享内存通信

共享内存( shared memory ) ：共享内存就是映射一段能被其他进程所访问的内存（内核揭开面纱），这段共享内存由一个进程创建，但多个进程都可以访问。共享内存是最快的 IPC 方式，它是针对其他进程间通信方式运行效率低而专门设计的。它往往与其他通信机制，如信号量，配合使用，来实现进程间的同步和通信。

仅提供资源，不提供解决竞争的方案。


### 信号量

信号量是一个计数器，可以用来控制多个进程对共享资源的访问。它常作为一种**锁**机制，防止某进程正在访问共享资源时，其他进程也访问该资源。因此，主要作为进程间以及同一进程内不同线程之间的同步手段。

提供了解决竞争的方案。

### 信号

用于通知接收进程某个事件已经发生。除了用于进程间通信外，进程还可以发送信号给进程本身；

仅提供资源。

### 套接字

套接口也是一种进程间通信机制，与其他通信机制不同的是，它可用于不同机器间的进程通信。这是一种客户端/服务器模式，目前用的较多。

![img](assets/%E8%BF%9B%E7%A8%8B%E9%97%B4%E9%80%9A%E4%BF%A1/1281379-2db1deb0115ec4f2.webp)

套接字是支持TCP/IP的网络通信的基本操作单元（封装了TCP/IP协议），属于计算机网络知识。

服务器端进程：创建套接字 -> 监听(等待其它进程连接)，同时也可发送数据 -> 接受连接 -> 处理数据/请求 -> 断开或被迫断开连接

客户端进程：创建套接字 -> 发送连接请求 -> 发送数据/请求，同时也在监听 -> 断开或被迫断开连接

通信双方必须要有地址，否则找不到对方。