## 0安装ssh插件

- Remote Development(2019年发布)

## 1配置私钥

1. 在.ssh目录下，如下图用ssh-keygen命令生成密钥。如有已有密钥，可省略此步骤
2. 然后将生成的id_rsa.pub文件传到远程根目录下.ssh文件夹中

## 2 ssh连接

1. 将id_rsa.pub内容添加到authorized_keys文件中
2. 用ssh命令（ssh username@ip -p port）连接远程主机；或直接添加到配置文件中，然后连接主机

