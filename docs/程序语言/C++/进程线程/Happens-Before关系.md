> 翻译自：https://preshing.com/20130702/the-happens-before-relation/

*Happens-before*是现代计算机科学术语，有助于描述C ++ 11，Java，Go甚至LLVM之后的[软件内存模型](http://preshing.com/20120930/weak-vs-strong-memory-models)。

您会在上述每种语言的规范中找到*happens-before*关系的定义。便利地，这些规范中给出的定义基本相同，尽管每个规范都有不同的表达方式。粗略地说，通用定义可以表述为：

> 令A和B代表多线程进程执行的操作。如果A ***happens-before*** B ，则A的内存**效果**对于B在执行B之前实际上是可见的。

当您为内存重排列给`lock-free`编程带来的复杂性思考各种解决方法时，A ***happens-before*** B是一种值得的保证。有多重方式可以获得此保证，尽管一种编程语言与另一种编程语言略有不同，但所有语言在处理器级别都必须依赖相同的机制。

无论使用哪种编程语言，它们都有一个共同点：如果操作A和操作B由同一线程执行，并且A的语句按程序顺序位于B的语句之前，则A *happens-before* B 。这基本上是我[在较早的文章中提到](http://preshing.com/20120625/memory-ordering-at-compile-time)的“内存排序的基本规则”的形式化表达。

```cpp
int A, B;

void foo()
{
    // This store to A ...
    A = 5;

    // ... effectively becomes visible before the following loads. Duh!
    B = A * A;
}
```

这不是实现*happens-before*关系的唯一方法。C ++ 11标准指出，除其他方法外，您还可以使用[Acquisition和Release语义](http://preshing.com/20120913/acquire-and-release-semantics)在不同线程的操作之间实现它。我将在下一篇有关[*synchronizes-with*](http://preshing.com/20130823/the-synchronizes-with-relation)文章中进一步讨论。

我非常确定，这种关系的名称可能会导致某些混乱。值得立即梳理：在上述定义下，*happens-before*关系与A在B之前实际发生的情况不同！尤其是，

1. A *happens-before* B 并不意味着A发生在B之前。
2. A发生在B之前并不意味着A *happens-before* B。

这些陈述可能看起来很矛盾，但事实并非如此。我将在以下各节中尝试解释它们。请记住，*happens-before*是一系列语言规范定义的操作之间的正式关系；它独立于时间的概念而存在。这与我们通常所说的“ A发生在B之前”不同。及时参考实际事件的顺序。在整个这篇文章中，我一直小心翼翼地总是用前一个术语*happens-before*，以区别于后者。

## *Happens-Before*并不意味着在之前发生

这是一个具有*happens-before*关系但没有实际按顺序发生的操作示例。以下代码执行（1）存储`A`，然后执行（2）存储`B`。根据程序顺序的规则，（1）*happens-before*（2）。

```cpp
int A = 0;
int B = 0;

void foo()
{
    A = B + 1;              // (1)
    B = 1;                  // (2)
}
```

然而，如果我们使用GCC用-O2编译此代码，则编译器将执行一些[指令重新排序](http://preshing.com/20120625/memory-ordering-at-compile-time)。结果，当我们在调试器中的反汇编级别逐步浏览生成的代码时，我们清楚地看到在第二条机器指令之后，存储区to `B`已完成，但是存储区to `A`还没有完成。换句话说，（1）实际上不会在（2）之前发生！

![img](assets/Happens-Before%E5%85%B3%E7%B3%BB/foo-debugger.png)

*happens-before*关系被破坏？让我们来看看。根据定义，在执行（2）之前，必须有效地看到（1）的记忆效果。换句话说，`A`的存储必须有机会影响 `B`的存储。

不过，在这种情况下，`A`的存储实际上不会影响 `B`的存储。即使（1）的效果是可见的，（2）仍会表现出相同的效果，这实际上与（1）的效果是可见的相同。因此，这不算是违反*happens-before*规则。我承认，这种解释有点儿杂乱无章，但我相当有信心，在所有这些语言规范中，它与*happens-before*的含义是一致的。

## 在之前发生并不意味着*Happens-Before*

这是一些操作的示例，这些操作显然按特定顺序*发生*而没有构成*先发生后*关系。在以下代码中，假设一个线程调用`publishMessage`，而另一个线程调用`consumeMessage`。由于我们要同时操作共享变量，简单起见，假设普通的`int`的加载和存储是[atomic](http://preshing.com/20130618/atomic-vs-non-atomic-operations)。由于程序顺序，（1）和（2）之间有一个*happens-before*的关系，（3）和（4）之间有另一个的*happens-before*关系。

```cpp
int isReady = 0;
int answer = 0;

void publishMessage()
{
    answer = 42;                      // (1)
    isReady = 1;                      // (2)
}

void consumeMessage()
{
    if (isReady)                      // (3) <-- Let's suppose this line reads 1
        printf("%d\n", answer);       // (4)
}
```

此外，让我们假设在运行时，标记为（3）的行最终读取1，该值的存储发生在另一个线程的第（2）行中。在这种情况下，我们知道（2）必须在（3）之前发生。但这并不意味着（2）和（3）之间存在*happens-before*关系！

*happens-before*关系仅仅存在于语言标准说它存在的地方。由于这些都是普通的加载和存储，因此C ++ 11标准没有规则在（2）和（3）之间引入*happens-before*关系，即使（3）读取了（2）编写的值也是如此。

此外，因为（2）和（3）之间没有*happens-before*的关系，（1）和（4）之间也无*happens-before*的关系。因此，由于编译器指令的重新排序或处理器本身上的内存重新排序，可以对（1）和（4）的内存交互进行重新排序，即使（3）读取1，（4）最终还是打印“ 0” 。

这篇文章实际上并没有显示任何新内容。我们已经知道执行无锁代码时[可以重新排序内存交互](http://preshing.com/20121019/this-is-why-they-call-it-a-weakly-ordered-cpu)。我们已经简单地检查了C ++ 11，Java，Go和LLVM中使用的一个术语，以正式指定在这些语言中可以观察到内存重新排序的情况。即使是我几周前发布的[Mintomic](http://mintomic.github.io/)库，也要依靠*happens-before*的保证，因为它模仿了特定C ++ 11原子库函数的行为。

我相信*happens-before*关系与实际操作顺序之间存在歧义，这是使低层次[无锁编程](http://preshing.com/20120612/an-introduction-to-lock-free-programming)如此棘手的部分原因。如果没有别的，这篇文章应该证明，*happens-before*是一个有用的保证，在线程之间并不轻量。我将在下一篇文章中进一步介绍。