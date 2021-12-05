> 翻译自：https://preshing.com/20150402/you-can-do-any-kind-of-atomic-read-modify-write-operation/
>
> 略有删改

原子读取-修改-写入操作（或称为“ RMW”）比[原子加载和存储](http://preshing.com/20130618/atomic-vs-non-atomic-operations)更为复杂。它们使您可以从共享内存中的变量读取数据，并同时在该变量中写入一个不同的值。在C ++ 11原子库中，以下所有功能均执行RMW：

```
std::atomic<>::fetch_add()
std::atomic<>::fetch_sub()
std::atomic<>::fetch_and()
std::atomic<>::fetch_or()
std::atomic<>::fetch_xor()
std::atomic<>::exchange()
std::atomic<>::compare_exchange_strong()
std::atomic<>::compare_exchange_weak()
```

`fetch_add`例如，从一个共享变量中读取，为其添加另一个值，然后将结果写回–所有这些都是一个不可分割的步骤。您可以使用互斥锁完成相同的操作，但是基于互斥锁的版本并不是无锁的。另一方面，RMW操作被设计为无锁。他们将尽可能利用无锁CPU指令，例如ARMv7上的`ldrex`/ `strex`。

新手程序员可能会看上面的函数列表，然后问：“为什么C ++ 11提供的RMW操作这么少？为什么会有原子的`fetch_add`，但没有原子的`fetch_multiply`，没有`fetch_divide`又没有`fetch_shift_left`？” 有两个原因：

1. 因为在实践中几乎不需要这些RMW操作。尽量不要对RMW的使用方式有错误的印象。您不能通过采用单线程算法并将每个步骤转换为RMW来编写安全的多线程代码。
2. 因为如果确实需要这些操作，则可以自己轻松实现它们。如标题所示，您可以进行任何类型的RMW操作！

## 比较交换：所有RMW的母亲

在C ++ 11中所有可用的RMW操作中，唯一必不可少的是`compare_exchange_weak`。其他每个RMW操作都可以使用该操作来实现。至少需要两个参数：

```cpp
shared.compare_exchange_weak(T& expected, T desired, ...);
```

该函数尝试将`desired`值存储到`shared`，但仅当当前值`shared`匹配时才存储`expected`。`true`返回成功。如果失败，则将`shared`的当前值加载回`expected`中，尽管其名称为in/out参数。这称为**比较交换**操作，所有这些操作都在一个原子的不可分割的步骤中发生。

![img](assets/%E5%8E%9F%E5%AD%90%E8%AF%BB-%E4%BF%AE%E6%94%B9-%E5%86%99%E6%93%8D%E4%BD%9C(RMW)/compare-exchange.png)

因此，假设您确实需要原子`fetch_multiply`操作，尽管我无法想象为什么。这是实现它的一种方法：

```cpp
uint32_t fetch_multiply(std::atomic<uint32_t>& shared, uint32_t multiplier)
{
    uint32_t oldValue = shared.load();
    // PS: 这种情况可以使用shared.compare_exchange_strong(oldValue, oldValue * multiplier);
    while (!shared.compare_exchange_weak(oldValue, oldValue * multiplier))
    {
    }
    return oldValue;
}
```

这称为比较交换循环或**CAS循环**。该函数反复尝试`oldValue`与之交换`oldValue * multiplier`直到成功。如果在其他线程中未发生并发修改，`compare_exchange_weak`则通常会在第一次尝试时成功。另一方面，如果`shared`同时被另一个线程修改，则它的值完全有可能在对`load`调用和`compare_exchange_weak`调用之间改变，从而导致比较和交换操作失败。在这种情况下，`oldValue`将使用的最新值进行更新`shared`，然后循环将重试。

![img](assets/%E5%8E%9F%E5%AD%90%E8%AF%BB-%E4%BF%AE%E6%94%B9-%E5%86%99%E6%93%8D%E4%BD%9C(RMW)/fetch-multiply-timeline.png)

上述实现的`fetch_multiply`是原子操作也是无锁的。即使CAS循环可能需要不确定的尝试次数，它也是原子的，因为当循环最终修改`shared`时，它会自动进行。它是无锁的，因为如果CAS循环的单个迭代失败，通常是因为其他线程`shared`成功修改了。最后一条陈述基于这样的假设，即`compare_exchange_weak`实际上可以编译为无锁机器代码，更多内容将在下面进行。它还忽略了在某些平台上`compare_exchange_weak`可能[会虚假失败](http://en.cppreference.com/w/cpp/atomic/atomic/compare_exchange)的事实，但这是罕见的事件。

## 您可以将多个步骤组合成一个RMW

`fetch_multiply`只需将的值替换为`shared`相同值的倍数即可。如果我们想执行更复杂的RMW怎么办？我们还可以使操作原子化*和无*锁吗？我们当然可以。为此提供一个令人费解的示例，下面的函数加载一个共享变量，如果为奇数，则将值减小；如果为偶数，则将其除以一半；仅在大于或等于10的情况下，才将结果存储回去。所有这一切都在单个原子的、无锁操作中：

```cpp
uint32_t atomicDecrementOrHalveWithLimit(std::atomic<uint32_t>& shared)
{
    uint32_t oldValue = shared.load();
    uint32_t newValue;
    do
    {
        if (oldValue % 2 == 1)
            newValue = oldValue - 1;
        else
            newValue = oldValue / 2;
        if (newValue < 10)
            break;
    }
    while (!shared.compare_exchange_weak(oldValue, newValue));
    return oldValue;
}
```

这与以前的想法相同：如果`compare_exchange_weak`失败（通常是由于另一个线程执行的修改而导致的失败）`oldValue`被更新了最新的值，然后循环再次尝试。如果在任何尝试中发现`newValue`小于10，则CAS循环会提前终止，从而有效地将RMW操作变为无操作。

关键是您可以将任何内容放入CAS循环中。将CAS循环的主体视为关键部分。通常，我们使用互斥锁来保护关键部分。通过CAS循环，我们只需重试整个事务，直到成功为止。

这显然是一个综合的例子。[`AutoResetEvent`](https://github.com/preshing/cpp11-on-multicore/blob/master/common/autoresetevent.h)在我[较早的帖子中有关信号量](http://preshing.com/20150316/semaphores-are-surprisingly-versatile)的课程中可以看到一个更实际的示例。它使用具有多个步骤的CAS循环以原子方式将共享变量增加到最大限制1。

## 您可以将多个变量组合到一个RMW中

到目前为止，我们仅研究了对单个共享变量执行原子操作的示例。如果我们想对多个变量执行原子操作怎么办？通常，我们会使用互斥锁来保护这些变量：

```cpp
std::mutex mutex;
uint32_t x;
uint32_t y;

void atomicFibonacciStep()
{
    std::lock_guard<std::mutex> lock(mutex);
    int t = y;
    y = x + y;
    x = t;
}
```

这种基于互斥锁的方法是原子的，但显然不是无锁的。这[可能已经足够好了](http://preshing.com/20111118/locks-arent-slow-lock-contention-is)，但是为了说明起见，让我们继续将其转换为CAS循环，就像其他示例一样。`std::atomic<>`是一个模板，因此我们实际上可以将两个共享变量都打包到一个`struct`中，并像以前一样应用相同的模式：

```cpp
struct Terms
{
    uint32_t x;
    uint32_t y;
};

std::atomic<Terms> terms;

void atomicFibonacciStep()
{
    Terms oldTerms = terms.load();
    Terms newTerms;
    do
    {
        newTerms.x = oldTerms.y;
        newTerms.y = oldTerms.x + oldTerms.y;
    }
    while (!terms.compare_exchange_weak(oldTerms, newTerms));
}
```

此操作是否无锁？现在，我们正在冒险进入骰子领域。正如我在开始时所写的那样，C ++ 11原子操作的设计“尽可能”利用了无锁CPU指令，这是一个宽松的定义。在这种情况下，我们包装`std::atomic<>`了一个结构`Terms`。让我们看看GCC 4.9.2如何为x64编译它：

![img](assets/%E5%8E%9F%E5%AD%90%E8%AF%BB-%E4%BF%AE%E6%94%B9-%E5%86%99%E6%93%8D%E4%BD%9C(RMW)/atomic-terms-rmw.png)

我们很幸运。编译器非常聪明，它可以看到`Terms`适合单个64位寄存器，并`lock cmpxchg`使用来实现`compare_exchange_weak`。编译后的代码是无锁的。

这就提出了一个有趣的观点：通常，C ++ 11标准*不能*保证原子操作是无锁的。实在有太多的CPU体系结构无法支持，并且有太多的方法来专门化`std::atomic<>`模板。您需要[检查编译器](http://en.cppreference.com/w/cpp/atomic/atomic/is_lock_free)以确保绝对正确。但是，实际上，当满足以下所有条件时，可以假定原子操作是无锁的，这是非常安全的：

1. 编译器是MSVC，GCC或Clang的最新版本。
2. 目标处理器是x86，x64或ARMv7（可能还有其他处理器）。
3. 的原子类型是`std::atomic`，`std::atomic`或`std::atomic`对于一些类型`T`。

作为个人喜好，我喜欢在第三点上挂上帽子，并限制自己使用显式整数或指针类型的特化`std::atomic<>`模板。我在上一篇文章中描述的[安全位域技术](http://preshing.com/20150324/safe-bitfields-in-cpp)为我们提供了一种使用显式整数特化`std::atomic`来重写上述函数的便捷方法：

```cpp
BEGIN_BITFIELD_TYPE(Terms, uint64_t)
    ADD_BITFIELD_MEMBER(x, 0, 32)
    ADD_BITFIELD_MEMBER(y, 32, 32)
END_BITFIELD_TYPE()

std::atomic<uint64_t> terms;

void atomicFibonacciStep()
{
    Terms oldTerms = terms.load();
    Terms newTerms;
    do
    {
        newTerms.x = oldTerms.y;
        newTerms.y = (uint32_t) (oldTerms.x + oldTerms.y);
    }
    while (!terms.compare_exchange_weak(oldTerms, newTerms));
}
```

我们将几个值打包到原子位域中的一些实际示例包括：

- 实现带标记的指针作为[ABA问题](http://en.wikipedia.org/wiki/ABA_problem#Tagged_state_reference)的[解决方法](http://en.wikipedia.org/wiki/ABA_problem#Tagged_state_reference)。
- 实现轻量级的读写锁，我在[信号量惊人的通用](http://preshing.com/20150316/semaphores-are-surprisingly-versatile)文章中曾简要介绍过。

通常，只要您有少量数据受互斥锁保护，并且可以将数据完全打包为32位或64位整数类型，则始终可以将基于互斥锁的操作转换为无锁RMW操作，无论这些操作实际上是做什么的！这就是我在《[信号量惊人的通用》](http://preshing.com/20150316/semaphores-are-surprisingly-versatile)一文中采用的原理，以实现一堆轻量级的同步原语。

当然，这种技术并不是C ++ 11原子库所独有的。我仅使用C ++ 11原子，因为它们现在已经广泛使用，并且对编译器的支持非常好。您可以使用公开比较交换函数的任何库来实现自定义RMW操作，例如[Win32](https://msdn.microsoft.com/en-us/library/ttk2z1ws.aspx)，[Mach内核API](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man3/OSAtomicCompareAndSwap32.3.html)，[Linux内核API](http://lxr.free-electrons.com/ident?i=atomic_cmpxchg)，[GCC原子内置](https://gcc.gnu.org/onlinedocs/gcc-4.9.2/gcc/_005f_005fatomic-Builtins.html)[函数](http://lxr.free-electrons.com/ident?i=atomic_cmpxchg)或[Mintomic](http://mintomic.github.io/lock-free/atomics/)。为了简洁起见，在这篇文章中我没有讨论内存排序问题，但是考虑您的原子库所做的保证至关重要。特别是，如果您的自定义RMW操作旨在**在线程之间传递非原子信息**，那么至少应确保与[*与*](http://preshing.com/20130823/the-synchronizes-with-relation)某处[*同步*](http://preshing.com/20130823/the-synchronizes-with-relation)关系。