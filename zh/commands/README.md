# 4. Consul Commands(CLI)

Consul 提供了强大的 CLI 功能，并且非常规范，错误的情况下会返回一个非零的退出码。通过 `-h` 或者 `--help` 参数可以查看帮助信息。直接在终端中输入 `consul`，可以看到完整的命令列表。
```bash
$ consul
usage: consul [--version] [--help] <command> [<args>]

Available commands are:
    agent          Runs a Consul agent
    configtest     Validate config file
    event          Fire a new event
    exec           Executes a command on Consul nodes
    force-leave    Forces a member of the cluster to enter the "left" state
    info           Provides debugging information for operators
    join           Tell Consul agent to join cluster
    keygen         Generates a new encryption key
    keyring        Manages gossip layer encryption keys
    kv             Interact with the key-value store
    leave          Gracefully leaves the Consul cluster and shuts down
    lock           Execute a command holding a lock
    maint          Controls node or service maintenance mode
    members        Lists the members of a Consul cluster
    monitor        Stream logs from a Consul agent
    operator       Provides cluster-level tools for Consul operators
    reload         Triggers the agent to reload configuration files
    rtt            Estimates network round trip time between nodes
    version        Prints the Consul version
    watch          Watch for changes in Consul
```
如果想了解某个命令的帮助信息，只需要输入参数 `-h` 即可。比如：
```bash
$ consul join -h
Usage: consul join [options] address ...

  Tells a running Consul agent (with "consul agent") to join the cluster
  by specifying at least one existing member.

Options:

  -rpc-addr=127.0.0.1:8400  Address to the RPC server of the agent you want to contact
                            to send this command. If this isn't specified, the command checks the
                            CONSUL_RPC_ADDR env variable.
  -wan                      Joins a server to another server in the WAN pool
```
