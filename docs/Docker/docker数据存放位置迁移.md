### 1 停止docker服务

```bash
systemctl stop docker
# or
service docker stop
```



### 2 迁移数据

迁移后的docker路径：/home/docker/lib/

```bash
mkdir -p /home/docker/lib
rsync -avz /var/lib/docker/ /home/docker/lib/
```



### 3 修改docker配置文件

 配置 /etc/systemd/system/docker.service.d/devicemapper.conf 

查看/etc/systemd/system/docker.service.d目录及devicemapper.conf是否存在。如果不存在，就新建 

```bash
mkdir -p /etc/systemd/system/docker.service.d/
vi /etc/systemd/system/docker.service.d/devicemapper.conf
```

 devicemapper.conf添加如下内容： 

```ruby
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd  --graph=/home/docker/lib/docker
```



### 4 重启docker服务

```bash
systemctl daemon-reload
systemctl restart docker
systemctl enable docker
```



### 5 确认数据同步成功

```bash
# 1 确认Docker Root Dir修改是否已经生效
docker info
# 2 确认镜像和容器有无丢失
docker images
docker ps -all
# 3 确认无误后，删除/var/lib/docker
rm -rf /var/lib/docker/
```



see:

https://www.jianshu.com/p/93a1ad47503b