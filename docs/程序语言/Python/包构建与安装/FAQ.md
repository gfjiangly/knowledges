## Q: Debian / Ubuntu: Fatal error: Python.h: No such file or Directory

### For Python version 2.x+

```bash
sudo apt update
sudo apt install python-dev
```

### For Python version 3.x+

```bash
sudo apt update
sudo apt install python3-dev
```



## Q：没有pip怎么办

从下载pip源码，然后从源码安装。或者：

```bash
sudo apt update
sudo apt install python3-pip
```

