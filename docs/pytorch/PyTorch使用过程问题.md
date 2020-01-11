## tensor类型错误

```text
  File "/CenterNet/src/lib/models/utils.py", line 16, in _gather_feat
    feat = feat.gather(1, ind)
RuntimeError: Expected object of type torch.cuda.LongTensor but found type torch.LongTensor for argument #3 'index'
```

期待`torch.cuda.LongTensor`得到的却是`torch.LongTensor`，注意这并不是说你创建的是`torch.LongTensor`，有可能你创建的是`torch.FloatTensor`，由此操作出`torch.LongTensor`，总之有张量不在GPU上。

## tensor 维度错误

```text
RuntimeError: The expanded size of the tensor (52) must match the existing size (51) at non-singleton dimension 3
```

