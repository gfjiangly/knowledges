## 测试文件/目录是否存在

```bash
# -f为文件 -d为目录
hadoop fs -test -f /path/exist
if [ $? -eq 0 ] ;then 
    echo 'Is a file' 
else 
    echo 'Is not a file' 
fi
```

如果文件或目录存在，-test这个命令将返回0；反之则返回1



## Java运行时错误

![img](hdfs命令.assets/(null)-20200705234522648.(null))

这个问题还是因为程序内部抛出了异常，影响到容器内Java运行时。要确保程序不会抛出异常（自己捕捉、处理），此外如果保存数据在单独进程，要确保这个进程不会因为错误提前退出，否则会发现处理数据的进程进行的好好的，但是保存结果的文件里一直没动静（已flush）。

## 常用命令

![img](hdfs命令.assets/(null)-20200705234523071.(null))



## 路径通配符

Hadoop支持的一系列通配符与Unix bash相同：

![img](hdfs命令.assets/(null)-20200705234521346.(null))

![img](hdfs命令.assets/(null))