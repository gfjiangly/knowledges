DataLoader的功能主要由三个类完成：DataLoader、Dataset、Sample。Dataset、Sample作为参数组合进DataLoader

```python
class DataLoader(object):
    r"""
    Data loader. Combines a dataset and a sampler, and provides
    single- or multi-process iterators over the dataset.

    Arguments:
        dataset (Dataset): dataset from which to load the data.
        batch_size (int, optional): how many samples per batch to load
            (default: 1).
        shuffle (bool, optional): set to ``True`` to have the data reshuffled
            at every epoch (default: False).
        sampler (Sampler, optional): defines the strategy to draw samples from
            the dataset. If specified, ``shuffle`` must be False.
        batch_sampler (Sampler, optional): like sampler, but returns a batch of
            indices at a time. Mutually exclusive with batch_size, shuffle,
            sampler, and drop_last.
        num_workers (int, optional): how many subprocesses to use for data
            loading. 0 means that the data will be loaded in the main process.
            (default: 0)
        collate_fn (callable, optional): merges a list of samples to form a mini-batch.
        pin_memory (bool, optional): If ``True``, the data loader will copy tensors
            into CUDA pinned memory before returning them.
        drop_last (bool, optional): set to ``True`` to drop the last incomplete batch,
            if the dataset size is not divisible by the batch size. If ``False`` and
            the size of dataset is not divisible by the batch size, then the last batch
            will be smaller. (default: False)
        timeout (numeric, optional): if positive, the timeout value for collecting a batch
            from workers. Should always be non-negative. (default: 0)
        worker_init_fn (callable, optional): If not None, this will be called on each
            worker subprocess with the worker id (an int in ``[0, num_workers - 1]``) as
            input, after seeding and before data loading. (default: None)

    .. note:: By default, each worker will have its PyTorch seed set to
              ``base_seed + worker_id``, where ``base_seed`` is a long generated
              by main process using its RNG. However, seeds for other libraies
              may be duplicated upon initializing workers (w.g., NumPy), causing
              each worker to return identical random numbers. (See
              :ref:`dataloader-workers-random-seed` section in FAQ.) You may
              use ``torch.initial_seed()`` to access the PyTorch seed for each
              worker in :attr:`worker_init_fn`, and use it to set other seeds
              before data loading.

    .. warning:: If ``spawn`` start method is used, :attr:`worker_init_fn` cannot be an
                 unpicklable object, e.g., a lambda function.
    """

    __initialized = False

    def __init__(self, dataset, batch_size=1, shuffle=False, sampler=None, batch_sampler=None,
                 num_workers=0, collate_fn=default_collate, pin_memory=False, drop_last=False,
                 timeout=0, worker_init_fn=None):
        self.dataset = dataset
        self.batch_size = batch_size
        self.num_workers = num_workers
        self.collate_fn = collate_fn
        self.pin_memory = pin_memory
        self.drop_last = drop_last
        self.timeout = timeout
        self.worker_init_fn = worker_init_fn

        if timeout < 0:
            raise ValueError('timeout option should be non-negative')

        if batch_sampler is not None:
            if batch_size > 1 or shuffle or sampler is not None or drop_last:
                raise ValueError('batch_sampler option is mutually exclusive '
                                 'with batch_size, shuffle, sampler, and '
                                 'drop_last')
            self.batch_size = None
            self.drop_last = None

        if sampler is not None and shuffle:
            raise ValueError('sampler option is mutually exclusive with '
                             'shuffle')

        if self.num_workers < 0:
            raise ValueError('num_workers option cannot be negative; '
                             'use num_workers=0 to disable multiprocessing.')

        if batch_sampler is None:
            if sampler is None:
                if shuffle:
                    sampler = RandomSampler(dataset)
                else:
                    sampler = SequentialSampler(dataset)
            batch_sampler = BatchSampler(sampler, batch_size, drop_last)

        self.sampler = sampler
        self.batch_sampler = batch_sampler
        self.__initialized = True

    def __setattr__(self, attr, val):
        if self.__initialized and attr in ('batch_size', 'sampler', 'drop_last'):
            raise ValueError('{} attribute should not be set after {} is '
                             'initialized'.format(attr, self.__class__.__name__))

        super(DataLoader, self).__setattr__(attr, val)

    def __iter__(self):
        return _DataLoaderIter(self)

    def __len__(self):
        return len(self.batch_sampler)

    
class _DataLoaderIter(object):
    r"""Iterates once over the DataLoader's dataset, as specified by the sampler"""

    def __init__(self, loader):
        self.dataset = loader.dataset
        self.collate_fn = loader.collate_fn
        self.batch_sampler = loader.batch_sampler
        self.num_workers = loader.num_workers
        self.sample_iter = iter(self.batch_sampler)
        
        # 省略部分代码...
        if self.num_workers > 0:
            # 使用Queue存放数据集索引，使用SimpleQueue存放数据加载结果
            self.worker_init_fn = loader.worker_init_fn
            self.index_queues = [multiprocessing.Queue() for _ in range(self.num_workers)]
            self.worker_queue_idx = 0
            self.worker_result_queue = multiprocessing.SimpleQueue()
            self.batches_outstanding = 0	# 在进程队列中需要处理的数据数量
            self.worker_pids_set = False
            self.shutdown = False
            self.send_idx = 0	# 需要处理的数据索引，以batch为单位
            self.rcvd_idx = 0	# 需要取用的数据索引，以batch为单位
            self.reorder_dict = {}	# 取出但未使用的索引和数据，以batch为单位
            # 创建Process对象，然后调用它的 start() 方法来生成进程
            self.workers = [
                multiprocessing.Process(
                    target=_worker_loop,
                    args=(self.dataset, self.index_queues[i],
                          self.worker_result_queue, self.collate_fn, base_seed + i,
                          self.worker_init_fn, i))
                for i in range(self.num_workers)]

            for w in self.workers:
                w.daemon = True  # ensure that the worker exits on process exit
                w.start()
                
            # prime the prefetch loop
            for _ in range(2 * self.num_workers):
                self._put_indices()
                
    def _put_indices(self):
        assert self.batches_outstanding < 2 * self.num_workers
        indices = next(self.sample_iter, None)	# 预取一个batch数据索引
        if indices is None:
            return	# 没有数据要处理了
        self.index_queues[self.worker_queue_idx].put((self.send_idx, indices))
        # 逐进程放入需要处理的batch索引，以平衡不同进程需要处理的数据量
        self.worker_queue_idx = (self.worker_queue_idx + 1) % self.num_workers
        self.batches_outstanding += 1
        self.send_idx += 1    
        
    def _process_next_batch(self, batch):
        # 只有取了batch，才继续放indices去处理，防止内存爆了
        self.rcvd_idx += 1
        self._put_indices()
        if isinstance(batch, ExceptionWrapper):
            raise batch.exc_type(batch.exc_msg)
        return batch
    
    def __len__(self):
        return len(self.batch_sampler)

    def _get_batch(self):
        if self.timeout > 0:
            try:
                return self.data_queue.get(timeout=self.timeout)
            except queue.Empty:
                raise RuntimeError('DataLoader timed out after {} seconds'.format(self.timeout))
        else:
            return self.data_queue.get()
        
    # 迭代逻辑
    def __next__(self):
        # 单进程
        if self.num_workers == 0:  # same-process loading
            indices = next(self.sample_iter)  # may raise StopIteration
            batch = self.collate_fn([self.dataset[i] for i in indices])
            if self.pin_memory:
                batch = pin_memory_batch(batch)
            return batch

        # check if the next sample has already been generated
        if self.rcvd_idx in self.reorder_dict:
            batch = self.reorder_dict.pop(self.rcvd_idx)
            return self._process_next_batch(batch)

        if self.batches_outstanding == 0:	# 已经没有数据要处理了
            self._shutdown_workers()
            raise StopIteration

        while True:
            assert (not self.shutdown and self.batches_outstanding > 0)
            idx, batch = self._get_batch()	# 从结果队列中取数据
            self.batches_outstanding -= 1
            if idx != self.rcvd_idx:
                # store out-of-order samples
                self.reorder_dict[idx] = batch
                continue
            return self._process_next_batch(batch)
        
    def __iter__(self):
        return self    
    
# 核心循环
def _worker_loop(dataset, index_queue, data_queue, collate_fn, seed, init_fn, worker_id):
    global _use_shared_memory
    _use_shared_memory = True

    # Intialize C side signal handlers for SIGBUS and SIGSEGV. Python signal
    # module's handlers are executed after Python returns from C low-level
    # handlers, likely when the same fatal signal happened again already.
    # https://docs.python.org/3/library/signal.html Sec. 18.8.1.1
    _set_worker_signal_handlers()

    torch.set_num_threads(1)
    random.seed(seed)
    torch.manual_seed(seed)

    if init_fn is not None:
        init_fn(worker_id)
    # 自定义类，管理进程状态
    watchdog = ManagerWatchdog()

    while True:
        try:
            # 获取dataset索引
            r = index_queue.get(timeout=MANAGER_STATUS_CHECK_INTERVAL)
        except queue.Empty:
            if watchdog.is_alive():
                continue
            else:
                break
        if r is None:
            break
        idx, batch_indices = r
        try:
            # 调用dataset的__getitem__获取数据，收集到的一个batch数据还可经过collate_fn进一步处理
            samples = collate_fn([dataset[i] for i in batch_indices])
        except Exception:
            data_queue.put((idx, ExceptionWrapper(sys.exc_info())))
        else:
            # 将制作好的数据放入SimpleQueue
            data_queue.put((idx, samples))
            del samples
```

