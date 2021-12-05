## 特性

- 先进后出
- 只有一个出口，不允许遍历

## STL实现

### 基本操作

```C++
stack<int> s;
s.push(x)      // 无返回值，将元素x压栈
s.pop();       // 退栈，无返回值
s.top();       // 取栈顶元素，返回栈顶元素
s.empty();     // 判断栈是否为空，如果是空，返回1，否则返回0
s.size();      // 返回栈中元素的个数
```

### 为什么pop函数不返回值

有人可能想知道为什么`pop()`返回void，而不是value_type。也就是说，为什么必须使用`top()`和`pop()`来检查和删除栈顶元素，而不是将两者合并为一个成员函数？实际上，这种设计是有充分理由的。如果`pop()`返回顶部元素，则必须按值而不是按引用返回：按引用返回将创建一个悬空指针。但是，按值返回效率低下：它涉及至少一个冗余副本构造函数调用。由于`pop()`不可能以高效且正确的方式返回值，因此更明智的做法是不返回任何值并要求用户使用`top()`获取栈顶元素值。

```
One might wonder why pop() returns void, instead of value_type. That is, why must one use top() and pop() to examine and remove the top element, instead of combining the two in a single member function? In fact, there is a good reason for this design. If pop() returned the top element, it would have to return by value rather than by reference: return by reference would create a dangling pointer. Return by value, however, is inefficient: it involves at least one redundant copy constructor call. Since it is impossible for pop() to return a value in such a way as to be both efficient and correct, it is more sensible for it to return no value at all and to require clients to use top() to inspect the value at the top of the stack.
```

值得注意的是，不是所有语言都注意考虑效率，有的语言更看重可用性。Java中pop函数是有返回值的。

### 清空栈

在栈中没有提供清空操作的函数，但是可以使用pop方法间接地实现清空栈。

```C++
while(!s.empty())  s.pop();
```

