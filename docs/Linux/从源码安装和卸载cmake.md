1 从官网下载源码，一般不超过10M

```
wget https://cmake.org/files/v3.15/cmake-3.15.0.tar.gz
```



2 解压并进入

```bash
tar -zxvf cmake-3.15.0.tar.gz
cd cmake-3.15.0
```



3 配置、编译和安装

```bash
./bootstrap
make -j8
sudo make install
```



4 卸载

```bash
sudo make uninstall
```



5 题外话

其实我觉得真没必要自己编译安装，这个过程比较耗时，可以直接从官网下载编译好的，直接安装。

如果不介意安装的不是新版本，也可以使用apt安装。

```bash
sudo apt install cmake
```

