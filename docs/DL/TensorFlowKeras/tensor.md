rank，即矩阵的秩。

标量的rank=0，一维tensor的rank=1，二维tensor的rank=2

## 创建tensor

```python
# 全0
tf.ones(shape, type=tf.float32, name=None)
# 全1
tf.zeros(shape, type=tf.float32, name=None)

# 新建一个与给定的tensor类型、大小一致的tensor，其所有元素为1和0
tf.ones_like(tensor, dype=None, name=None) 
tf.zeros_like(tensor, dype=None, name=None) 

# 创建一个形状大小为shape的tensor，其初始值为value
tf.fill(shape,value,name=None)
>>> z = tf.fill([2, 3], 2)
>>> sess.run(z)
array([[2, 2, 2],
       [2, 2, 2]])

# random_normal: 正太分布随机数，均值mean,标准差stddev 
tf.random_normal(shape, mean=0.0, stddev=1.0, dtype=tf.float32, seed=None, name=None) 

# truncated_normal: 截断正态分布随机数，均值mean,标准差stddev,不过只保留[mean-2*stddev,mean+2*stddev]范围内的随机数 
tf.truncated_normal(shape, mean=0.0, stddev=1.0, dtype=tf.float32, seed=None, name=None) 

# random_uniform: 均匀分布随机数，范围为[minval, maxval]
tf.random_uniform(shape, minval=0, maxval=None, dtype=tf.float32, seed=None, name=None) 

# 沿着value的第一维进行随机重新排列
tf.random_shuffle(value, seed=None, name=None) 
```

