# 1.4.5 健康检测
我们前面演示了如何运行 Consul，添加节点和服务，查询节点和服务，一切都很简单。这一节，我们将给节点以及服务添加健康检测。健康检测对于服务发现和服务高可用是非常重要的。

在说健康检测之前，你应该已经有了一个两个节点的集群了，也就是 1.4.4 节做的。

## 健康检测定义
和服务一样，检测也可以通过配置或者 HTTP API 注册。常用的方式也是通过配置来注册。

我们下面为节点2添加两个配置文件。
```bash
vagrant@n2:~$ echo '{"check": {"name": "ping",
  "script": "ping -c1 google.com >/dev/null", "interval": "30s"}}' \
  >/etc/consul.d/ping.json

vagrant@n2:~$ echo '{"service": {"name": "web", "tags": ["rails"], "port": 80,
  "check": {"script": "curl localhost >/dev/null 2>&1", "interval": "10s"}}}' \
  >/etc/consul.d/web.json
```
第一个配置是节点级的健康检测配置 "ping"。这个健康检测会每30秒执行一次，每次调用 `ping -c1 google.com`。基于脚本的健康检测会以运行 Consul 进程的用户来运行。如果脚本执行的退出码不是 0，那么这个节点就会标记为 unhealthy。任何基于脚本的健康检测都遵循这一标准。

第二行修改了 `web` 服务的配置，加了一个健康检测：每隔10秒通过 curl 去请求服务来检测 `web` 服务是否在运行。与上面的健康检测一样，脚本执行返回的状态码非 0，则将服务标记为 unhealthy。

现在向节点2发送 `SIGHUP` 信号来重启，你应该会看到下面的输出。
```bash
$ kill -s HUP process_id
...

==> Starting Consul agent...
...
    [INFO] agent: Synced service 'web'
    [INFO] agent: Synced check 'service:web'
    [INFO] agent: Synced check 'ping'
    [WARN] Check 'service:web' is now critical
```
前面几行表示 agent 已经同步了新的配置。下面几行表示对 `web` 服务的健康检测检测到服务不可用。因为我们本来就没有服务，所有导致 curl 失败。

## 检测健康状态
现在我们已经添加了一些简单的健康检测配置，我们可以使用 HTTP API 监测节点的健康状态了。首先，我们可以通过下面命令查询任何失败的健康检测（注意，这可以在集群中的任何一个节点上执行。注：这里不太严谨，更严谨一点应该是任何一个健康的节点）：
```bash
vagrant@n1:~$ curl http://localhost:8500/v1/health/state/critical
[{"Node":"agent-two","CheckID":"service:web","Name":"Service 'web' check","Status":"critical","Notes":"","ServiceID":"web","ServiceName":"web"}]
```
从输出结果可以看出，只有一个 `web` 服务的检测失败结果。

另外，这个使用我们通过 DNS 去查询 web 服务，Consul 不会返回任何结果，因为 `web` 服务健康检测失败，被认为是不可用的。
```bash
dig @127.0.0.1 -p 8600 web.service.consul
...

;; QUESTION SECTION:
;web.service.consul.        IN  A
```

## 下一步
这一节，我们演示了如何添加健康检测，如此简单。健康检测配置更新可以通过修改配置文件然后通过向 agent 发送 `SIGHUP` 信号来重启。同时，HTTP API 也可用用来添加、删除和修改来动态修改配置。API 还支持 "dead man's switch"，一种基于 TTL 的健康检测，我们再 Agent 那一章再细说。

下面，我们看看 Consul 的 K/V 存储。

