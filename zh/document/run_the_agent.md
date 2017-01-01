# 1.4.2 运行 agent
Consul 安装之后，我们就可以安装 agent 了。agent 可以以两种模式运行：server 和 client 模式。每个数据中心必须有至少一个 server，但是建议一个数据中心的 server 个数为3或者5个。因为只有一个 server 的话，容错能力太差了。

除了上面以 server 模式运行的机器外，其他所有的 agent 都以 client 模式运行。client 模式运行的 agent 是一个非常轻量级的程序，负责服务注册、健康监测和请求转发。对于集群中的每个节点都需要运行 agent。

数据中心的 bootstrapping 的更多信息，可以参考[Bootstrapping](../guides/bootstrapping.md)

## 启动 agent
简单起见，我们以 dev 模式启动 agent。这种模式可以简单快速地在单个节点上部署上 Consul 环境。但是在生产环境上并不建议这么做，因为 dev 模式不会进行任何持久化。

```bash
-$ consul agent -dev
==> Starting Consul agent...
==> Starting Consul agent RPC...
==> Consul agent running!
           Version: 'v0.7.0'
         Node name: 'Armons-MacBook-Air'
        Datacenter: 'dc1'
            Server: true (bootstrap: false)
       Client Addr: 127.0.0.1 (HTTP: 8500, HTTPS: -1, DNS: 8600, RPC: 8400)
      Cluster Addr: 127.0.0.1 (LAN: 8301, WAN: 8302)
    Gossip encrypt: false, RPC-TLS: false, TLS-Incoming: false
             Atlas: <disabled>

==> Log data will now stream in as it occurs:

    2016/09/15 10:21:10 [INFO] raft: Initial configuration (index=1): [{Suffrage:Voter ID:127.0.0.1:8300 Address:127.0.0.1:8300}]
    2016/09/15 10:21:10 [INFO] raft: Node at 127.0.0.1:8300 [Follower] entering Follower state (Leader: "")
    2016/09/15 10:21:10 [INFO] serf: EventMemberJoin: Armons-MacBook-Air 127.0.0.1
    2016/09/15 10:21:10 [INFO] serf: EventMemberJoin: Armons-MacBook-Air.dc1 127.0.0.1
    2016/09/15 10:21:10 [INFO] consul: Adding LAN server Armons-MacBook-Air (Addr: tcp/127.0.0.1:8300) (DC: dc1)
    2016/09/15 10:21:10 [INFO] consul: Adding WAN server Armons-MacBook-Air.dc1 (Addr: tcp/127.0.0.1:8300) (DC: dc1)
    2016/09/15 10:21:13 [DEBUG] http: Request GET /v1/agent/services (180.708µs) from=127.0.0.1:52369
    2016/09/15 10:21:13 [DEBUG] http: Request GET /v1/agent/services (15.548µs) from=127.0.0.1:52369
    2016/09/15 10:21:17 [WARN] raft: Heartbeat timeout from "" reached, starting election
    2016/09/15 10:21:17 [INFO] raft: Node at 127.0.0.1:8300 [Candidate] entering Candidate state in term 2
    2016/09/15 10:21:17 [DEBUG] raft: Votes needed: 1
    2016/09/15 10:21:17 [DEBUG] raft: Vote granted from 127.0.0.1:8300 in term 2. Tally: 1
    2016/09/15 10:21:17 [INFO] raft: Election won. Tally: 1
    2016/09/15 10:21:17 [INFO] raft: Node at 127.0.0.1:8300 [Leader] entering Leader state
    2016/09/15 10:21:17 [INFO] consul: cluster leadership acquired
    2016/09/15 10:21:17 [DEBUG] consul: reset tombstone GC to index 3
    2016/09/15 10:21:17 [INFO] consul: New leader elected: Armons-MacBook-Air
    2016/09/15 10:21:17 [INFO] consul: member 'Armons-MacBook-Air' joined, marking health alive
    2016/09/15 10:21:17 [INFO] agent: Synced service 'consul'
```

从上面我们可以看出，consul agent 已经启动并且输出了一些日志数据。从日志数据中，你可以看到 agent 以 server 模式启动并且集群完成了选主。另外，本地成员被标记为 “healthy”。

**OS X 用户请注意**: consul 会使用你的 hostname 作为默认的节点名字。如果 hostname 涉及到一些 DNS 查询可能会出问题。这时候可以通过参数 `-node` 显示地指定节点名字。

## 节点成员
运行命令 `consul members` 就能看到 Consul 集群的所有成员了。由于我们上面只启动了单个节点，现在成员列表里面只有一个成员。
```bash
$ consul members
Node                Address            Status  Type    Build     Protocol  DC
Armons-MacBook-Air  172.20.20.11:8301  alive   server  0.6.1dev  2
```
上面的输出信息包括节点名称、地址、健康状态、运行模式以及一些版本信息。更详细的信息可以通过参数 `-detailed` 查看。

`members` 命令的输出基于 gossip 协议，满足最终一致性。也就是说，你看到的信息可能和真实的节点状态有一些差异。为了查看到真正的节点状态，可以使用下面的 HTTP API 请求。

```bash
$ curl localhost:8500/v1/catalog/nodes
[{"Node":"Armons-MacBook-Air","Address":"127.0.0.1","TaggedAddresses":{"lan":"127.0.0.1","wan":"127.0.0.1"},"CreateIndex":4,"ModifyIndex":110}]
```

除了 HTTP API，还可以使用 DNS 查询的方式。值得注意的时候你需要将你的 DNS lookups 指向 Consul agent 的默认运行在8600端口的 DNS 服务器。
```bash
$ dig @127.0.0.1 -p 8600 Armons-MacBook-Air.node.consul
...

;; QUESTION SECTION:
;Armons-MacBook-Air.node.consul.    IN  A

;; ANSWER SECTION:
Armons-MacBook-Air.node.consul. 0 IN    A   127.0.0.1
```

## 停止 agent
你可以使用 `Ctrl+C` 的方式去停止 agent。停掉了 agent 之后，你会从日志信息里看见这个节点离开了集群。

当某个节点的 agent 停止的时候，Consul 会通知其他的集群成员。如果你强制杀死 agent 进程，其他集群成员会检测到这个节点 ***failed***。当一个节点离开的时候，部署在其上的服务以及对节点和服务的检测都将从 catalog 中移除。当一个成员 fail 的时候，它的健康状态会被标记为危险的，当时并不会从 catalog 中移除。Consul 会自动尝试重连失败的节点，失败的节点有可能重连回来。

另外，如果一个节点是以 server 模式运行的，离开会稍微复杂一点，因为涉及到强一致性协议。详细信息，可以查看 [guide](../guides/README.md).

## 下一步
agent 已经启动了，我们在上面运行一些服务吧。