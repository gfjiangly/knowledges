

## 使用nvidia gpu

必须安装nvidia-container-toolkit

see: https://github.com/NVIDIA/nvidia-docker#ubuntu-16041804-debian-jessiestretchbuster

```bash
# Add the package repositories
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
sudo systemctl restart docker

# 使用含CUDA镜像创建容器方式
docker run --gpus all --entrypoint=/bin/bash -it <imageid>
# 如果需要nvcc之类工具，需要额外安装
apt-get install -y cuda-core-8-0	# cuda-8.0
```

