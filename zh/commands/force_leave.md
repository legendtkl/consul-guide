# 4.5 consul force leave
命令：`consul force-leave`

`force-leave` 命令强制 Consul 集群中的一个成员进入 `left` 状态。如果成员是活着的，它最终会重新加入到集群中。这个命令的真实目的是摘除 `failed` 节点。（注：节点有两种状态 `left` 状态和 `failed` 状态。对于 `failed` 状态的节点，Consul 会不断尝试重连，而 `left` 状态的节点不会。）

Consul 会不断尝试重连 `failed` 状态的节点，以防止是网络原因。在一定时间之后（默认是 72 小时），Consul 会停止重试。`force-leave` 命令加快了 `failed` 状态的节点转变到 `left` 状态的过程。

这个命令对于 Consul server 节点尤其有用，因为 server 节点参与 Raft 选举。

## 用法
用法：`consul force-leave [options] node`

下面是可选的参数。
* `-rpc-addr:` 指定你想 force-leave 的 node 的 RPC 地址。如果不指定，则会检测环境变量 `CONSUL_RPC_ADDR`。如果变量没有设置，默认的 RPC 地址是 `127.0.0.1:8400`。