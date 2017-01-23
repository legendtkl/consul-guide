# 4.6 join

命令：`consul join`。

Consul agent 通过命令 `join` 加入到一个集群中。一个新的 Consul agent 必须加入一个集群的某个成员才能加入到集群中。加入集群的某个成员之后，然后集群中通过 gossip 协议扩散并更新集群成员关系。

如果你没有加入某个集群，那么这个 agent 则自己成为一个孤立的集群，别的节点可以加入它。

agent 可以多次加入其他的 agent。如果一个集群的节点加入另一个集群的某个节点，则这两个集群合并为一个集群。

## 用法
用法：`consul join [optoins] address ...`

如果你想加入多个集群，可以在 join 后面接多个地址。Consul 会尝试加入所有的地址，如果某个地址不能加入，则 join 命令失败。

join 命令的参数都是可选的，下面是可用的参数。
* `-wan:` 对于运行在 server 模式的 agent，它会尝试加入其它在一个 WAN 集群的 server。这样可以在多个 datacenter 之间建立一个 bridge。

* `-rpc-addr:` 指定你想要发送命令的 node 的 RPC 地址。如果不指定，则会检测环境变量 `CONSUL_RPC_ADDR`。如果变量没有设置，默认的 RPC 地址是 `127.0.0.1:8400`。