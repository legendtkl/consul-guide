# Consul KV get

命令：**`consul kv get`**

`kv get` 命令用来从 Consul KV 存储中心取值。如果指定的键名称不存在，会返回一个错误。（这个地方和 delete 稍有区别。）如果指定的键存在，但是没有值，则返回空。如果没有指定名称，默认使用空串 ""，KV 存储中心的 root。

## 用法
用法：**`consul kv get [options] [KEY_OR_PREFIX]`**

### API Options
* **`-http-addr=<addr>:`** Consul agent 的地址，IP 地址或者 DNS 地址，必须包含端口号。也可以通过环境变量 **CONSUL_HTTP_ADDR** 指定，默认 127.0.0.1:8500。
* **`-datacenter=<name>:`** 查询的 datacenter 名称。如果没有指定，默认查询通过 **http-addr** 指定的 agent 所在的 datacenter。
* **`-token=<value>:`** ACL token。可以通过环境变量 **CONSUL_HTTP_TOKEN** 指定。如果没有指定，默认使用指定的 Consul agent 的 token。
* **`stale:`** 允许任何 Consul server (非 leader) 响应 request。使用 stale 可以使系统的延迟更低和吞吐更高，但是会产生 stale 数据。这个选项对非读操作没有影响，默认值是 false。

### KV Get Options
* **`-base64:`** 使用 base64 对值进行编码，默认是 false。
* **`-detailed:`** 除了键的值，还提供其他更多的元信息，比如：ModifyIndex 等。默认值是 false。
* **`-keys:`** 列出满足指定前缀的所有键，但是不列出值。当你只需要键的名称的时候，这个参数会比较有用。这个参数经常和 separtor 配合使用。默认值为 false。
* **`-recurse:`** 递归查询所有满足指定前缀的键。默认值为 false。
* **`-separator=<string>:`** 指定键的分隔符，和 keys 结合使用的时候，当遇到 separator 则不再继续往下找（注：这里使用 separator 这个词并不一定合适）。默认为 "/"，这个参数要配合 keys 参数使用。

### 示例
查询键 redis/config/connections 的值。
```text
$ consul kv get redis/config/connections
5
```
返回存储在 Consul 的原始值。要想查看详细信息，可以指定 "-detailed" 参数。
```text
$ consul kv get -detailed redis/config/connections
CreateIndex      336
Flags            0
Key              redis/config/connections
LockIndex        0
ModifyIndex      336
Session          -
Value            5
```

如果指定的键不存在，返回错误。
```text
$ consul kv get not-a-real-key
Error! No key exists at: not-a-real-key
```

列出满足指定前缀的所有的键，使用 "-recurse" 参数。
```text
$ consul kv get -recurse redis/
redis/config/connections:5
redis/config/cpu:128
redis/config/memory:512
```

当然也可以列出所有键的详细信息。
```text
$ consul kv get -recurse -detailed redis
CreateIndex      336
Flags            0
Key              redis/config/connections
LockIndex        0
ModifyIndex      336
Session          -
Value            5

CreateIndex      472
Flags            0
Key              redis/config/cpu
LockIndex        0
ModifyIndex      472
Session          -
Value            128

CreateIndex      471
Flags            0
Key              redis/config/memory
LockIndex        0
ModifyIndex      471
Session          -
Value            512
```

列出前缀是 "redis/config/" 的所有键。
```text
$ consul kv get -keys redis/config/
redis/config/connections
redis/config/cpu
redis/config/memory
```

keys 操作默认往下匹配到 "/"，就不再往下找。我们可以自定义 separator。
```text
$ consul kv get -keys -separator="c" redis
redis/c
```

同时，你可以将 separator 设置成空串来关闭 separator 的功能。
```text
$ consul kv get -keys -separator="" redis
redis/config/connections
redis/config/cpu
redis/config/memory
```

列出 root 下所有的键。
```text
$ consul kv get -keys
memcached/
redis/
```