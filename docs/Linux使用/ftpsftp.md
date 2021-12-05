## sftp命令

```bash
# 废弃！不要用sftp/scp之类工具拷贝转移文件，会漏掉软链接。使用rsync同步工具
# login sftp
sftp gfjiang@10.193.0.20
# 传文件, 传输文件夹加 -r
put /home/jiang/soft/x86_64.iso /home/gfjiang/soft/
get /home/gfjiang/soft/Miniconda3-latest-Linux-x86_64.sh /home/jiang/soft/
```

## rsync命令

 rsync命令是一个远程数据同步工具，可通过LAN/WAN快速同步多台主机间的文件。 rsync使用所谓的“rsync算法”来使本地和远程两个主机之间的文件达到同步，这个算法只传送两个文件的不同部分，而不是每次都整份传送，因此速度相当快。 

```bash
# 本地文件
rsync -a /data /backup

# 把本地的source.txt文件拷贝到192.168.0.10机器上的/home/work目录下
rsync /home/work/source.txt work@192.168.0.10:/home/work/   

# 把192.168.0.10机器上的source.txt文件拷贝到本地的/home/work目录下
rsync work@192.168.0.10:/home/work/source.txt /home/work/ 

# 把192.168.0.10机器上的source.txt文件拷贝到192.168.0.11机器的/home/work目录下
rsync work@192.168.0.10:/home/work/source.txt work@192.168.0.11:/home/work/   

# 拷贝文件夹，加-r参数
rsync -azhrvl /home/work/sourcedir work@192.168.0.10:/home/work/ 

# 使用主机名
rsync -rvl /home/work/sourcedir work@www.myhost.com:/home/work/  

# 显示详情，加-v参数  
rsync -rvl /home/work/sourcedir work@www.myhost.com:/home/work/   

# 排除子目录，注意：--exclude后面的路径不能为绝对路径，必须为相对路径才可以，否则匹配不上，就不会被排除掉
rsync -rvl --exclude sourcedir/notinclude  /home/work/sourcedir work@www.myhost.com:/home/work/ 
```

```
-a 包含-rtplgoD参数选项
-r 同步目录时要加上，类似cp时的-r选项
-v 同步时显示一些信息，让我们知道同步的过程
-l 保留软连接
若是拷贝的原目录里面有一个软链接文件，那这个软链接文件指向到了另外一个目录下
在加上-l，它会把软链接文件本身拷贝到目标目录里面去
-L 加上该选项后，同步软链接时会把源文件给同步
-p 保持文件的权限属性
-o 保持文件的属主
-g 保持文件的属组
-D 保持设备文件信息
/dev/sdb1 这样的设备文件有它的特殊性，如果不加-D 可能拷贝过去就是一个非常普通的文件，不能当设备来用
-t 保持文件的时间属性
--delete 删除DEST中SRC没有的文件
--exclude 过滤指定文件，如--exclude “logs”会把文件名包含logs的文件或者目录过滤掉，不同步
-P 显示同步过程，比如速率，比-v更加详细
-u 加上该选项后，如果DEST中的文件比SRC新，则不同步
update
-z 传输时压缩
```

