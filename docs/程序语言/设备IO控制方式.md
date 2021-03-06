现代计算机系统中总是配备有各种外部设备，他们都在CPU控制下进行工作。CPU对外部设备的控制方式主要有以下四种 :

- 轮询方式
- 中断处理方式
- DMA方式
- 通道方式

## 轮询方式

早期的计算机系统中，由于无中断机构，处理机对I/O设备的控制采取程序直接控制方式，或称为忙--等待方式，即在处理器向控制器发出一条I/O指令启动输入设备输入数据时，要同时把状态寄存器中的忙/闲标志busy置为1，然后便不断地循环测试busy。当busy=1时，表示输入设备尚未输完一个字（符），处理器继续对该标志进行测试，直至busy=0，表明输入设备已将输入数据送入控制器的数据寄存器中。于是处理器将数据寄存器中的数据取出，送入内存指定单元中，这样便完成了一个字/符的I/O。接着再去启动读下一个数据，并置busy=1。在程序I/O方式中，由于CPU的高速性和I/O设备的低速性，致使CPU的绝大部分时间都处于等待I/O设备完成数据I/O的循环测试中，造成对CPU的极大浪费。在该方式中，CPU之所以要不断地测试I/O设备的状态，就是因为在CPU中无中断机构，使I/O设备无法向CPU报告它已完成了一个字符的输入操作。这在单片机编程中有时会用到，在现代计算机系统中几乎用不到了。

## 中断处理方式

在现代的计算机系统中，都引入了中断机构，所以对I/O设备的控制都广泛采用中断驱动（Interrupt Driven）方式，即当某进程要启动某个I/O设备工作时，便由CPU向相应的设备控制器发出一条I/O命令，然后立即返回继续执行原来的任务。设备控制器于是按照该命令的要求去控制指定的I/O设备。此时，CPU与I/O设备并行操作。例如，在输入时，当设备控制器收到CPU发来的读命令后，便去控制相应的输入设备读数据。一旦数据进入数据寄存器(在设备控制器中)，设备控制器便通过控制线向CPU发送一中断信号，由CPU检查输入过程是否出错。若无错，便向控制器发送取走数据的信号，然后再通过控制器及数据线将数据写入内存指定单元中。在I/O设备输入每个数据的过程中，由于无需CPU干预，因而可使CPU与I/O设备并行工作。仅当输完一个数据时，才需CPU花费极短的时间去做些中断处理。可见，这样可使CPU和I/O设备都处于忙碌状态，从而提高了整个系统的资源利用率及吞吐量。

## DMA方式

虽然中断驱动I/O比程序I/O方式更有效，但须注意，它仍是以字节为单位进行I/O的，每当完成一个字节的I/O时，控制器便要向CPU请求一次中断。换言之，采用中断驱动I/O方式时的CPU是以字节为单位进行干预的。如果将这种方式用于块设备的I/O，显然是极其低效的。例如，为了从磁盘中读出1KB的数据块，需要中断CPU 1K次。为了进一步减少CPU对I/O的干预而引入了DMA访问方式，该方式的特点是：

- 数据传输的基本单位是数据块，即在CPU与I/O设备之间，每次传送至少一个数据块；
- 所传送的数据是从设备直接送入内存的，或者相反，即只能单向传输。
- 仅在传送一个或多个数据块的开始和结束时，才需CPU干预，整块数据的传送是在DMA控制器的控制下完成的。

 ![img](../../assets/20160707153707094) 

计算机硬件上使用DMA来访问磁盘等IO，也就是请求发出后，CPU就不再管了，直到DMA处理器完成任务，再通过中断告诉CPU完成了。所以，单独的一个IO时间，对CPU的占用是很少的，阻塞了就更不会占用CPU了，因为程序都不继续运行了，CPU时间交给其它线程和进程了。虽然IO不会占用大量的CPU时间，但是非常频繁的IO还是会非常浪费CPU时间的，所以面对大量IO的任务，有时候是需要算法来合并IO，或者通过cache来缓解IO压力的。

## 通道方式

通道是独立于CPU的、专门负责数据的输入输出传输工作的处理器，能执行指令，只不过通道执行的指令是与外部设备相关的指令。它对外部设备实统一管理，代替CPU对I/O操作进行控制，从而使I/O操作可以与CPU并行工作。与DMA控制方式相比，通道所需要的CPU控制更少，一个通道可以控制多个设备，并且能够一次进行多个不连续的数据块的存取交换，从而大大提高了计算机系统效率。

简而言之，通道方式就是通过一个比DMA更高级的硬件芯片，可以负责主存与I/O设备数据交换。

## 参考

- https://blog.csdn.net/Echo_Ana/article/details/53165244
- https://baike.baidu.com/item/I/O%20%E6%8E%A7%E5%88%B6%E6%96%B9%E5%BC%8F
- https://blog.csdn.net/dongyanxia1000/article/details/51802583
- https://www.zhihu.com/question/27734728