# consul 指南

## 引言
consul 致力于为服务治理提供一整套的解决方案，这俩年在国内开始逐渐流行起来，可以说 consul 的流行主要有两个原因：
1. 微服务的兴起。微服务的兴起使得服务治理越来越重要。
2. DevOps 理念得到越来越多的人的认可。

关于 consul，遗憾的是中文资料非常少，所以就有了这本 gitbook。此书的内容始于官方文档，而不止于官方文档。我们的目的并不是翻译，而是要通过这本书让大家能够更好地理解和使用 consul.

## 鸣谢
感谢项目的发起人：陶克路、杨龙、王斌，和他们的付出。

感谢所有 contributor。

## 在线 gitbook
在线 gitbook 地址：consul-guide.github.io/consul-guide

# 目录

* [Consul 指南](README.md)
* [1. 新手指南](document/README.md)
    * [1.1 Consul 是什么？](document/whatisconsul.md)
    * [1.2 Consul vs. Other Software](document/comparison.md)
        * [1.2.1 ZooKeeper,doozerd,etcd](document/zookeeper.md)
        * [1.2.2 Chef,Puppet,etc](document/chef.md)
        * [1.2.3 Nagios,Sensu](document/nagios.md)
        * [1.2.4 SkyDNS](document/skyDNS.md)
        * [1.2.5 SmartStack](document/smartstack.md)
        * [1.2.6 Serf](document/serf.md)
        * [1.2.7 Custom Solutions](document/custom_solutions.md)
    * [1.3 Consul & Hashicorp 生态](document/hashicorp_ecosystem.md)
    * [1.4 入门指南](document/getting_started.md)
        * [1.4.1 Consul安装](document/install_consul.md)
        * [1.4.2 运行Agent](document/run_the_agent.md)
        * [1.4.3 服务](document/services.md)
        * [1.4.4 Consul集群](document/consul_cluster.md)
        * [1.4.5 健康检查](document/health_checks.md)
        * [1.4.6 K/V存储](document/key_value_data.md)
        * [1.4.7 Web UI](document/web_ui.md)
        * [1.4.8 下一步](document/next_steps.md)
* [2. Upgrading and Compatibility](upgrade/README.md)
    * [2.1 Upgrading Consul](upgrade/upgrading_consul.md)
    * [2.2 Compatibility Promise](upgrade/compatibility_promise.md)
    * [2.3 Specific Version Details](upgrade/specific_version.md)
* [3. Consul Internals](internal/README.md)
    * [3.1 Architecture](internal/architecture.md)
    * [3.2 Consensus Protocol](internal/consensus_protocal.md)
    * [3.3 Gossip Protocal](internal/gossip_protocal.md)
    * [3.4 Network Coordinates](internal/network_coordinates.md)
    * [3.5 Sessions](internal/sessions.md)
    * [3.6 ACLs](internal/acls.md)
    * [3.7 Anti-Entropy](internal/anti_entropy.md)
    * [3.8 Security Model](internal/security_model.md)
    * [3.9 Jepsen Testing](internal/jepsen_testing.md)
* [4. Consul Commands(CLI)](commands/README.md)
    * [4.1 agent](commands/agent.md)
    * [4.2 configtest](commands/configtest.md)
    * [4.3 event](commands/event.md)
    * [4.4 exec](commands/exec.md)
    * [4.5 force-leave](commands/force_leave.md)
    * [4.6 join](commands/join.md)
    * [4.7 keygen](commands/keygen.md)
    * [4.8 keyring](commands/keyring.md)
    * [4.9 kv](commands/kv.md)
    * [4.10 leave](commands/leave.md)
    * [4.11 lock](commands/lock.md)
    * [4.12 maint](commands/maint.md)
    * [4.13 members](commands/members.md)
    * [4.14 monitor](commands/monitor.md)
    * [4.15 operator](commands/operator.md)
    * [4.16 info](commands/info.md)
    * [4.17 reload](commands/reload.md)
    * [4.18 rtt](commands/rtt.md)
    * [4.19 snapshot](commands/snapshot.md)
    * [4.20 watch](commands/watch.md)
* [5. Consul Agent](agent/README.md)
    * [5.1 Running and Stopping](agent/running_and_stopping.md)
    * [5.2 DNS Interface](agent/dns_interface.md)
    * [5.3 HTTP API](agent/http_api.md)
    * [5.4 Configuration](agent/configuration.md)
    * [5.5 Service Definitions](agent/service_definitions.md)
    * [5.6 Check Definitions](agent/check_definitions.md)
    * [5.7 Encryption](agent/encryption.md)
    * [5.8 RPC Protocol](agent/rpc_protocol.md)
    * [5.9 Telemetry](agent/telemetry.md)
    * [5.10 Watches](agent/watches.md)
* [6. Guides](guides/README.md)
    * [6.1 Atlas Integration](guides/atlas_integration.md)
    * [6.2 Adding/Removing Servers](guides/add_remove_servers.md)
    * [6.3 Bootstrapping](guides/bootstrapping.md)
    * [6.4 DNS Forwarding](guides/dns_forwarding.md)
    * [6.5 External Services](guides/external_services.md)
    * [6.6 Leader Election](guides/leader_election.md)
    * [6.7 Multiple Datacenters](guides/multiple_datacenters.md)
    * [6.8 Outage Recovery](guides/outage_recovery.md)
    * [6.9 Semaphore](guides/semaphore.md)
    * [6.10 Server Performance](guides/server_performance.md)
* [7. Frequently Asked Questions](QA/README.md)

