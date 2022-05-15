# 索引

为什么需要索引？
- 加快查找速度

Mongodb 在 collection 创建时会默认建立一个基于_id的唯一性索引作为 document 的 primary key，这个 index 无法被删除。

## 复合索引


## TTL索引
TTL索引不支持复合索引，不会报错但无效。

参考：
- https://juejin.cn/post/6844903591166083086
- https://docs.mongodb.com/manual/core/index-ttl/
