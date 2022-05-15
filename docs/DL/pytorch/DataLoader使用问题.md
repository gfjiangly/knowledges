数据加载过程程序出错，报RuntimeError错误

```bash
RuntimeError: DataLoader worker (pid 17406) exited unexpectedly with exit code 1. Details are lost due to multiprocessing
```

上面这个错误提示可真够迷惑人的，看起来好像是线程问题，实际上和线程一点关系没有，就是`__getitem__`代码写得有问题而已，抛出了异常没有捕捉，到PyTorch层次只显示RuntimeError。



参数：

1. shuffle决定使用RandomSampler还是SequentialSampler，前提是没有自己传输sampler，否则shuffle就由自己传入的sampler实现；
2. batch_size=None时，失能BatchSampler，由dataset自己负责产生一个batch数据，自己经过collate_fn类似处理，DataLoader不对dataset出来的数据做任何处理。这样通常更高效：https://pytorch.org/docs/stable/data.html#disable-automatic-batching

