# consul nginx    
分布式服务发现与治理  

## consul节点类型  

服务端模式节点:  consul-server
- consul-server:把所有的信息持久化本地
- server-leader :leader server，负责把同步注册信息给其他server,同时负责各个节点的健康检测  

client模式节点:  
- consul-client:只接受注册，不进行存储

## 架构  
接入层：  
nginx:反向代理+nginx.conf  
consul-template：监听consul server上的变动，并且将consul信息写入nginx.conf

应用层：  
register ：监听应用程序，注册到consul client中
consul client : 提供注册应用程序地址功能；同时和consul leader进行同步。

## 与Eureka之间的区别   
consul    
一致性：   
强一致性，注册稍慢，需要在一半以上注册成功，才算成功     
leader挂掉，通过raft协议推选的机器之前不可用    
 其他方面： 属于应用之外的服务     
Eureka    
一致性：  
服务注册相对要快，因为不需要等注册信息replicate到其他节点，也不保证注册信息是否replicate成功   
当数据出现不一致时，虽然A, B上的注册信息不完全相同，但每个Eureka节点依然能够正常对外提供服务，
这会出现查询服务信息时如果请求A查不到，但请求B就能查到。如此保证了可用性但牺牲了一致性。   

 其他方面： 属于应用之内的servlet程序      
 
