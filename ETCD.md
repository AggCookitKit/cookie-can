# ETCD

https://www.cnblogs.com/passzhang/p/12556518.html

用于解决**集群**管理中OS升级时的**分布式并发控制**、**配置文件的存储与分发**问题

**高可用、强一致**的**key-value**数据存储服务

## 技术架构&内部机制

一般3-5节点，raft一致性算法保持一致

选举leader负责数据同步和分发

leader故障后重新选举leader



quorum = (n+1)/2

3个节点容忍1个故障

5个节点容忍2个故障

特点：

- 支持通过ETCD客户端访问

- 接口简单，支持通过http协议查询
- key-value
- 支持订阅
- 

APIs：

- Put(key,value)/Delete(key)
- Get(key)/Get(keyFrom,keyEnd)
- Watch(key/keyPrefix)
- Transactions(if/then/else ops).Commit()
- Leases: Grant/Revoke/KeepAlive

数据版本号机制：

- term(64bit，全局单调递增)：集群的leader任期，leader节点出现故障、leader网络存在问题、集群重新拉起等情况发生时，leader会发生切换，此时term值+1

- revision(64bit，全局单调递增)：全局数据的版本，数据发生变更（创建、修改、删除），reversion+1
- KeyValue中：
  - create_revision：数据创建时操作对应的版本号
  - mod_revision：数据修改时操作对应的版本号
  - version：该KeyValue被修改的次数

ETCD MVCC & Streaming Watch

- 一个数据有多个版本，支持多版本维护
- 通过定期的Compaction来清理历史数据
- 