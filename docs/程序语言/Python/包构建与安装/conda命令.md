
```bash
# 清理缓存
conda clean -ya
```

1、 首先在所在系统中安装Anaconda。可以打开命令行输入conda -V检验是否安装以及当前conda的版本。

conda list查看安装了哪些包

conda env list查看有哪些虚拟环境

conda -V查看conda的版本

 

2、conda常用的命令。

​       1）conda list 查看安装了哪些包。

​    2）conda env list 或 conda info -e 查看当前存在哪些虚拟环境

​    3）conda update conda 检查更新当前conda

 

3、创建python虚拟环境。

使用 conda create -n your_env_name python=X.X（2.7、3.6等)命令创建python版本为X.X、名字为your_env_name的虚拟环境。your_env_name文件可以在Anaconda安装目录envs文件下找到。

 新版Anaconda或Miniconda虚拟环境位置从软件安装路径变更到用户目录`.conda/envs`下。



4、使用激活(或切换不同python版本)的虚拟环境。

​    打开命令行输入python --version可以检查当前python的版本。

​    使用如下命令即可 激活你的虚拟环境(即将python的版本改变)。

​    Linux:  source activate your_env_name(虚拟环境名称)

​    Windows: activate your_env_name(虚拟环境名称)

   这是再使用python --version可以检查当前python版本是否为想要的。

 

5、对虚拟环境中安装额外的包。

​    使用命令conda install -n your_env_name [package]即可安装package到your_env_name中

 

6、关闭虚拟环境(即从当前环境退出返回使用PATH环境中的默认python版本)。

   Linux: source deactivate

   Windows: deactivate

 

7、删除虚拟环境。

   使用命令conda remove -n your_env_name(虚拟环境名称) --all， 即可删除。

 

8、删除环境中的某个包。

   使用命令conda remove --name your_env_name  package_name 即可。

 

9、requirements使用

conda导出已有环境：

conda env export > environment.yaml

环境会被保存在 environment.yaml文件中。当我们想再次创建该环境，或根据别人提供的.yaml文件复现环境时，可以：

conda env create -f environment.yaml

就可以复现安装环境。

 

移植过来的环境只是安装原来环境里用conda install等命令直接安装的包，用pip之类装的东西没有移植过来，需要重新安装。

 

导出requirements方法

pip freeze > requirements.txt

安装requirements方法

pip install -r requirements.txt

 

注：如果是虚拟环境，记得导入导出包的时候要先激活虚拟环境。



常用包安装：

```bash
# 安装cuda, 不指定版本默认安装最新版
conda install cudatoolkit=8.0 -c https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/linux-64/

# 安装cudnn:
conda install cudnn=7.0.5 -c https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/linux-64/

# tf2的gpu和cpu版本不需要分开安装
conda install tensorflow-gpu keras
conda install pillow
conda install matplotlib
conda install scikit-learn
conda install graphviz pydot

# 当解决环境很费时时可以尝试
conda upgrade --all
```

