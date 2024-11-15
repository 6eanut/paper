# GAM

## 摘要

背景：高性能网络互联技术提高了节点间通信的速度，为DSM实现节点间缓存一致性提供了机会

做了什么：提出了GAM

* 通过RDMA提供directory-based缓存一致性协议
* 管理节点上的空闲内存，提供统一的内存模型，提供API用于内存操作
* 为了将写操作从关键执行路径中移除，GAM允许写操作和后面的读写操作进行重排序，实现部分存储顺序PSO内存一致性
* 轻量级日志以提供故障恢复能力
* 在GAM上构建了事务引擎和分布式哈希表

测试：测试GAM在不同负载下的读写锁性能，对事务引擎和分布式哈希表进行测试

## 简介

shared-memory和shared-nothing对比

* 前者，提供统一全局内存抽象，便于开发人员；负载均衡
* 后者，键值存储系统，易于扩展

现有DSM提供缓存来缓冲远程内存访问，以减少网络延迟；但是为了维持缓存数据一致性，需要同步原语来更新缓存中的数据，开销过大；而且这需要程序员来完成

现在RDMA技术几乎接近本地访存、甚至比NUMA的吞吐量都大，但是还是需要程序员来完成同步原语

一种方法是不用缓存，直接向数据所在节点进行读写，但是这样延迟太太大了
