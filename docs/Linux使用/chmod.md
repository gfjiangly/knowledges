可以使用命令chmod来为文件或目录赋予权限。Linux/Unix 的档案存取权限分为三级 : 档案拥有者、群组、其他。利用 chmod 可以藉以控制档案如何被他人所存取。



### 快速使用

查看linux文件的权限：`ls -l 文件名称 `
查看linux文件夹的权限：`ls -ld 文件夹名称(所在目录) `

修改权限

```bash
chmod o+r file  # 为other用户组添加读权限
chmod o-w file  # 为other用户组去除写权限
```

递归修改

```bash
chmod -R o+r file  # 为other用户组添加读权限
chmod -R o-w file  # 为other用户组去除写权限
# -R也可以放置在末尾
```

修改所有者

```bash
chown [-R] 账号名称 文件或目录
chown [-R] 账号名称:用户组名称 文件或目录
```



### 字母方式

用户类型

```
u：拥有文件的用户（所有者）； 
g：所有者所在的组群； 
o：其他人（不是所有者或所有者的组群）； 
a：每个人或全部（u、g、和o）。 
```

用户所具有的文件访问权限类型如下： 

```
r：读取权； 
w：写入权； 
x：执行权。 
```

文件权限配置行为有如下几类： 

```
+：添加权限； 
-：删除权限； 
=：使它成为惟一权限。 
```

例子：

```bash
chmod o+w readme.txt  # 为other添加读权限
chmod go -rw readme.txt  # 为go删除读写权限，go是group和other
# 删除所有权限（包括每个人的权限）
chmod a -rwx readme.txt 
# 为自己添加读写权限
chmod u+rw readme.txt 
```

几个用在chmod命令设置上的常用例子： 

```
g+w：为组群添加写入权； 
o-rwx：删除其他人的所有权限； 
u+x：允许文件所有者执行这个文件； 
a+rw：允许每个人读取并写入文件； 
ug+r：允许所有者和组群读取文件； 
g=rx：只允许组群读取和执行（不能写入）。 
```



### 数字方式

```bash
chmod abc file 
```

其中a,b,c各为一个数字，分别表示User、Group、及Other的权限。 

```
r=4，w=2，x=1 
```

若要rwx属性则4+2+1=7； 若要rw-属性则4+2=6； 若要r-x属性则4+1=5。 

例子：

```bash
chmod a=rwx file   # a表示all
chmod 777 file  # 第一个数字表示user权限, 第二个数字表示group权限，第三个数字表示other权限
# 上面两个命令等价
```

```bash
chmod ug=rwx,o=x file  # u表示user，即拥有者；g表示group，同组用户；o表示other，其它用户组用户
chmod 771 file
# 上面两个命令等价
```


