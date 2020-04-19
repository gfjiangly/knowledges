hdfs的目录在/user/lab/路径下，可以在此目录下建立以自己名字命名的目录。常用操作：

列出文件：hadoop dfs -ls /user/lab/ouyanglinshu

上传文件：hadoop dfs -put file.txt /user/lab/ouyanglinshu

下载文件：hadoop dfs -get /user/lab/ouyanglinshu/file.txt

删除文件：hadoop dfs -rm /user/lab/ouyanglinshu/file.txt





产生背景
以文件为基本存储单位的缺点
1、文件大小不同，难以实现负载均衡。
2、处理一个文件时，只能利用一个节点资源，无法动用集群。

HFDS的定义
源自于Google的GFS论文
    发表于2003年10月
    HDFS是GFS克隆版
Hadoop Distributed File System
    易于扩展的分布式文件系统
    运行在大量普通廉价机器上，提供容错机制
    为大量用户提供性能不错的文件存取服务
HDFS的优缺点
优点：
1）高容错性
    数据自动保存多个副本
    副本丢失后，自动恢复
2）适合批处理
    移动计算而非数据
    数据位置暴露给计算框架
3）适合大数据处理
    GB、TB、甚至PB级数据
    百万规模以上的文件数量
    10K+节点规模
4）流式文件访问
    一次性写入，多次读取
    保证数据一致性
5）可构建在廉价机器上
    通过多副本提高可靠性
    提供了容错和恢复机制
不擅长：
1）低延迟与高吞吐率的数据访问 ，比如毫秒级
2）小文件存取
    占用NameNode大量内存 
    寻道时间超过读取时间 
3）并发写入、文件随机修改
    一个文件同一个时间只能有一个写者
    仅支持append
