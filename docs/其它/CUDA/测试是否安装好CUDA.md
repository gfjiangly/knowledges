安装低版本gcc与g++(有的话跳过)：

```bash
sudo apt-get install gcc-4.9 g++-4.9
```



测试CUDA的sammples：

```bash
cd /usr/local/cuda/samples/1_Utilities/deviceQuery #由自己电脑目录决定
make
sudo ./deviceQuery
```

![image-20200722000836857](../../assets/image-20200722000836857.png)

