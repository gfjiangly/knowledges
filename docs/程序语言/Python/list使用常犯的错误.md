## 认为list.append()会返回插入数据后的list

```python
results = []
dets = [[1, 2, 3, 4], [4, 5, 6, 7]]
for det in dets:
    score = 1.
    results.append(dets.append(score))
# result里面全都是None，因为append函数没有返回值。append是典型的原位修改方法
```

正确用法是：

```python
results = []
dets = [[1, 2, 3, 4], [4, 5, 6, 7]]
for det in dets:
    score = 1.
    results.append(dets+[score])
# 用+连接两个list会返回一个新的list
```

