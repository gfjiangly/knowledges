驱动版本与CUDA版本对应关系

![img](../../assets/cuda_sm_list.jpg)


```bash
# 卸载原驱动
sudo /usr/bin/nvidia-uninstall
# or
sudo apt-get install autoremove --purge nvidia*

# 安装驱动
# 1 从cuda中
sh cuda_10.0.130_410.48_linux.run
# 2 单独驱动，从官网下载。无须注册
sh NVIDIA-Linux-x86_64-410.48.run

```