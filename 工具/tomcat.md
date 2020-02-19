# tomcat
## 简介   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;是一个运行JAVA的网络服务器，底层是Socket的一个程序，它也是JSP和Serlvet的一个容器  
## 架构
Tomcat中最顶层的容器是Server，代表着整个服务器，一个Server可以包含至少一个Service，用于具体提供服务  
Service主要包含两个部分：Connector和Container。他们的作用如下：  
1、Connector用于处理连接相关的事情，并提供Socket与Request和Response相关的转化;  
2、Container用于封装和管理Servlet，以及具体处理Request请求；  

一个Tomcat中只有一个Server，一个Server可以包含多个Service，一个Service只有一个Container，
但是可以有多个Connectors，这是因为一个服务可以有多个连接，如同时提供Http和Https链接，也可以提供向相同协议不同端口的连接。

### connector架构
Connector就是使用ProtocolHandler来处理请求的
其中ProtocolHandler由包含了三个部件：Endpoint、Processor、Adapter。  

（1）Endpoint用来处理底层Socket的网络连接，Processor用于将Endpoint接收到的Socket封装成Request，
Adapter用于将Request交给Container进行具体的处理。  

（2）Endpoint由于是处理底层的Socket网络连接，因此Endpoint是用来实现TCP/IP协议的，
而Processor用来实现HTTP协议的，Adapter将请求适配到Servlet容器进行具体的处理。  

（3）Endpoint的抽象实现AbstractEndpoint里面定义的Acceptor和AsyncTimeout
两个内部类和一个Handler接口。Acceptor用于监听请求，AsyncTimeout用于检查异步Request的超时，
Handler用于处理接收到的Socket，在内部调用Processor进行处理。  

### contain架构  
Container用于封装和管理Servlet，以及具体处理Request请求  
4个子容器的作用分别是：  
（1）Engine：引擎，用来管理多个站点，一个Service最多只能有一个Engine；     
（2）Host：代表一个站点，也可以叫虚拟主机，通过配置Host就可以添加站点；      
（3）Context：代表一个应用程序，对应着平时开发的一套程序，或者一个WEB-INF目录以及下面的web.xml文件；      
（4）Wrapper：每一Wrapper封装着一个Servlet；     
