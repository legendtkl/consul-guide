# 3.2 Consensus Protocol

Consul使用了一种共识协议来提供CAP中的一致性。这个共识协议基于Raft协议。

## Raft协议概览

Raft是一个基于Paxos的共识算法。与Paxos相比，Raft具有更少的状态和更简单易懂的算法。

以下是一些在讨论Raft时必须知道的关键术语：

* Log - Raft系统中的主要工作单元是日志条目。一致性问题可以通过复制日志解决。一条日志是一组有序的条目序列。如果所有成员都对条目和顺序一致，那么我们认为日志是一致的。
* FSM - 有限状态机。
* Peer set
* Quorum
* Commited Entry
* Leader

## Raft in Consul

只有Consul server节点参与Raft协议。

## 一致性模式



## 部署表格



