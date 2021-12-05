N: batch

C: channel

H: height

W: width

Caffe 的Blob通道顺序是：NCHW;

TensorFlow的tensor通道顺序：默认是NHWC， 也支持NCHW，使用cuDNN会更快;

Pytorch中tensor的通道顺序：NCHW

TensorRT中的tensor 通道顺序： NCHW

MXNet中的tensor 通道顺序： NCHW



### 修改channel顺序

```python
  # change channels last to channels first format
  img = moveaxis(img, 2, 0)
  print(img.shape)
  # change channels first to channels last format
  img = moveaxis(img, 0, 2)
  print(img.shape)
```

