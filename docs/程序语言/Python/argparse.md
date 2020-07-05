尽量不要使用F-String语法，此语法只在py3.6及以上版本才支持



## list参数

```python
parser.add_argument('--gpu-ids', type=int, nargs='+', help='ids of gpus to use')
```

```
$ python arg.py --gpu-ids 0 1 2 3
[0, 1, 2, 3]
```



## choice

```python
parser.add_argument(
  '--launcher',
  choices=['none', 'pytorch', 'slurm', 'mpi'],
  default='none',
  help='job launcher')
```



## bool参数

```python
  parser.add_argument('--val', action='store_true')
```

