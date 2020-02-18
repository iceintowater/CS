#pinpoint

## 场景
分布式性能监控工具

## 数据结构    traceid > transactionid >spanid = parentspanid
Pinpoint中，核心数据结构由Span, Trace, 和 TraceId组成。
Span: RPC (远程过程调用/remote procedure call)跟踪的基本单元; 当一个RPC调用到达时指示工作已经处理完成并包含跟踪数据。为了确保代码级别的可见性，Span拥有带SpanEvent标签的子结构作为数据结构。每个Span包含一个TraceId。这些信息由时间、⾏为、数据和信息传递⽅向等等。
Trace: 多个Span的集合; 由关联的RPC (Spans)组成. 在同一个trace中的span共享相同的TransactionId。Trace通过SpanId和ParentSpanId整理为继承树结构.
TraceId: 由 TransactionId, SpanId, 和 ParentSpanId 组成的key的集合. TransactionId 指明消息ID，而SpanId 和 ParentSpanId 表示RPC的父-子关系。 
TransactionId (TxId): 在分布式系统间单个事务发送/接收的消息的ID; 必须跨整个服务器集群做到全局唯一.
SpanId: 当收到RPC消息时处理的工作的ID; 在RPC请求到达节点时生成。
ParentSpanId (pSpanId): 发起RPC调用的父span的SpanId. 如果节点是事务的起点，这里将没有父span - 对于这种情况， 使用值-1来表示这个span是事务的根span。

## 架构
  1、Collector, 收集应用中agent发送的数据并存储到Hbase中
  2、Agent,  是和应用一起启动的和应用共享JVM，定时发送数据给Collector
  3、Web UI, 从hbase中读取数据并展示给用户，之后会有demo展示其功能

## 流程
1、当请求到达TomcatA时, Pinpoint agent 产生一个 TraceId.
TX_ID: TomcatA^TIME^1
SpanId: 10
ParentSpanId: -1(Root)
2、从spring MVC 控制器中记录数据
3、插入HttpClient.execute()方法的调用并在HTTPGet中配置TraceId
创建一个子TraceId
TX_ID: TomcatA^TIME^1 -> TomcatA^TIME^1
SPAN_ID: 10 -> 20
PARENT_SPAN_ID: -1 -> 10 (父 SpanId)
在HTTP header中配置子 TraceId
HttpGet.setHeader(PINPOINT_TX_ID, "TomcatA^TIME^1")
HttpGet.setHeader(PINPOINT_SPAN_ID, "20")
HttpGet.setHeader(PINPOINT_PARENT_SPAN_ID, "10")
4、传输打好tag的请求到TomcatB.
TomcatB 检查传输过来的请求的header
HttpServletRequest.getHeader(PINPOINT_TX_ID)
TomcatB 作为子节点工作因为它识别了header中的TraceId
TX_ID: TomcatA^TIME^1
SPAN_ID: 20
PARENT_SPAN_ID: 10
5、从spring mvc控制器中记录数据并完成请求
6、当从tomcatB回来的请求完成时，pinpoint agent发送跟踪数据到pinpoint collector就此存储在HBase中
7、在对tomcatB的HTTP调用结束后，TomcatA的请求也完成了。pinpoint agent发送跟踪数据到pinpoint collector就此存储在HBase中
8、UI从HBase中读取跟踪数据并通过排序树来创建调用栈
