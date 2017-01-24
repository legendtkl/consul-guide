# Consul KV delete

命令： `consul kv delete`

命令 `kv delete` 从 Consul 的存储中心删除指定的数据。如果数据不存在，则直接返回。

## 用法
用法：`consul kv delete [options] KEY_OR_PREFIX`

### API Options
* `-http-addr=<addr>:` Consul agent 的地址，可以是 IP 地址，也可以是 DNS 地址，但是都必须包含端口号。这个也可以通过环境变量 `CONSUL_HTTP_ADDR` 指定，默认值是 127.0.0.1:8500。
* `-datacenter=<name>:` 查询的 datacenter 名称。如果没有指定这查询指定的 Consul agent 所在的 datacenter。
* `-token=<value>:` 请求使用的 ACL token。这也可以通过 `CONSUL_HTTP_TOKEN` 环境变量指定。如果没有指定，请求默认使用指定的 Consul agent 的 token。
* `-stale:` 允许任何 Consul server (非 leader) 响应 request。使用 stale 可以使系统的延迟更低和吞吐更高，但是会产生 stale 数据。这个选项对非读操作没有影响，默认值是 false。

### KV Delete Options
* `-cas:` Check-And-Set 操作。要配合 `-modify-index` 使用。默认值是 false。
* `-modify-index=<int>:` 键的 ModifyIndex 的值，无符号整数，配合 `-cas` 使用。
* `-recurse:` 递归删除路径下的所有键，默认值为 false。

## 示例
删除键 **redis/config/connections**。
```bash
$ consul kv delete redis/config/connections
Success! Deleted key: redis/config/connections
```

如果键不存在，不会报错。
```bash
$ consul kv delete not-a-real-key
Success! Deleted key: not-a-real-key
```

只有某个键自在给定某个 index 之后没有被修改过才删除。使用参数：`-cas` 和 `-modify-index`。
```bash
$ consul kv get -detailed redis/config/connections | grep ModifyIndex
ModifyIndex 	456

$ consul kv delete -cas -modify-index=123 redis/config/connections
Error! Did not delete key redis/config/connections: CAS failed

$ consul kv delete -cas -modify-index=456 redis/config/connections
Success! Deleted key: redis/config/connections
```

递归删除
```bash
$ consul kv delete -recurse redis/
Success! Deleted keys with prefix: redis/
```

**末尾的斜杠很重要：** 因为 Consul 递归删除键的时候使用的贪心匹配算法。如果你使用键 **foo**，那么会递归删除键：foo, food, football 等。为了确保你删除的是一个目录，末尾一定要加斜杠。

`-cas` 和 `-recurse` 参数不能组合使用，因为你在用一次操作删除多个键。
```bash
$ consul kv delete -cas -recurse redis/
Cannot specify both -case and -recurse!
```