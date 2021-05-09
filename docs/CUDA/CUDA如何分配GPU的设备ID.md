CUDA默认选择算力最强的设备作为设备0。可以导入环境变量使CUDA按PCI ID来分配GPU设备ID： 

```bash
export CUDA_DEVICE_ORDER=PCI_BUS_ID
```

编码时更为灵活：

```cpp
cudaError_t cudaDeviceGetByPCIBusId ( int* device, char* pciBusId )
   Returns a handle to a compute device.

cudaError_t cudaDeviceGetPCIBusId ( char* pciBusId, int  len, int  device )
   Returns a PCI Bus Id string for the device.
```

 或CUDA驱动程序API `cuDeviceGetByPCIBusId` 和`cuDeviceGetPCIBusId`。 

 nvidia-smi 命令显示的顺序是PCI ID顺序排列的GPU。

