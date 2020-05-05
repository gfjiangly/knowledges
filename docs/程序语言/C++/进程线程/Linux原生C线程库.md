C++程序中可以调用操作系统提供的线程库。Linux系统提供了<pthread.h>，用C语言写的原生线程库，没有抽象封装。

## pthread库函数使用

### 创建线程

```cpp
#include <pthread.h>
/* 
 * pid : 线程id，传入pthread_t类型的指针，函数返回时会返回线程id
 * attr : 线程属性
 * func : 线程调用的函数
 * arg : 给函数传入的参数
 */
int pthread_create(pthread_t* pid, const pthread_attr_t* attr, void*(*func)(void*), void* arg);
```

参数从哪里来？

`pthread_t`是线程库提供的一种类型，用以标志这个线程，pid指向的对象的信息由create函数复制写入，如：

```cpp
#include <unistd.h>
#include <pthread.h>
#include <sys/types.h>
#include <iostream>

long long int n = 0;  // 全局变量多个线程可见

void *thread_func(void* arg)
{
    for(int i = 0; i < 10000; ++i)
        ++n;  // 自增非原子操作，原子操作的实现是需要代价的
    ::pthread_exit(nullptr);
}

/*
	创建10个线程对进程全局变量n做加法，每个线程加10000次
*/
int main()
{
    for(int i = 0; i < 10; ++i)
    {
        pthread_t pid;
        ::pthread_create(&pid, nullptr, thread_func, nullptr); 
        ::pthread_detach(pid);
    }
    ::sleep(1);  // 等待所有线程正常退出
    std::cout << n << std::endl;
    return 0;
}
```

`pthread_detach`函数用于主动分离线程，分离的线程运行完毕后其资源(主要是独立的栈空间)的回收由操作系统负责。未分离的线程需要调用`pthread_join`手动释放其资源，如果调用时目标线程还没有运行结束，那么调用方(主线程)会被阻塞。

当然最后这个结果绝不可能是100000，要想保证正确性，需要使用锁。

### 结束当前线程

pthread_exit