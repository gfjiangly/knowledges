1、Samba的安装

sudo apt-get update

sudo apt-get install samba samba-common

 

 

2、创建共享目录

sudo mkdir /home/gfjiang/share

sudo chmod 777 /home/gfjiang/share

 

 

3、Samba配置文件

sudo nano /etc/samba/smb.conf

修改完配置后，把用户添加到samba组才能在客户端登录，最后重启服务：

```bash
# 添加到samba组
sudo smbpasswd -a jiang

sudo /etc/init.d/samba restart
or sudo service smbd restart
```



\# --------------------------------------------

 [root@s]
comment = Root Folder For DL
path = /
public = no
read only = no
guest ok = no
valid users = gfjiang
write list = gfjiang
available = yes
writable = yes



[pengchenghu samba share]

comment = samba home directory

path = /home/pengchenghu/share

public = yes

browseable = yes

read only = no

create mask = 0777

directory mask = 0777

force user = nobody

force group = nogroup

available = yes

 

[gfjiang]

comment = samba home directory

path = /home/gfjiang/

browseable = yes

public = no

read only = no

guest ok = no

available = yes

writable = yes 

valid users = gfjiang

write list = gfjiang

create mask = 0777

directory mask = 0777

force user = nobody

force group = nogroup



 

[Course]

comment = Shared Folder For seu08s

path = /home/gfjiang/seu08/Course

available = yes

browseable = yes

public = yes

writable = yes

guest ok = yes

 

[projects]

comment = samba home directory

path = /home/gfjiang/projects/

available = yes

browseable = yes

public = yes

writable = yes

guest ok = yes

\# --------------------------------------------

 

配置说明：

 

browseable：通过browseable参数可控制用户的浏览权限，当browseable = yes时，将显示共享资源，当browseable = no时，将隐藏共享资源，默认为browseable = yes。共享资源被隐藏后只是在浏览Samba服务器时不可见，与可不可以访问该共享资源无关（此功能与Windows中创建共享文件夹时在共享名后加$功能一样，可直接输入链接访问）。

 

public：设置是否允许匿名用户访问共享资源，当public = yes时表示允许，public = no时表示不允许，默认为public = no。在Samba服务器中guest ok与public有相同的功能。当一个共享资源参数中guest ok与public参数发生冲突时，在后面的参数优先（覆盖原则）。

 

valid users：设置允许访问共享资源的用户或组（如果使用组时，需要在组名称前加@）。设置该参数后，未指定的所有用户将不能访问共享资源。

 

wide links：设置是否允许共享外连接。如某共享资源内有个连接指向非共享资源的文件或目录，如果设置wide links = no将使该连接不可用，默认为wide links = yes。（该参数与硬链接和软链接相关）

 

用户在允许访问某共享资源的情况下，可以通过Samba对共享资源读写权限进行控制。在Windows中对一个位于NTFS分区中的共享文件夹有怎样的操作权限，由该文件夹的NTFS权限及共享权限共同起作用，两个权限中更严格的优先，如某用户对一个共享文件夹的NTFS权限为完全控制，共享权限为只读，那么这个用户通过网络访问该共享文件夹时的权限为只读。在Samba服务器也是这样，通过网络访问Samba服务器资源时，能否读写是通过文件或目录自身在文件系统中的权限与该文件或目录在Samba服务器配置共同决定的。如果希望某用户可以在读或者写Samba服务的共享资源，首先要正确配置该用户对文件或目录自身文件系统中的权限。

 

使用子配置文件

 

在前面讲述过browseable可以实现类似于Windows隐藏共享的功能，但是如果将该参数配置为browseable = no时，所有用户都无法浏览到，如果希望某个共享资源只允许特定的用户浏览，其他用户都无法浏览或访问时该怎么办呢？Windows的共享在默认情况下没有这个功能（在微软网站下载一个免费工具Windows Server 2003 Access-based Enumeration后也可实现），Samba可以通过引用子配置文件的方法来实现。

 

下面来看一个引用子配置文件的方法来实现。

 

在Samba服务器有一个共享目录share（/home/share的自身权限为777）希望用户test可见可读可写，其他用户不仅没有读写权限，连可见的权限都没。

①     在/etc/samba下建立一个名为test.smb.conf的文件，在该文件中加入以下内容：

[share]

   path = /home/share

   write list = test

 

②     引用子配置文件。在引用子配置文件时有两种方法：

● config file：在[global]标签下加入如下参数。

[global]

   config file = /etc/samba/%U.smb.conf

 

● include：在[global]标签下加入如下参数。

[global]

   include = /etc/samba/%U.smb.conf

 

