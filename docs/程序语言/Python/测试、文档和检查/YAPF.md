YAPF (Yet Another Python Formatter)是Google开源的一个用来格式化Python代码的工具. 支持2种代码规范

- PEP8
- Google style

### 安装

```bash
pip install yapf
```

### 常用命令

- 直接跟文件名（并不修改文件）

```sh
yapf <python file>
```

不想格式化的在代码段的后面加上： # yapf: disable 

参考：

- https://github.com/open-mmlab/mmdetection/blob/master/.style.yapf

