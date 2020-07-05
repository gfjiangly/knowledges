## GPU可见度

终端设定

```bash
CUDA_VISIBLE_DEVICES=1 python train.py
```

代码设定

```python
import os
os.environ["CUDA_VISIBLE_DEVICES"] = "1,2,3,4"
```



## 显存使用

```python
# set GPU memory 
mport tensorflow as tf
config = tf.compat.v1.ConfigProto()
config.gpu_options.allow_growth=True
tf.compat.v1.Session(config=config)
```

