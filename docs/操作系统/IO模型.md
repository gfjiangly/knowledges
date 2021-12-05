IO不一定指文件(抽象或具体)到内存，内存到文件。广义的IO包括网络设备。IO的类型有文件IO、网络IO。本文主要讨论网络IO。

网络IO的本质是socket的读取，socket在Linux系统被抽象为流，IO可以理解为对流的操作。对于一次IO访问（以read举例），数据会先被拷贝到操作系统内核的缓冲区中，然后才会从操作系统内核的缓冲区拷贝到应用程序的地址空间。所以说，当一个read操作发生时，它会经历两个阶段：

> 1. 第一阶段：等待数据准备 (Waiting for the data to be ready)。
> 2. 第二阶段：将数据从内核拷贝到进程中 (Copying the data from the kernel to the process)。

对于socket流而言，

> 1. 第一步：通常涉及等待网络上的数据分组到达，然后被复制到内核的某个缓冲区。
> 2. 第二步：把数据从内核缓冲区复制到应用进程缓冲区。

网络应用需要处理的无非就是两大类问题，`网络IO，数据计算`。相对于后者，网络IO的延迟，给应用带来的性能瓶颈大于后者。网络IO的模型大致有如下几种：

> - **同步模型（synchronous IO）**
> - 阻塞IO（bloking IO）
> - 非阻塞IO（non-blocking IO）
> - 多路复用IO（multiplexing IO）
> - 信号驱动式IO（signal-driven IO）
> - **异步IO（asynchronous IO）**

signal-driven IO在实际中并不常用。

## 相关概念

同步：调用端会一直等待服务端响应，直到返回结果。 

异步：调用端发起调用之后不会立刻返回，不会等待服务端响应。服务端通过通知机制或者回调函数来通知客户端。 

阻塞：服务端返回结果（包括数据）之前，客户端线程会被挂起，此时线程不可被CPU调度，CPU调度其它线程执行。**当线程或进程被阻塞，它是不占用CPU资源的。**

非阻塞：在服务端返回前，函数不会阻塞调用端线程，而会立刻返回。

### 文件描述符(FD, File Descriptor)

FD用于描述指向文件的引用的抽象化概念。文件描述符在形式上是一个非负整数。实际上，它是一个索引值，指向内核为每一个进程所维护的该进程打开文件的记录表。当程序打开一个现有文件或者创建一个新文件时，内核向进程返回一个文件描述符。在程序设计中，一些涉及底层的程序编写往往会围绕着文件描述符展开。但是文件描述符这一概念往往只适用于UNIX、Linux这样的操作系统。

### 缓存I/O

缓存IO又被称作标准IO，大多数文件系统的默认IO 操作都是缓存IO。在Linux的缓存IO 机制中，操作系统会将 IO 的数据缓存在文件系统的页缓存（ page cache ）中，也就是说，数据会先被拷贝到操作系统内核的缓冲区中，然后才会从操作系统内核的缓冲区拷贝到应用程序的地址空间。

#### 缓存IO的缺点：

数据在传输过程中需要在应用程序地址空间和内核进行多次数据拷贝操作，这些数据拷贝操作所带来的CPU以及内存开销是非常大的。

## IO模型

### 阻塞IO模型

进程会一直阻塞，直到数据拷贝完成。应用程序调用一个IO函数，导致应用程序阻塞，等待数据准备好。数据准备好后，从内核拷贝到用户空间，IO函数返回成功指示。阻塞IO模型图如下所示：

![blocking-io.jpg](assets/IO%E6%A8%A1%E5%9E%8B/1691ebff3e4ebf27) 

这个一个比较好的行为，不占用CPU资源，但是对于该进程而言需要承担进程切换的代价，属于好人行为。

### 非阻塞IO模型

通过进程反复调用IO函数，**在数据拷贝过程中，进程是阻塞的**。模型图如下所示:

![non-blocking-io](assets/IO%E6%A8%A1%E5%9E%8B/1691ebf25c789918)