采样器取batch数据索引，这个过程是在主进程进行的。一个batch数据的获取是在同一进程完成的，不同batch数据的获取是在不同进程中完成的。DataLoader类的设计看起来很棒(用起来也很棒)，主要学习其：

- 组合设计模式。DataLoader组合Dataset和Sample，并且Dataset和Sample都支持自定义派生，灵活性很高。
- 继承设计模式。Sample和Dataset都是抽象类，PyTorch提供一组派生自Sample的采样器，满足大部分需求。而由于数据集的变化较大，因此只定义了接口。
- 迭代器模式。DataLoader自身就是一个良好的迭代器，让人们无须关注它的内部实现而轻易使用。
- 多进程数据处理的设计。即支持单进程也支持多进程，且多进程设计的逻辑非常清晰。将一个batch数据放在同一进程处理，不同batch数据放在不同进程处理。主进程负责任务调度和数据取用。
- 队列数据结构的使用。不同进程各自拥有自己待处理数据(索引)队列，处理完的数据统一存放在一个队列中。



我曾经遇到过一个奇怪的问题，问题描述如下：

```python
for iter_id, batch in enumerate(data_loader):    
    print('data_loader.dataset id:', id(data_loader.dataset))
    if hasattr(data_loader.dataset, 'input_size'):        
        data_loader.dataset.input_size = 32 * random.randint(12, 20)

class MyDataset(torch.utils.data.Dataset):
    def __getitem__(self, index):
        print('dataset id:', id(self))
```

