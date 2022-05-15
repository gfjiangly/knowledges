- **安装**

```bash
conda install jupyter notebook
# or
pip install jupyter
```


- **配置**

```bash
jupyter notebook --generate-config  # 生成jupyter notebook配置文件
ipython # 打开ipython
```

```python
from notebook.auth import passwd
passwd() # 输入密码
```

 这时候会生成密码的hash值，把他复制出来。 

- **修改jupyper配置文件**

然后输入

```bash
nano ~/.jupyter/jupyter_notebook_config.py
```

对jupyter进行配置

```text
c.NotebookApp.ip='*'
c.NotebookApp.password = u'密码hash值'
c.NotebookApp.open_browser = False
c.NotebookApp.port = 8888
```

- **启动jupyter**

输入jupyter notebook，即可启动

```bash
nohup jupyter notebook --allow-root > notebook.log 2>&1 &
```

## 插件安装

```bash
pip install jupyter_contrib_nbextensions 
jupyter contrib nbextension install --user

pip install jupyter_nbextensions_configurator
jupyter nbextensions_configurator enable --user
```

- **Table of Contents** 目录
- **Execute Time**   统计cell运行时间 
- **Nofity ** 代码运行结束时发出通知 
- **Codefolding ** 代码折叠工具 
- **Hinterland**  自动补全插件 

## 参考

- https://www.jianshu.com/p/91365f343585
- https://zhuanlan.zhihu.com/p/97394628
- https://zhuanlan.zhihu.com/p/83252017