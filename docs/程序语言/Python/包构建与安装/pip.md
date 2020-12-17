### 临时指定源

根据requirement.txt使用清华源下载安装包到指定目录下：

pip download -i https://pypi.tuna.tsinghua.edu.cn/simple -d /home/gfjiang/packs -r requirement.txt

-i 指定临时源

- https://pypi.org/simple/  默认
- https://test.pypi.org/simple/



### pip 生成和安装requirements.txt

```bash
pip freeze > requirements.txt
pip install -r requirements.txt
```



### 持久修改pip源

#### Linux

修改 ~/.pip/pip.conf (没有就创建一个)， 内容与windows下相同。

#### Windows

直接在user目录中创建一个pip目录，如：C:\Users\xx\pip，新建文件pip.ini，内容如下：

```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

#### 通过命令创建pip.conf文件

```bash
pip3 config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
pip3 config set install.trusted-host pypi.tuna.tsinghua.edu.cn
```





### 安装卸载pytorch

cuda9.0

```bash
pip install torch torchvision
conda install pytorch torchvision cudatoolkit=9.0 -c pytorch
```

cuda10.0

```bash
pip install https://download.pytorch.org/whl/cu100/torch-1.1.0-cp37-cp37m-linux_x86_64.whl
pip install https://download.pytorch.org/whl/cu100/torchvision-0.3.0-cp37-cp37m-linux_x86_64.whl
conda install pytorch torchvision cudatoolkit=10.0 -c pytorch
```

pip注意使用清华pypi源，速度快很多

pip uninstall torch torchvision



缓存位置

Linux and Unix

~/.cache/pip 

Windows

%LocalAppData%\pip\Cache



pip命令

```bash
# 查看包安装信息
pip show 包名

# 升级包
pip install --upgrade 要升级的包名
```



```
#!/usr/bin/env bash

set -x

# 参考这篇 wiki https://wiki.bytedance.net/pages/viewpage.action?pageId=346427717
# 以及这个脚本 https://code.byted.org/nlp/ptx/blob/test_xlmr/runs/pretrain_bert/arnold_script.sh
# multi 和 single的区别在于加了3个字段：
# 1. nnodes 节点个数，也就是机器数: ${ARNOLD_WORKER_NUM}
# 2. node_rank 节点编号  ${ARNOLD_ID}
# 3. master_addr rank0的node的IP ${METIS_WORKER_0_HOST}
# 4. master_port rank0的node的port ${METIS_WORKER_0_PORT}


train_script=${1:-"./vcr/train_end2end.py"}
config=${2:-"./cfgs/vcr/base_q2a_4x16G_fp32.yaml"}
resource_dir=${3:-"/mnt/cephfs_new_wj/datanlp/huangwenguan/vl"}
hdfs_path=${4:-""}

echo "train_script [$train_script]"
echo "config [$config]"
echo "hdfs_path [$hdfs_path]"
echo "resource_dir [$resource_dir]"
echo "nnodes [${ARNOLD_WORKER_NUM}]" 
echo "node_rank [${ARNOLD_ID}]" 
echo "master_addr [${METIS_WORKER_0_HOST}]" 
echo "master_port [${METIS_WORKER_0_PORT}]" 

# export NCCL_IB_DISABLE=0
# export NCCL_IB_HCA=mlx5_2
# export NCCL_IB_GID_INDEX=3
# export NCCL_SOCKET_IFNAME=eth0
# export NCCL_DEBUG=INFO

cd /opt/tiger/VL-BERT

rm -rf data
rm -rf model
rm -rf cfgs
rm -rf common/lib/roi_pooling

