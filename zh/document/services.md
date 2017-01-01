# 1.4.3 服务注册
前面我们启动了 agent，并且查看了集群成员，并且介绍了 HTTP API 和 DNS 的方式。这一节，我们将注册我们的第一个服务，并且进行查询。

## 服务定义
服务注册有两种方式：配置文件和 HTTP API。

配置文件是最常用的方式，我们这里主要使用这种方式来演示。

首先，建一个目录用来存放 Consul 配置。Consul 将加载这个目录下的所有配置文件，所以我们不妨使用 Unix 系统典型的命名方式 `/etc/consul.d`(文件目录可以自己定义)。

```bash
sudo mkdir /etc/consul.d
```

然后，我们在配置文件中定义我们的服务。假设我们已经有一个服务 “web” 运行在 80 端口。另外，我们将给它分配一个 tag 名字来方便查询。
```bash
$ echo '{"service": {"name": "web", "tags": ["rails"], "port": 80}}' \
    | sudo tee /etc/consul.d/web.json
```

现在重启 agent。
```bash
$ consul agent -dev -config-dir=/etc/consul.d
==> Starting Consul agent...
...
    [INFO] agent: Synced service 'web'
...
```
重启日志里面可以看出 agent 同步了 web 服务，也就是 agent 从配置文件中载入了服务配置信息，并且注册成功了。

如果你想注册多个服务，那么只需要在 Consul 配置目录下创建多个配置文件。

## 查询服务
一旦 agent 启动，并且服务被同步成功，我们就能够通过 DNS 或者 HTTP API 来查询服务信息了。

### DNS API
对于 DNS API，服务的 DNS 名字是 `NAME.service.consul`。名字默认在 `consul` 名字空间下。 `service` 子域意思是我们要查询服务，最后 `Name` 表明了服务的名字。

对于我们注册的 web 服务，我们可以通过下面的方式查询。
```bash
$ dig @127.0.0.1 -p 8600 web.service.consul
...

;; QUESTION SECTION:
;web.service.consul.        IN  A

;; ANSWER SECTION:
web.service.consul. 0   IN  A   172.20.20.11
```

从输出可以看到，一条 `A` 记录连同 IP 地址被返回了。`A` 记录只包含 IP 地址。

要查询包括端口地址，需要查询 `SRV` 记录。
```bash
$ dig @127.0.0.1 -p 8600 web.service.consul SRV
...

;; QUESTION SECTION:
;web.service.consul.        IN  SRV

;; ANSWER SECTION:
web.service.consul. 0   IN  SRV 1 1 80 Armons-MacBook-Air.node.dc1.consul.

;; ADDITIONAL SECTION:
Armons-MacBook-Air.node.dc1.consul. 0 IN A  172.20.20.11
```
`SRV` 记录返回了 web 服务运行的端口以及节点 `Armons-MacBook-Air.node.dc1.consul`。然后下面紧接着的是一条对应节点的 `A` 记录。

最后，我们使用 DNS API 去通过 tag 查询过滤。基于 tag 的服务查询形式为 `TAG.NAME.service.consul`。例子如下。
```bash
$ dig @127.0.0.1 -p 8600 rails.web.service.consul
...

;; QUESTION SECTION:
;rails.web.service.consul.      IN  A

;; ANSWER SECTION:
rails.web.service.consul.   0   IN  A   172.20.20.11
```

### HTTP API
除了 DNS API，我们还可以通过 HTTP API 去查询服务。
```bash
$ curl http://localhost:8500/v1/catalog/service/web
[{"Node":"Armons-MacBook-Air","Address":"172.20.20.11","ServiceID":"web", \
    "ServiceName":"web","ServiceTags":["rails"],"ServicePort":80}]
```

catalog 返回服务的所有相关节点。我们之后会在健康检测介绍有时候我们只想查询健康的节点，那么可以通过下面的命令。
```bash
$ curl 'http://localhost:8500/v1/health/service/web?passing'
[{"Node":"Armons-MacBook-Air","Address":"172.20.20.11","Service":{ \
    "ID":"web", "Service":"web", "Tags":["rails"],"Port":80}, "Checks": ...}]
```

## 更新服务
我们通过修改配置文件可以更新服务，更新完配置文件之后向 agent 发送 `SIGHUP` 信号。这么更新的好处是服务一直处于可用状态。

同事，HTTP API 也可以用来动态增加、删除和修改服务。

## 下一步
我们现在只配置了一个 agent，并且注册了服务。下面我们看一下如何建一个集群。