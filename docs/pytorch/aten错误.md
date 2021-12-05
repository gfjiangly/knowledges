```bash
/pytorch/aten/src/THC/THCTensorScatterGather.cu:97: void THCudaTensor_gatherKernel(TensorInfo<Real, IndexType>, TensorInfo<Real, IndexType>, TensorInfo<long, IndexType>, int, IndexType) [with IndexType = unsigned int, Real = float, Dims = 2]: block: [0,0,0], thread: [127,0,0] Assertion `indexValue >= 0 && indexValue < src.sizes[dim]` failed.

```

遇到此类错误即使使用try捕获，再获取一次迭代代替这次失败的迭代也无济于事，因为CUDA的上下文已经损坏，必须停止程序，找到错误的原因。

