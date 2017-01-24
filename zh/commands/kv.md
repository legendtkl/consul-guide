# 4.9 Consul kv
命令：**`consul kv`**。

`kv` 命令用来和 Consul 的 KV 存储中心进行命令行交互。它提供了一些插入，更新，读取和删除操作。从 Consul 0.7.1 版本开始支持 `kv` 命令。

KV 存储中心也提供了 [HTTP API](../agent/http_api_kv_store.md)。

## 用法
用法：**`consul kv subcommand`**

对应 Consul 版本的 kv 文档可以使用命令 `consul kv -h` 查看更详细的信息。
```bash
Usage: consul kv <subcommand> [options] [args]

  # ...

Subcommands:

    delete    Removes data from the KV store
    export    Exports part of the KV tree in JSON format
    get       Retrieves or lists data from the KV store
    import    Imports part of the KV tree in JSON format
    put       Sets or updates data in the KV store
```

对于使用子命令（delete, export等）更详细的信息，可以点击下面的链接。
* [delete](kv_delete.md)
* [export](kv_export.md)
* [get](kv_get.md)
* [import](kv_import.md)
* [put](kv_put.md)

## 简单示例
创建或者更新键 `redis/config/connections` 的值为 5.
```bash
$ consul kv put redis/config/connections 5
Success! Data written to: redis/config/connections
```

查询数据
```bash
$ consul kv get redis/config/connections
5
```

查询更详细的信息
```bash
$ consul kv get -detailed redis/config/connections
CreateIndex      336
Flags            0
Key              redis/config/connections
LockIndex        0
ModifyIndex      336
Session          -
Value            5
```

删除数据
```bash
$ consul kv delete redis/config/connections
Success! Data deleted at key: redis/config/connections
```

更详细的信息可以查看子命令的文档。