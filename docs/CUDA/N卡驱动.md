![img](https://img-blog.csdnimg.cn/20190508103705372.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlc29uZ3plZmFpcnk=,size_16,color_FFFFFF,t_70)


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