在迭代DataLoader对象data_loader过程，修改data_loader.dataset中一个数据。发现在Dataset派生类对象dataset的`__getitem__`方法中属性值并没有被修改，且修改的对象确认为同一对象。

```bash
data_loader.dataset id: 140374705187584
dataset id: 140374705187584
```

一度怀疑是DataLoader类使用了多进程的原因，因为设置num_workers=0后便修改成功。而后详细了解其多进程设计后，又觉得这个现象与多进程无关，不同进程之间采用共享内存方式共享dataset数据。

软件环境

```shell
OS: Ubuntu Server 16.04
Python: 3.6.8
PyTorch: 0.4.1(从源码编译==> torch version: 0.5.0a0+a24163a)
CUDA: 9.0.176
cudnn: 7104
```

原因：

这确实是多进程的原因，虽然是同一个对象，但是不同进程修改了对象不会同步进其它进程。

PS：不要在迭代时修改迭代器对象！



## DataLoader迭代关系简化

```python
class DataLoader(object):
    """
    使用dataset, batch_size等参数构造DataLoader
    """
    def __init__(self, dataset, batch_size, ...):
        self.dataset = dataset
        self.batch_size = batch_size
        pass
    def __iter__(self):
        return _DataLoaderIter(self)
        
class _DataLoaderIter(object):
    """
    存储容器loader的引用，也获取了dataloader的各种参数，如collate_fn，batch_sampler等，用于不同策略的迭代
    """
    def __init__(self, loader):
    	  self.dataset = loader.dataset
        self.collate_fn = loader.collate_fn
        self.batch_sampler = loader.batch_sampler
        self.sample_iter = iter(self.batch_sampler) # 使用一个iterator来记录当前迭代的状态
        pass
    
    """
    返回self，和上面的例子一样
    """
    def __iter__(self):
        return self
    
    """
    使用batch_size等(代码经过简化)
    """  
    def __next__(self):
        
        # 获取下一个batch包含dataset中哪些index，next可能会抛出StopIteration异常
        indices = next(self.sample_iter)  
        
        # 这里调用了dataset的__getitem__函数真正获取了数据并返回
        batch = self.collate_fn([self.dataset[i] for i in indices]) 
        
        return batch
```

