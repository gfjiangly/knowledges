安装ssh服务：

sudo apt-get install openssh-server



/etc/init.d/ssh stop
/etc/init.d/ssh start



查看ssh server是否开启：
ps -e | grep ssh



root用户远程登录,允许无密码登录

```bash
sudo nano /etc/ssh/sshd_config
# 编辑
PermitRootLogin yes
PermitEmptyPasswords yes
```

