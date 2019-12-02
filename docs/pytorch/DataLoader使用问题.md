数据加载过程程序出错，报RuntimeError错误

```bash
RuntimeError: DataLoader worker (pid 17406) exited unexpectedly with exit code 1. Details are lost due to multiprocessing
```

上面这个错误提示可真够迷惑人的，看起来好像是线程问题，实际上和线程一点关系没有，就是`__getitem__`代码写得有问题而已，抛出了异常没有捕捉，到PyTorch层次只显示RuntimeError。

