可能一张图中没有标记，此时需要重新取一张；或者图片损坏之类，也需要重新取一张；总之错误了就要重新取数据。

```python
def __getitem__(self, index):
    again = False
    try:
        # 如果发生异常，就将异常的数据丢弃掉
        im, gt = self.pull_item(index)
    except Exception as e:
        print('getitem exception:', e)
        # 对于诸如样本损坏或数据集加载异常等情况,就随机取一张图片代替：
        again = True
    if len(gt) == 0 or again:
        new_index = random.randint(0, len(self) - 1)
        return self[new_index]
    return im, gt

def getitem(self, index):
    # 此处省略
    pass
```

