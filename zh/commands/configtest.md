# 4.2 configtest
命令 `consul configtest` 对 Consul 的配置文件作一些智能的测试。对于任一配置文件或者配置目录，configtest 命令会去尝试解析其内容。这个命令通常在启动 agent 前测试使用。

关于 Consul 的配置文件的格式，参考 [配置文件](../agent/configuration.md)。

## 用法
用法：`consul configtest [options]`

至少要提供 `-config-file` 或者 `-config-dir` 参数。如果配置合法，返回0，否则返回1。（注：终端命令的返回值可以通过命令 `echo $?` 查看。）具体 `option` 如下：
* `-config-file`: 配置文件路径，对于多个文件可以使用多次。
* `-config-dir`: 配置文件目录地址。目录下的所有 `.json` 文件都会被当做配置文件。
