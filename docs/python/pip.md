根据requirement.txt使用清华源下载安装包到指定目录下：

pip download -i https://pypi.tuna.tsinghua.edu.cn/simple -d /home/gfjiang/packs -r requirement.txt



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