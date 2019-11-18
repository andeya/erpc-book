# eRPC 使用手册

[eRPC](https://github.com/henrylee2cn/erpc) 是一个高效、可扩展且简单易用的 RPC 框架。

适用于 RPC、微服务、点对点长连接、IM 和游戏等领域。

![eRPC-Framework](https://github.com/henrylee2cn/erpc/raw/master/doc/erpc_module_diagram.png)


## 安装

```sh
go get -u -f github.com/henrylee2cn/erpc
```

```go
import "github.com/henrylee2cn/erpc/v6"
```

## 特性

- 使用 peer 为 server 和 client 提供相同的 API 封装
- 提供多层抽象，如：
  - peer
  - session/socket
  - router
  - handle/context
  - message
  - protocol
  - codec
  - transfer filter
  - plugin
- 支持平滑重启和关闭
- 兼容 HTTP 的消息格式：
  - 由 `Header` 和 `Body` 两部分组成
  - `Header` 包含与 HTTP header 格式相同的 metadata
  - `Body` 支持类似 Content Type 的自定义编解码器，已经实现的：
    - Protobuf
    - Thrift
    - JSON
    - XML
    - Form
    - Plain
  - 支持 push、call-reply 和更多的消息类型
- 支持自定义消息协议，并提供了一些常见实现：
  - `rawproto` - 默认的高性能二进制协议
  - `jsonproto` - JSON 消息协议
  - `pbproto` - Ptotobuf 消息协议
  - `thriftproto` - Thrift 消息协议
  - `httproto` - HTTP 消息协议
- 可优化的高性能传输层
  - 使用 Non-block socket 和 I/O 多路复用技术
  - 支持设置套接字 I/O 的缓冲区大小
  - 支持设置读取消息的大小（如果超过则断开连接）
  - 支持控制连接的文件描述符
- 支持多种网络类型：
  - `tcp`
  - `tcp4`
  - `tcp6`
  - `unix`
  - `unixpacket`
  - `quic`
  - 其他
    - websocket
    - evio
- 提供丰富的插件埋点，并已实现：
  - auth
  - binder
  - heartbeat
  - ignorecase(service method)
  - overloader
  - proxy(for unknown service method)
  - secure
- 强大灵活的日志系统：
  - 详细的日志信息，支持打印输入和输出详细信息
  - 支持设置慢操作警报阈值
  - 支持自定义实现日志组件
- 客户端会话支持在断开连接后自动重拨


## 性能测试

**自测**

- 一个服务端与一个客户端进程，在同一台机器上运行
- CPU:    Intel Xeon E312xx (Sandy Bridge) 16 cores 2.53GHz
- Memory: 16G
- OS:     Linux 2.6.32-696.16.1.el6.centos.plus.x86_64, CentOS 6.4
- Go:     1.9.2
- 信息大小: 581 bytes
- 信息编码：protobuf
- 发送 1000000 条信息

- erpc

| 并发client | 平均值(ms) | 中位数(ms) | 最大值(ms) | 最小值(ms) | 吞吐率(TPS) |
| -------- | ------- | ------- | ------- | ------- | -------- |
| 100      | 1       | 0       | 16      | 0       | 75505    |
| 500      | 9       | 11      | 97      | 0       | 52192    |
| 1000     | 19      | 24      | 187     | 0       | 50040    |
| 2000     | 39      | 54      | 409     | 0       | 42551    |
| 5000     | 96      | 128     | 1148    | 0       | 46367    |

- erpc/socket

| 并发client | 平均值(ms) | 中位数(ms) | 最大值(ms) | 最小值(ms) | 吞吐率(TPS) |
| -------- | ------- | ------- | ------- | ------- | -------- |
| 100      | 0       | 0       | 14      | 0       | 225682   |
| 500      | 2       | 1       | 24      | 0       | 212630   |
| 1000     | 4       | 3       | 51      | 0       | 180733   |
| 2000     | 8       | 6       | 64      | 0       | 183351   |
| 5000     | 21      | 18      | 651     | 0       | 133886   |

**对比测试**

<table>
<tr><th>Environment</th><th>Throughputs</th><th>Mean Latency</th><th>P99 Latency</th></tr>
<tr>
<td width="10%"><img src="https://github.com/henrylee2cn/rpc-benchmark/raw/master/result/env.png"></td>
<td width="30%"><img src="https://github.com/henrylee2cn/rpc-benchmark/raw/master/result/throughput.png"></td>
<td width="30%"><img src="https://github.com/henrylee2cn/rpc-benchmark/raw/master/result/mean_latency.png"></td>
<td width="30%"><img src="https://github.com/henrylee2cn/rpc-benchmark/raw/master/result/p99_latency.png"></td>
</tr>
</table>

**[More Detail](https://github.com/henrylee2cn/rpc-benchmark)**

- CPU耗时火焰图 erpc/socket

![erpc_socket_profile_torch](https://github.com/henrylee2cn/erpc/raw/master/doc/erpc_socket_profile_torch.png)

**[svg file](https://github.com/henrylee2cn/erpc/raw/master/doc/erpc_socket_profile_torch.svg)**

- 堆栈信息火焰图 erpc/socket

![erpc_socket_heap_torch](https://github.com/henrylee2cn/erpc/raw/master/doc/erpc_socket_heap_torch.png)

**[svg file](https://github.com/henrylee2cn/erpc/raw/master/doc/erpc_socket_heap_torch.svg)**


## 企业用户

<a href="http://www.xiaoenai.com"><img src="https://raw.githubusercontent.com/henrylee2cn/imgs-repo/master/xiaoenai.png" height="50" alt="深圳市梦之舵信息技术有限公司"/></a>
&nbsp;&nbsp;
<a href="https://tech.pingan.com/index.html"><img src="http://pa-tech.hirede.com/templates/pa-tech/Images/logo.png" height="50" alt="平安科技"/></a>
<br/>
<a href="http://www.fun.tv"><img src="http://static.funshion.com/open/static/img/logo.gif" height="70" alt="北京风行在线技术有限公司"/></a>
&nbsp;&nbsp;
<a href="http://www.kejishidai.cn"><img src="http://simg.ktvms.com/picture/logo.png" height="70" alt="北京可即时代网络公司"/></a>
<a href="https://www.kuaishou.com/"><img src="https://inews.gtimg.com/newsapp_bt/0/4400789257/1000" height="70" alt="快手短视频平台"/></a>