if [[ ${resource_dir} == hdfs://* ]]
then
    echo "resource on hdfs: [${resource_dir}]"
    hadoop fs -get ${resource_dir} .
    hadoop fs -get ${resource_dir}/roi_pooling common/lib/roi_pooling
else
    ln -s ${resource_dir}/data data
    ln -s ${resource_dir}/model model
    cp -r ${resource_dir}/roi_pooling common/lib/roi_pooling
fi

if [[ ${hdfs_path} == hdfs://* ]]
then
    hadoop fs -mkdir ${hdfs_path}
fi

export PYTHONPATH=$PYTHONPATH:/opt/tiger/workbench/data_process/

python3 ./scripts/launch.py \
    --nnodes ${ARNOLD_WORKER_NUM} \
    --node_rank ${ARNOLD_ID} \
    --master_addr ${METIS_WORKER_0_HOST} \
    --master_port ${METIS_WORKER_0_PORT} \
    --nproc_per_node ${ARNOLD_WORKER_GPU} \
    "${train_script}" \
    --cfg ${config} \
    
    
def parse_args():
    """
    Helper function parsing the command line options
    @retval ArgumentParser
    """
    parser = ArgumentParser(description="PyTorch distributed training launch "
                                        "helper utilty that will spawn up "
                                        "multiple distributed processes")

    # Optional arguments for the launch helper
    parser.add_argument("--nnodes", type=int, default=1,
                        help="The number of nodes to use for distributed "
                             "training")
    parser.add_argument("--node_rank", type=int, default=0,
                        help="The rank of the node for multi-node distributed "
                             "training")
    parser.add_argument("--nproc_per_node", type=int, default=1,
                        help="The number of processes to launch on each node, "
                             "for GPU training, this is recommended to be set "
                             "to the number of GPUs in your system so that "
                             "each process can be bound to a single GPU.")
    parser.add_argument("--master_addr", default="127.0.0.1", type=str,
                        help="Master node (rank 0)'s address, should be either "
                             "the IP address or the hostname of node 0, for "
                             "single node multi-proc training, the "
                             "--master_addr can simply be 127.0.0.1")
    parser.add_argument("--master_port", default=29500, type=int,
                        help="Master node (rank 0)'s free port that needs to "
                             "be used for communciation during distributed "
                             "training")
    # parser.add_argument("--use_env", default=False, action="store_true",
    #                     help="Use environment variable to pass "
    #                          "'local rank'. For legacy reasons, the default value is False. "
    #                          "If set to True, the script will not pass "
    #                          "--local_rank as argument, and will instead set LOCAL_RANK.")

    # positional
    parser.add_argument("training_script", type=str,
                        help="The full path to the single GPU training "
                             "program/script to be launched in parallel, "
                             "followed by all the arguments for the "
                             "training script")

    # rest from the training program
    parser.add_argument('training_script_args', nargs=REMAINDER)
    return parser.parse_args()


def main():
    args = parse_args()

    # world size in terms of number of processes
    dist_world_size = args.nproc_per_node * args.nnodes

    # set PyTorch distributed related environmental variables
    current_env = os.environ.copy()
    current_env["MASTER_ADDR"] = args.master_addr
    current_env["MASTER_PORT"] = str(args.master_port)
    current_env["WORLD_SIZE"] = str(dist_world_size)

    processes = []

    for local_rank in range(0, args.nproc_per_node):
        # each process's rank
        dist_rank = args.nproc_per_node * args.node_rank + local_rank
        current_env["RANK"] = str(dist_rank)
        current_env["LOCAL_RANK"] = str(local_rank)

        # # spawn the processes
        # if args.use_env:
        #     cmd = [sys.executable, "-u",
        #            args.training_script] + args.training_script_args
        # else:
        #     cmd = [sys.executable,
        #            "-u",
        #            args.training_script,
        #            "--local_rank={}".format(local_rank)] + args.training_script_args

        cmd = [sys.executable, "-u",
               args.training_script] + args.training_script_args + ["--dist"]

        process = subprocess.Popen(cmd, env=current_env)
        processes.append(process)

    for process in processes:
        process.wait()
        if process.returncode != 0:
            raise subprocess.CalledProcessError(returncode=process.returncode,
                                                cmd=process.args)
```