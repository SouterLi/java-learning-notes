# Redis的高可用方案
## Redis Cluster
Redis Cluster是Redis的分布式集群方案，Redis Cluster采用去中心化的分布式架构。

## 哨兵模式
在Redis主从集群之上，建立一个哨兵服务。主要有以下功能：  
- 集群监控，监控redis master和slave节点是否正常工作  
- 消息通知：如果某个redis节点有故障，发送通知给管理员  
- 故障转移：如果master实例挂了，会自动转移到slave 节点上  
- 配置中心：如果发生了故障转移，将新的master的地址发送给client  