看起来很着急。真的很急的情况下可以理解。

### IO复用模型

一个线程可以对多个IO端口进行监听，当socket有读写事件时分发到具体的线程进行处理。主要是select和epoll两个系统调用。epoll是Linux所特有，而select是POSIX所规定，一般操作系统均有实现。模型如下所示：

![io-multiplexing](assets/IO%E6%A8%A1%E5%9E%8B/1691ebf25c7146fa)

### select、poll、epoll区别总结：

![image-20200213162208557](assets/IO%E6%A8%A1%E5%9E%8B/image-20200213162208557.png)

面试喜欢问epoll的底层实现。

```cpp
int epoll_create(int size);
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);
int epoll_wait(int epfd, struct epoll_event *events,int maxevents, int timeout);
```

首先epoll_create创建一个epoll文件描述符，底层同时创建一个**红黑树**，和一个**就绪链表**；红黑树存储所**监控的**文件描述符的节点数据，就绪链表存储**就绪的**文件描述符的节点数据；

epoll_ctl作用是新的描述符，过程是：首先判断红黑树上是否有此文件描述符节点，如果有，则立即返回。如果没有， 则在树干上插入新的节点，并且告知**内核注册回调函数**。当接收到某个文件描述符过来数据时，内核将该节点插入到就绪链表里面。

epoll_wait将会接收到消息，并且将数据拷贝到用户空间，清空链表。epoll_wait清空就绪链表之后会检查该文件描述符是哪一种模式，对于LT模式，节点确实有事件未处理，那么就会把该节点重新放入到刚刚删除掉的且刚准备好的就绪链表，epoll_wait马上返回；如果为ET模式，则不检查节点是否有事件未处理，只会调用一次。

### 信号驱动IO模型

信号驱动式I/O：首先我们允许Socket进行信号驱动IO，并安装一个信号处理函数，进程继续运行并不阻塞。当数据准备好时，进程会收到一个SIGIO信号，可以在信号处理函数中调用I/O操作函数处理数据。过程如下图所示：

![sigio](assets/IO%E6%A8%A1%E5%9E%8B/1691ec1919c6fa00)



### 异步IO模型

相对于同步IO，异步IO不是顺序执行。用户进程进行aio_read系统调用之后，无论内核数据是否准备好，都会直接返回给用户进程，然后用户态进程可以去做别的事情。等到socket数据准备好了，内核直接复制数据给进程，然后从内核向进程发送通知。IO两个阶段，进程都是非阻塞的。异步过程如下图所示：

![aio](assets/IO%E6%A8%A1%E5%9E%8B/1691ec1919b4b16b)



### 五种IO模型比较

IO过程可以分成两个阶段：第一个是系统调用后等待数据阶段，根据是否直接返回分成同步和异步IO，根据是否挂起分成阻塞和非阻塞IO；第二个阶段是数据拷贝阶段，做真正的数据读取或写入操作，除了异步IO外，其余IO模型第二个阶段都是阻塞。定义可以有不同，但两个阶段过程必须理解。

POSIX中对同步和异步IO的定义：

```
A synchronous I/O operation causes the requesting process to be blocked until that I/O operation completes;
An asynchronous I/O operation does not cause the requesting process to be blocked;
```

**两者的区别就在于synchronous IO做”IO operation”的时候会将process阻塞。**按照这个定义，之前所述的blocking IO，non-blocking IO，IO multiplexing都属于synchronous IO。注意到non-blocking IO会一直轮询(polling)，这个过程是没有阻塞的，但是recvfrom阶段blocking IO,non-blocking IO和IO multiplexing都是阻塞的。 而asynchronous IO则不一样，当进程发起IO操作之后，就直接返回再也不理睬了，直到kernel发送一个信号，告诉进程说IO完成。在这整个过程中，进程完全没有被block。


![io-diff](assets/IO%E6%A8%A1%E5%9E%8B/1691ec1919cbe0ac)