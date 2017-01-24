# Consul KV put
命令：**`consul kv put`**

命令 `kv put` 向 KV 存储中心写入数据。

## 用法
用法：**`consul kv put [options] KEY [DATA]`**

### API Options
* **`-http-addr=<addr>:`** Consul agent 的地址，IP 地址或者 DNS 地址，必须包含端口号。也可以通过环境变量 **CONSUL_HTTP_ADDR** 指定，默认 127.0.0.1:8500。
* **`-datacenter=<name>:`** 查询的 datacenter 名称。如果没有指定，默认查询通过 **http-addr** 指定的 agent 所在的 datacenter。
* **`-token=<value>:`** ACL token。可以通过环境变量 **CONSUL_HTTP_TOKEN** 指定。如果没有指定，默认使用指定的 Consul agent 的 token。
* **`stale:`** 允许任何 Consul server (非 leader) 响应 request。使用 stale 可以使系统的延迟更低和吞吐更高，但是会产生 stale 数据。这个选项对非读操作没有影响，默认值是 false。

### KV Put Options
* **`acquire:`** 在 key 上分配一个锁。如果 key 不存在，则会先创建再加锁。这个时候 session 必须是存在的，通过 -session 参数指定。默认值是 false。
* **`base64:`** 认为数据为 base64 编码过的数据。默认值为 false。
* **`-cas:`** 执行 Check-And-Set 操作。需要配合 -modify-index 一起使用。默认值为 false。
* **`-flags=<nil>:`** 指定给 kv 的无符号整数。Consul 并不会读取这个数，所以 client 可以为了自己方便来使用 flag。默认值为 0。
* **`-modify-index=<nil>:`** key 的 ModifyIndex，配合 -cas 使用。
* **`-release:`** 释放 key 上的锁。这要求 -session 参数被设置。默认值是 false。
* **`-session=<string>:`** 用户指定的 session 标志。配合 -acquire 和 -release 使用。默认为空。

## 示例
创建。
```text
$ consul kv put redis/config/connections 5
Success! Data written to: redis/config/connections
```

如果只指定 key，没有指定 value，则 value 为空。
```text
$ consul kv put redis/config/connections
Success! Data written to: redis/config/connections
```

如果使用了参数 `-base64`，value 在存储之前会进行解码。
```text
$ consul kv put -base64 foo/encoded aGVsbG8gd29ybGQK
Success! Data written to: foo/encoded
```

***小心数据重写！*** 上面的数据可能会导致数据被写成空。

对于大量或者敏感数据，可以从文件中读取。
```text
$ consul kv put redis/config/password @password.txt
Success! Data written to: redis/config/connections
```

也可以从标准输入中读。
```text
$ echo "5" | consul kv put redis/config/password -
Success! Data written to: redis/config/connections

$ consul kv put redis/config/password -
5
<CTRL+D>
Success! Data written to: redis/config/connections
```

对于秘密和敏感数据，可以考虑使用 **HashiCorp's Vault**。

在某个 key 没有备修改过 ModifyIndex 的情况下才更新。使用参数 `-cas` 和 `-modify-index`。
```text
$ consul kv get -detailed redis/config/connections | grep ModifyIndex
ModifyIndex      456

$ consul kv put -cas -modify-index=123 redis/config/connections 10
Error! Did not write to redis/config/connections: CAS failed

$ consul kv put -cas -modify-index=456 redis/config/connections 10
Success! Data written to: redis/config/connections
```

指定 key 上的 flag，使用参数 `-flags`。用户可以自己决定如何使用。
```text
$ consul kv put -flags=42 redis/config/password s3cr3t
Success! Data written to: redis/config/password
```

使用参数 `-acquire` 和 `-session` 创建锁。 session 必须已经存在。
```text
$ consul kv put -acquire -session=abc123 redis/lock/update
Success! Lock acquired on: redis/lock/update
```

释放锁
```text
$ consul kv put -release -session=acb123 redis/lock/update
Success! Lock released on: redis/lock/update
```

**Warning!** 如果你想在这些 low-level 的操作原语上构建锁机制，不妨看看命令 lock，它提供了 high-level 的操作。