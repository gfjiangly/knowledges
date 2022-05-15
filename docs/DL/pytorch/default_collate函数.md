接受一个batch数据，batch必须是可迭代对象。

循环batch的目的是将每个item值取出，然后增加一个batch维度，将所有元素放在一起。最后返回的类型取决于item，如果item是单个对象，最后返回的也是单个对象，如果item是个可迭代对象，最后返回的是list，只不过每个list里面是张量。

举例

```python
batch = [[int1, list1, dict1], [int2, list2, dict2], ...]
batch = default_collate(batch)
# 此时batch为：[tensor1, tensor2, tensor3]，tensor1由int1，int2, ... 在batch维度堆叠在一起
```



源码

```python
if PY2:
    string_classes = basestring
else:
    string_classes = (str, bytes)


if PY2:
    int_classes = (int, long)
else:
    int_classes = int

    
def default_collate(batch):
    r"""Puts each data field into a tensor with outer dimension batch size"""

    error_msg = "batch must contain tensors, numbers, dicts or lists; found {}"
    elem_type = type(batch[0])
    if isinstance(batch[0], torch.Tensor):
        out = None
        if _use_shared_memory:
            # If we're in a background process, concatenate directly into a
            # shared memory tensor to avoid an extra copy
            numel = sum([x.numel() for x in batch])
            storage = batch[0].storage()._new_shared(numel)
            out = batch[0].new(storage)
        return torch.stack(batch, 0, out=out)
    elif elem_type.__module__ == 'numpy' and elem_type.__name__ != 'str_' \
            and elem_type.__name__ != 'string_':
        elem = batch[0]
        if elem_type.__name__ == 'ndarray':
            # array of string classes and object
            if re.search('[SaUO]', elem.dtype.str) is not None:
                raise TypeError(error_msg.format(elem.dtype))

            return torch.stack([torch.from_numpy(b) for b in batch], 0)
        if elem.shape == ():  # scalars
            py_type = float if elem.dtype.name.startswith('float') else int
            return numpy_type_map[elem.dtype.name](list(map(py_type, batch)))
    elif isinstance(batch[0], int_classes):
        return torch.LongTensor(batch)
    elif isinstance(batch[0], float):
        return torch.DoubleTensor(batch)
    elif isinstance(batch[0], string_classes):
        return batch
    elif isinstance(batch[0], collections.Mapping):
        return {key: default_collate([d[key] for d in batch]) for key in batch[0]}
    elif isinstance(batch[0], collections.Sequence):
        # 这里是精华部分，用了zip将元素按位置配对后递归，目的就是要把最内层元素按batch维度堆叠
        transposed = zip(*batch)
        return [default_collate(samples) for samples in transposed]

    raise TypeError((error_msg.format(type(batch[0]))))
```

