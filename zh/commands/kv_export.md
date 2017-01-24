# Consul KV export
命令：**`consul kv export`**

命令 `kv export` 用来从 Consul KV 存储中心查询指定前缀的 key/value 数据并生成 json 数据。

## 用法
用法：**`consul kv export [PREFIX]`**

### API Options
* **`-http-addr=<addr>:`** Consul agent 的地址，IP 地址或者 DNS 地址，必须包含端口号。也可以通过环境变量 **CONSUL_HTTP_ADDR** 指定，默认 127.0.0.1:8500。
* **`-datacenter=<name>:`** 查询的 datacenter 名称。如果没有指定，默认查询通过 **http-addr** 指定的 agent 所在的 datacenter。
* **`-token=<value>:`** ACL token。可以通过环境变量 **CONSUL_HTTP_TOKEN** 指定。如果没有指定，默认使用指定的 Consul agent 的 token。
* **`stale:`** 允许任何 Consul server (非 leader) 响应 request。使用 stale 可以使系统的延迟更低和吞吐更高，但是会产生 stale 数据。这个选项对非读操作没有影响，默认值是 false。

## 示例
导出 "vault/" 下的 kv 数据。
```text
$ consul kv export vault/
# JSON output
```