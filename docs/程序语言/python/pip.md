根据requirement.txt使用清华源下载安装包到指定目录下：

pip download -i https://pypi.tuna.tsinghua.edu.cn/simple -d /home/gfjiang/packs -r requirement.txt

-i 指定临时源



持久修改pip源

linux下，修改 ~/.pip/pip.conf (没有就创建一个)， 修改 index-url至tuna，内容如下：

[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple

windows下，直接在user目录中创建一个pip目录，如：C:\Users\xx\pip，新建文件pip.ini，内容如下

[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple



安装卸载pytorch

cuda9.0

```bash
pip install torch torchvision
conda install pytorch torchvision cudatoolkit=9.0 -c pytorch
```

cuda10.0

```bash
pip install https://download.pytorch.org/whl/cu100/torch-1.1.0-cp37-cp37m-linux_x86_64.whl
pip install https://download.pytorch.org/whl/cu100/torchvision-0.3.0-cp37-cp37m-linux_x86_64.whl
conda install pytorch torchvision cudatoolkit=10.0 -c pytorch
```

pip注意使用清华pypi源，速度快很多

pip uninstall torch torchvision



缓存位置

Linux and Unix

~/.cache/pip 

Windows

%LocalAppData%\pip\Cache



pip命令

```bash
# 查看包安装信息
pip show 包名

```