config file和include的区别是：使用config file时，当以test的身份访问Samba服务器，只能浏览到share，其他在smb.conf中定义的共享资源都无法查看。使用include时，当以test的身份访问Samba服务器，除了可以浏览到share，其他在smb.conf中定义的共享资源也可以浏览到。

 

 

### 4 添加Samba访问账号-需要和配置文件中相对应

新增Samba访问账号sudo useradd gfjiang     # 添加系统用户，用户已存在可略去此步骤

设置Windows访问时需要的密码: sudo smbpasswd -a gfjiang    # gfjiang必须是系统用户，密码可以与gfjiang用户登录Linux的密码不同

重新启动Samba服务

sudo /etc/init.d/samba restart

or sudo service smbd restart

 

smbpasswd 命令的用法 

smbpasswd -a 增加用户（要增加的用户必须以是系统用户）   

smbpasswd -d 冻结用户，就是这个用户不能在登录了   

smbpasswd -e 恢复用户，解冻用户，让冻结的用户可以在使用   

smbpasswd -n 把用户的密码设置成空。要在global中写入 null passwords -true   

smbpasswd -x  删除用户  

 

 

5、用户账号映射 ： 

samba的用户帐号信息是保存在smbpasswd文件中滴，而且可以访问samba服务器的帐号也必须对应一个同名的系统帐号。基于这 一点，所以，对于一些hacker来说，只要知道samba服务器滴samba帐号，就等于是知道了Linux系统帐号，只要crack其samba帐号密码加以利用就可以攻击samba服务器。所以我们要使用用户帐号映射这个功能来解决这个问题。 

用户帐号映射这个功能需要建立一个帐号映射关系表，里面记录了samba帐号和虚拟帐号的对应关系，客户端访问samba服务器时就使用虚拟来登录。 

1）编辑主配置文件vi /etc/samba/smb.conf 

在global下添加一行字段username map = /etc/samba/smbusers开启用户帐号映射功能。 

2）编辑 vi /etc/samba/smbusers 

smbusers文件保存帐号映射关系，其有固定滴格式： 

samba帐号 = 虚拟帐号（映射帐号） 

myname = networkusername 

帐号myname就是我们上面建立的samba帐号（同时也是Linux系统帐号），networkusername就是映射的帐号名（虚拟帐号），帐号 myname在我们访问共享目录时只要输入networkusername就可以成功访问了，但是实际上访问samba服务器的还是我们的myname帐号，这样一来就解决了安全问题。 

3）重启samba服务：service smbd restart

 

 

6、Windows客户端切换注销/切换用户登录Samba

在Windows客户机访问Samba服务器时，常出现的一种现象是：在建立了访问Samba服务器的连接之后，再次访问该服务器时，不再出现身份认证 对话框，这样便无法更换用户身份。造成这一现象的原因是Windows本身的机制问题，更确切地说这是smb服务的问题，由于NETBIOS服务是面向连接的，当客户与Samba服务器建立连接后，此连接在一段时间内始终是活跃的，所以当用户再次访问该服务器时，便采用了前面的身份而无需再次验证身份。 

方法一：
删除windows保留的登录凭证
控制面饭->凭证管理器->找到想要删除的账号和密码



方法二：
在Windows系统的命令行中运行：

net use * /del

net use [\\10.193.0.20\IPC$](file://10.193.0.20/IPC$)

net use [\\10.193.0.20\IPC$](file://10.193.0.20/IPC$) /del

net use \\10.193.0.20 /del

在成功删除远程连接，过一段时间后再次通过“运行”对话框运行访问命令，就会重新提示输入用户名及密码了。

 

查看远程主机的共享资源（但看不到默认共享） 

net view [\\IP](file://IP)

 方法二有时不太好使，目前不清楚原因，似乎是清除后需要一定时间才能生效，有点奇怪。



# samba在linux之间共享文件挂载

mount -t cifs -o noserverino -o username="gfjiang",uid="501",gid="501" //10.193.0.20/gfjiang /home/gfjiang/10.193.0.20

- 需要安装cifs，`sudo apt-get install cifs-utils `

- 网上没加-o noserverino,uid,gid大部分都不能成功挂载

 username是远程主机的用户名，//10.193.0.20/gfjiang是远程主机目录，/home/gfjiang/10.193.0.20是本地挂载目录。命令输完后会要求输入远程主机登录密码，以授权访问。

因为是借助于sudo来挂载的，导致挂载共享硬盘里面的文件全部都只有root能进行修改。挂载时可以加上文件权限`-o file_mode=0777 -o dir_mode=0777`

samba服务使用的端口：139 及 445



## 无法访问软链接

与权限无关，在配置文件的`[global]`下增加：

```bash
wide links = yes
symlinks = yes
unix extensions = no
```



参考：

http://blog.sina.com.cn/s/blog_63c8c46401014uhx.html