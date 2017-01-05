# 4.3 event
命令：`consul event`

`event` 命令提供了一种将某一种 event 应用到整个数据中心的机制。对于 Consul 来说，这些 event 是未知的，但是可以用来做自动化部署、重启服务等。event 可以通过 [watch](watch.md) 来处理。

event 通过 gossip 协议传播。

尽管这些细节对于使用 event 没有影响，但是理解其原理还是有点用的。gossip 层面会尽力传递 event，但是并不**保证交付**。不像大部分 Consul 数据通过强一致性协议进行复制，event 数据是通过 gossip 进行的 p2p 传递。这也就意味着这些数据不会进行持久化也不保证顺序。然而这么做的好处在于就算没有 server 节点或者节点故障，event 还是可以使用。

gossip 协议限制了用户 event 数据大小，但是很难给出一个具体的值，因为它依赖于各种各样的参数。可以肯定的是，event 的有效负载应该保证非常小（< 100 bytes），event 过大会返回一个错误。

## 用法
用法：`consul event [options] [payload]`

对于 option，必填的参数是 `-name`，指定 event 的名字。payload 参数可选。

下面是可用的参数：
* `-http-addr`: 你想要将命令发送给的 agent 的 HTTP server 地址。如果没有指定，将使用 `127.0.0.1:8500`，也就是 Consul agent 的默认 HTTP 地址。
* `-datacenter`: 查询的dc，默认是当前 agent 所在的dc。
* `-name`: event 的名字。
* `-node`: 正则表达式，用来过滤执行此 event 的节点。
* `-service`: 正则表达式，用来过滤特定的服务。
* `-tag`: 正则表达式，用来过滤特定的 tag。必须和 `-service` 一起使用，比如 `-service mysql -tag secondary`。
* `-token`: 当应用 event 时的 ACL token。token 必须要指定 event 的写权限。
