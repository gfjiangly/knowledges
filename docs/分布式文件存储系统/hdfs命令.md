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

