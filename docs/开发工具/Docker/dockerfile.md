## 编译镜像

```bash
# 注意在dockerfile所在文件夹下执行
sudo docker build -t ngnix:v1 .
```



## 更换Ubuntu源

```bash
# 清华源
RUN sed -i s:/archive.ubuntu.com:/mirrors.tuna.tsinghua.edu.cn/ubuntu:g /etc/apt/sources.list \
    && apt-get clean && apt-get update
# 阿里云
RUN  sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list \
    && apt-get clean && apt-get update
```



## 更换pip源

```dockerfile
# 阿里云源
RUN pip install -U pip
RUN pip config set global.index-url http://mirrors.aliyun.com/pypi/simple
RUN pip config set install.trusted-host mirrors.aliyun.com

# 清华源
RUN pip install -U pip
RUN pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
RUN pip config set install.trusted-host pypi.tuna.tsinghua.edu.cn
```

临时使用，直接在安装命令后加` -i https://pypi.tuna.tsinghua.edu.cn/simple`



## 镜像中时区/时间不对

参考：https://www.jianshu.com/p/23d07d9c6278