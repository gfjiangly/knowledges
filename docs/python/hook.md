hook，又成钩子，在C/C++中一般叫做回调函数。

钩子是从功能角度描述这种编程模式，回调则是从函数调用时间角度描述的。

通常理解的hook是在一个已有的方法上加入一些钩子，使得在该方法执行前或执行后另在做一些额外的处理。

如我们熟知的windows系统消息响应事件，鼠标点击对程序产生的影响是有程序自己决定的，但是程序的执行是受制于框架（windows系统），框架提供了一些通用的流程执行，但是往往框架或流程在设计时无法完全预料到以后的使用会有什么新需求，或者有些行为只有在运行时才能确定的。这就产生了回调的需求，即用户提供需求，框架负责执行，流程先于具体需求，当触发或者满足某种条件时，执行Hook函数。hook函数的数据也是由用户自己提供的，框架只负责流程执行，这样框架的通用性就能大大提高。



我们看看具体的设计实例：mmcv库

Run类负责流程执行（包含由用户提供的数据）；

Hook类是所有hook类的父类，规定了具体的调用名称，子类需要具体实现自己的需求，类似于windows定义的一系列响应事件，只不过这里是按流程顺序主动调用的，不存在不同线程的hook调用，因此比windows系统的hook简单许多。如before_train_epoch方法即在Run的train中每个epoch执行前循环执行用户注册的所有hook，用户hook是放在一个有序列表中，按优先级排列，优先级高的在前，先得到执行，优先级也是由用户确定的，用户权力很大。

```python
def call_hook(self, fn_name):
    for hook in self._hooks:
        getattr(hook, fn_name)(self)
```

用户仅仅需要实现自己所需要的hook，不需要的会调用到父类Hook中响应的方法，实际上父类的hook什么也没做，如OptimizerHook

```python
class OptimizerHook(Hook):

    def __init__(self, grad_clip=None):
        self.grad_clip = grad_clip

    def clip_grads(self, params):
        clip_grad.clip_grad_norm_(
            filter(lambda p: p.requires_grad, params), **self.grad_clip)

    def after_train_iter(self, runner):
        runner.optimizer.zero_grad()
        runner.outputs['loss'].backward()
        if self.grad_clip is not None:
            self.clip_grads(runner.model.parameters())
        runner.optimizer.step()
```

仅仅实现了after_train_iter。数据在runner中，也是用户自己放进去的。所以可以看到，一个hook仅仅需要两个元素，一个是执行者，这里是runner，另外一个是执行时间（触发条件），这里是after_train_iter。

这里hook的注册过程比较简单，因为触发是按流程顺序主动调用的，因此仅需要按优先级插入到有序列表中即可，

```python
def register_hook(self, hook, priority='NORMAL'):
    """Register a hook into the hook list.

    Args:
        hook (:obj:`Hook`): The hook to be registered.
        priority (int or str or :obj:`Priority`): Hook priority.
            Lower value means higher priority.
    """
    assert isinstance(hook, Hook)
    if hasattr(hook, 'priority'):
        raise ValueError('"priority" is a reserved attribute for hooks')
    priority = get_priority(priority)
    hook.priority = priority
    # insert the hook to a sorted list
    inserted = False
    for i in range(len(self._hooks) - 1, -1, -1):
        if priority >= self._hooks[i].priority:
            self._hooks.insert(i + 1, hook)
            inserted = True
            break
    if not inserted:
        self._hooks.insert(0, hook)
```

如果有一个hook需要在两个不同时机执行两个需求，如在before_train_epoch和after_train_epoch，但是恰巧这两个需求的优先级不同，这个时候建议写成两个hook，每个hook只负责做一件事，这也是编程中一般原则吧。

如果编写过windows窗体程序（如MFC），hook应该很容易理解，因为在MFC中回调太普遍了，这也是入门难（或者说难深入底层）的原因，框架默默帮你做了很多事，导致新入局者看不到全局。

这种Google风格docstring，我也是比较喜欢的，层次清楚，无多余字符，符合它本来的目的。可能是看惯了，统一就好。



BTW，其实这个知识不应该写在python目录下的，但是如果想到一个知识就按一般化分类，最后可能发现分类太多，知识缺少具体场景依托。因为以后的文章可能位置比较随性~