# 1.4.6 Key/Value Data
除了提供服务发现和健康检测，Consul 还提供了一种简单的方法使用 K/V 存储。可以用来存储动态配置、服务协作、选主以及很多有趣的事情。

使用 K/V 存储之前需要保证有一个 agent 在运行。

## 简单用法
我们下面演示一下如何使用 K/V 存储，很简单。K/V 存储提供了两种方式：HTTP API 和 Consul KV cli。下面演示如何使用 cli。关于 HTTP API 可以参考 Agent 那一章。

我们先请求路径 `redis/config/mincons` 下面的 key 的值。
```bash
$ consul kv get redis/config/minconns
Error! No key exists at: redis/config/minconns
```
如你所见，没有得到结果。因为我们还没有存放数据。下面我们使用 `put` 命令存数据。
```bash
$ consul kv put redis/config/minconns 1
Success! Data written to: redis/config/minconns

$ consul kv put redis/config/maxconns 25
Success! Data written to: redis/config/maxconns

$ consul kv put -flags=42 redis/config/users/admin abcd1234
Success! Data written to: redis/config/users/admin
```

查询
```bash
$ consul kv get redis/config/minconns
1
```

Consul 还记录了 key 的其他元数据，可以通过 `-detailed` 参数查看。
```bash
$ consul kv get -detailed redis/config/minconns
CreateIndex      207
Flags            0
Key              redis/config/minconns
LockIndex        0
ModifyIndex      207
Session          -
Value            1
```

比如参数 `flag`，client 可以自定义一些有意义的值。

通过选项 `recurse`，我们可以列出所有的 key/value。结果以字典序排列。
```bash
$ consul kv get -recurse
redis/config/maxconns:25
redis/config/minconns:1
redis/config/users/admin:abcd1234
```

删除
```bash
$ consul kv delete redis/config/minconns
Success! Deleted key: redis/config/minconns
```

通过选项 `recurse` 选项可以删除共同前缀的所有 key。
```bash
$ consul kv delete -recurse redis
Success! Deleted keys with prefix: redis
```

更新操作可以通过 `put` 新 value 实现。
```bash
$ consul kv put foo bar

$ consul kv get foo
bar

$ consul kv put foo zip

$ consul kv get foo
zip
```

Consul 支持 key 的原子更新，使用 `Check-And-Set` 操作，参数 `-cas`.
```bash
$ consul kv put -cas -modify-index=123 foo bar
Success! Data written to: foo

$ consul kv put -cas -modify-index=123 foo bar
Error! Did not write to foo: CAS failed
```
上面的例子，第一个 CAS 更新成功了，因为 index 的值为 123。第一个操作失败，因为 index 值不再是 123。

## 下一步
我们这里只是简单列了几个例子，更详细的信息请参考 [K/V CLI](../commands/kv.md)

下一节，我们会介绍一下 Consul 的 web UI。
