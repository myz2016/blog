

# RPC 框架

1. 创建工程、制定协议、通用工具方法
2. 实现序列化模块
3. 实现网络模块
   1. 根据 rpc 的通讯场景，对网络层做一层抽象
4. 实现 server 模块
   1. 暴露服务，对服务做管理
5. 实现 client 模块
   1. 动态代理，代理对象内部通过网络通信，与 server 进行交互
6. gk-rpc 使用案例

## 模块

* 协议模块
  * 描述 server 与 client 之间通信的协议
  * ServiceDescriptor：服务描述信息
  * 两个核心类
    * request：请求 server 的服务，还有携带的参数
    * response：server 响应给 client 的信息，成功失败等
* server 模块
  * RpcServer：
  * ServiceManager：维护 RpcServer 暴露出的服务
  * ServiceInstance：暴露出去的具体对象和实现，暴露的服务注册在 serviceManager 中
* client 模块
  * RpcClient：
  * RemoteInvoker：与 RpcServer 交互，交互的媒介就是 Request 与 Response
  * TransportSelector：client 与 server 之间可建立多个连接，也可以一个 client 连接不同的 server，所以把网络连接做了抽象，也就是 TransportSelector
* 序列化模块
  * 对对象2进制进行互转
* 网络模块
  * TransportServer
  * TransportClient
  * 基于 http 做出实现