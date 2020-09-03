### 调用方式

需要用`torch.distributed.launch`来启动训练。

- 作用

- - 在这里，我们给出分布式训练的**重要参数**：

  - - **有多少台机器？**

    - - **--nnodes**

    - **当前是哪台机器？**

    - - **--node_rank**

    - **每台机器有多少个进程？**

    - - **--nproc_per_node**

    - 高级参数（可以先不看，多机模式才会用到）

    - - 通讯的address
      - 通讯的port

- 实现方式

- - 我们需要在每一台机子（总共m台）上都运行一次`torch.distributed.launch`

  - 每个`torch.distributed.launch`会启动n个进程，并给每个进程一个`--local_rank=i`的参数

  - - 这就是之前需要"新增:从外面得到local_rank参数"的原因

  - 这样我们就得到`n*m`个进程，`world_size=n*m`

**单机模式**

```bash
## Bash运行
# 假设我们只在一台机器上运行，可用卡数是8
python -m torch.distributed.launch --nproc_per_node 8 main.py
```

**多机模式**

复习一下，master进程就是rank=0的进程。
在使用多机模式前，需要介绍两个参数：

- 通讯的address

- - `--master_address`
  - 也就是master进程的网络地址
  - 默认是：127.0.0.1，只能用于单机。

- 通讯的port

- - `--master_port`
  - 也就是master进程的一个端口，要先确认这个端口没有被其他程序占用了哦。一般情况下用默认的就行
  - 默认是：29500

```bash
## Bash运行
# 假设我们在2台机器上运行，每台可用卡数是8
#    机器1：
python -m torch.distributed.launch --nnodes=2 --node_rank=0 --nproc_per_node 8 \
  --master_adderss $my_address --master_port $my_port main.py
#    机器2：
python -m torch.distributed.launch --nnodes=2 --node_rank=1 --nproc_per_node 8 \
  --master_adderss $my_address --master_port $my_port main.py
```

**小技巧**

```bash
# 假设我们只用4,5,6,7号卡
CUDA_VISIBLE_DEVICES="4,5,6,7" python -m torch.distributed.launch --nproc_per_node 4 main.py
# 假如我们还有另外一个实验要跑，也就是同时跑两个不同实验。
#    这时，为避免master_port冲突，我们需要指定一个新的。这里我随便敲了一个。
CUDA_VISIBLE_DEVICES="4,5,6,7" python -m torch.distributed.launch --nproc_per_node 4 \
    --master_port 53453 main.py
```

### **mp.spawn**调用方式

PyTorch引入了torch.multiprocessing.spawn，可以使得单卡、DDP下的外部调用一致，即不用使用torch.distributed.launch。 python main.py一句话搞定DDP模式。

给一个mp.spawn的文档：[代码文档](https://link.zhihu.com/?target=https%3A//pytorch.org/docs/stable/_modules/torch/multiprocessing/spawn.html%23spawn)

下面给一个简单的demo：

```python
def demo_fn(rank, world_size):
    dist.init_process_group("nccl", rank=rank, world_size=world_size)
    # lots of code.
    ...

def run_demo(demo_fn, world_size):
    mp.spawn(demo_fn,
             args=(world_size,),
             nprocs=world_size,
             join=True)
```

**mp.spawn与launch各有利弊，请按照自己的情况选用。**
按照笔者个人经验，如果算法程序是提供给别人用的，那么mp.spawn更方便，因为不用解释launch的用法；但是如果是自己使用，launch更有利，因为你的内部程序会更简单，支持单卡、多卡DDP模式也更简单。



参考：

- https://zhuanlan.zhihu.com/p/178402798
- https://zhuanlan.zhihu.com/p/76638962