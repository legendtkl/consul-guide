# 1.4.1 Consul 安装
对于 Consul 集群的每个节点都需要先安装 Consul。为了安装方便，官网提供了所有平台对于的[二进制安装包](https://www.consul.io/downloads.html)。所以这里我们就不再介绍怎么样通过源码编译安装了。（注：通过源码编译安装也很简单，下载源码，go build 即可。）

## 安装
首先从[下载页面](https://www.consul.io/downloads.html)找到对应的二进制安装包。安装包是一个压缩的 "zip" 文件。

下载完成之后，解压，然后把二进制文件 `consul` 拷贝到系统环境变量 `PATH` 对应的目录下即可，比如 Unix 系统下的 `/usr/local/bin`，Windows 系统上你需要手动将其路径添加到系统的 `%PATH%` 变量中。

## OS X
Mac 安装比较简单，如果你使用 `homebrew` 的话，可以直接用下面命令安装。
```bash
$ brew install consul
```

## 验证
那么怎么验证我们有没有安装成功呢？只要打开一个新的终端，键入 `consul`，系统如果提示如下，那么就是安装成功了。如果系统提示 `consul` 没有找到，那么你可能需要检查一下你的 `PATH` 变量。
```bash
$ consul
usage: consul [--version] [--help] <command> [<args>]

Available commands are:
    agent          Runs a Consul agent
    event          Fire a new event
    exec           Executes a command on Consul nodes
    force-leave    Forces a member of the cluster to enter the "left" state
    info           Provides debugging information for operators
    join           Tell Consul agent to join cluster
    keygen         Generates a new encryption key
    leave          Gracefully leaves the Consul cluster and shuts down
    members        Lists the members of a Consul cluster
    monitor        Stream logs from a Consul agent
    reload         Triggers the agent to reload configuration files
    version        Prints the Consul version
    watch          Watch for changes in Consul
```

## 下一步
Consul 已经安装成功，我们下面就[启动 agent 吧！](run_the_agent.md)