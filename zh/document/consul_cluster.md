# 1.4.4 Consul 集群

前面我们简单运行了单个节点的 agent，并且注册了服务，也演示如何查询。这一节我们将搭建了一个 Consul 集群。

当一个 Consul agent 启动的时候，它只是一个孤立的节点，并不知道其它节点的任何信息。为了获得集群中其它成员，这个 agent 需要 join 现存的集群中。加入集群很简单，只需要知道集群的一个成员节点就行了。加入之后，这个成员节点将通过 gossip 协议扩散新的成员信息。一个 Consul agent 可以加入任何 agent，不限于 server 模式运行的 agent 节点。

## 启动 agent
为了模拟集群，我们使用 vagrant 启动两个节点。vagrantfile 可以从 [demo section of the Consul repo](https://github.com/hashicorp/consul/tree/master/demo/vagrant-cluster) 下载。

首先启动两个节点
```bash
$ vagrant up
```
启动之后，ssh 登录。
```bash
$ vagrant ssh n1
```

我们之前的例子中，使用参数 `-dev` 参数快速启动了一个 server。然而为了演示集群，`-dev` 参数是不够的。下面我们将使用具体的参数。

集群中的每个节点都有一个名字，Consul 默认使用机器的 hostname，但是我们之后会使用 `-node` 参数显示指定节点的名字。

我们也会显示指定一个 `bind` 地址：这个是 Consul 监听的地址，并且要保证这个地址对其他节点是可访问的。然而 `bind` 地址并不是一定需要的，只是最好提供一个。Consul 默认会监听机器上的 IPv4 地址，但是如果发现了多个地址，Consul 则会启动失败。因为生产环境中的 server 通常有多个地址，所以这个时候指定 `bind` 地址是十分有必要的。

第一个启动的节点会是集群中的单独的 server，我们通过参数 `-server` 启动。

`-bootstrap-expect` 参数表示我们希望整个集群中的 server 个数。当集群中的 server 个数还没有到达指定的个数的时候，boostrapping 不会进行。关于 bootstrapping 的更多内容，可以参考 [bootstrapping 指南](../guides/bootstrapping.md)。

最后我们还会指定 `-config-dir` 参数，指定我们的配置文件目录。

综上，我们的 Consul agent 命令如下。
```bash
vagrant@n1:~$ consul agent -server -bootstrap-expect=1 \
    -data-dir=/tmp/consul -node=agent-one -bind=172.20.20.10 \
    -config-dir=/etc/consul.d
...
```
我们通过另一个终端 ssh 登录到另一个 vagrant 节点中。
```bash
$ vagrant ssh n2
```

我们期望集群中只有一个 server，第二个节点不需要再以 server 模式启动，而是以 client 模式。
```bash
vagrant@n2:~$ consul agent -data-dir=/tmp/consul -node=agent-two \
    -bind=172.20.20.11 -config-dir=/etc/consul.d
...
```

这时候我们就有了一个两个 Consul agent 节点的集群：一个 server，一个 client。然而，此时两个节点还是互相不知道对方的存在，你可以通过 `consul members` 命令查看。

## 加入集群
我们可以通过下面的命令，将一个 agent 加入到另一个。
```bash
$ vagrant ssh n1
...
vagrant@n1:~$ consul join 172.20.20.11
Successfully joined cluster by contacting 1 nodes.
```
这时，你会在输出的 log 中看到 join 信息。再执行命令 `consul members` 会发现成员变成两个了。
```bash
vagrant@n2:~$ consul members
Node       Address            Status  Type    Build  Protocol
agent-two  172.20.20.11:8301  alive   client  0.5.0  2
agent-one  172.20.20.10:8301  alive   server  0.5.0  2
```

**Remember:** 要加入集群，Consul agent 只需要知道集群中的任一节点。加入之后，这个节点就会通过 gossip 协议将新成员的信息通知集群内的其他节点。

## 启动时自动加入一个集群
当集群中新启动一个新节点的时候，它应该自动加入集群，而不是需要人工干预。你可以使用工具 `Atlas by HashiCorp` 和参数 `-atlas-join`，示例如下：
```bash
$ consul agent -atlas-join \
  -atlas=ATLAS_USERNAME/infrastructure \
  -atlas-token="YOUR_ATLAS_TOKEN"
```

这种方式需要先注册一个 Atlas 账户得到用户名和 token，然后替换掉 Consul 配置文件中对应的值。这时候再启动 Consul agent 的时候，它就会自动加入集群，而不需要任何 hardcode。

hardcode 的方法是启动的时候通过 `-join` 参数或者 `start_join` 设置连接到任何 hardcode 的地址。

## 查询节点
类似于查询服务，你也可以通过 DNS 或者 HTTP API 查询节点。

DNS API，节点查询的格式为 `NAME.node.consul` 或者 `NAME.node.DATACENTER.consul`。如果 datacenter 没有写的话，Consul 只会查询本地 datacenter。

举个例子，在节点 `agent-one` 上查询节点 `agent-two`。
```bash
vagrant@n1:~$ dig @127.0.0.1 -p 8600 agent-two.node.consul
...

;; QUESTION SECTION:
;agent-two.node.consul. IN  A

;; ANSWER SECTION:
agent-two.node.consul.  0 IN    A   172.20.20.11
```
查询节点对于系统管理员非常有用。比如，知道了节点地址就可以通过 SSH 登录。

## 离开集群
要离开一个集群，你可以通过 `Ctrl-C` （gracefully quit）或者干掉某个 agent。第一种方式节点会转换到 ***left*** 状态，而第二种方式其他节点会认为它 ***fail***。这两种方式的区别在于：***fail*** 状态，其他节点会不断重试；而 ***left*** 状态，集群中就像少了这个节点。

## 下一步
我们现在已经启动了一个多节点的 Consul 的集群。为了让我们的服务更加的健壮，让我们来看看健康监测。