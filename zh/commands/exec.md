# 4.4 exec

命令：`consul exec`

通过 `exec` 命令我们可以远程执行一些命令。比如，在所有提供 `web` 服务的机器上执行 `uptime` 命令。

远程执行的原理是在 KV 存储中存储一个 job，consul 通过 [event](event.md) 通知 agent job 信息，传播协议为 gossip 协议。这是一种尽力交付（best-effort），并不保证一定执行。

尽管 event 只依赖于 gossip，但是远程执行依赖于 KV 存储。所以 `exec` 命令在 Consul 发生运行中断的时候不能被正确运行。

**verbose output warning:** 谨慎使用 verbose output 参数，因为它会产生大量的输出。这些输出信息会通过 Consul server 和 Raft 协议写到 KV 存储中，所以这中间会有大量的数据传输，可能会导致集群不可用。

## 用法
用法：`consul exec [options] [-|command...]`

必选的参数只有需要远程执行的命令。命令可以直接写到后面，也可以通过 `-` 指定。

可选的参数如下：
* `-http-addr:` 你想发送命令的 agent 地址。如果没有指定这个参数，默认连接 `127.0.0.1:8500`，即 Consul agent 的默认 HTTP 地址。
* `-datacenter:` 查询的 datacenter。默认使用运行命令的 agent 的 datacenter。版本 0.4 也只支持默认的。
* `-prefix:` KV 存储中心用来存储请求数据的 Key 的前缀。默认值是 `_rexec`。
* `-node:` 过滤 evaluate event 的节点的正则表达式。
* `-service:` 过滤指定服务的节点的正则表达式。
* `-tag:` 通过指定 tag 的正则表达式来过滤服务节点。这个命令必须配合 `-service` 使用，比如 `-service mysql -tag secondary`。
* `-wait:` （to be refined）
* `-wait-repl:` 向 replicatoin 写入 job 之后的等待时间。这一般是一个自适应的值，默认 200 ms。
* `-verbose:` 开启 verbose output。
* `-token:` 请求时使用的 ACL token。token 对于 KV 存储中心的 `_rexec` event 必须要有读写权限。