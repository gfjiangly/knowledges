一般用法

```python
from joblib import Parallel, delayed


def task(data_num):
    pass

if __name__ == '__main__':
    # res是一个结果list，里面的数据顺序与for in的输入顺序一致
    res = Parallel(n_jobs=2)(delayed(task)(i) for i in range(10))
    
    # 使用多线程
	res = Parallel(n_jobs=2, backend="threading")(delayed(task)(i) for i in range(10))
```

 `Parallel` 对象会创建一个进程池，以便在多进程中执行每一个列表项。函数 `delayed` 是一个创建元组 `(function, args, kwargs)` 的简单技巧。 

 默认情况下，当`n_jobs != 1`时，joblib使用Python标准库的多进程模块(multiprocessing)来创建真实的Python工作进程 。传递给Parallel调用的参数被序列化，并且会在每一个工作进程中重新创建。 