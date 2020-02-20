
#  kafka  
削峰、异步、解耦  

##  结构：topic、part（对应一个log）、segment、mang message    

Partition的话第一个目标，并行读写      
Producer产生10个新消息，可以交给10个broker上的partition一起写；Consumer消费10个新消息，可以从10个broker上面一起读      
Kafka会给每个分区找一个节点当带头大哥（Leader），以及若干个节点当随从（Follower）   

##  过程
  发布者发到某个Topic的消息会被均匀地分布到多个Part上（随机或根据用户指定的回调函数进行分布），   
Broker收到发布消息后往对应Part的最后一个Segment上添加该消息，当某个Segment上的消息条数到达配置值或消息发布时间超过阈值时，   
Segment上的消息便会被flush到磁盘上，只有flush到磁盘上的消息订阅者才能订阅到，   
Segment达到一定的大小后将不会再往该Segment写数据，Broker会创建新的Segment。     

topic key -->partition     
offset-->segmengt    
indxe -> message    
 
## rabbitmq、rocketmq区别    
rabbitmq：基于erlang开发，并发能力很强，性能极好，延时很低      
rocketmq：MQ功能较为完善，还是分布式的，扩展性好           
kafka:吞吐量大(批量)，有序，集群（zookeeper,,热替换），消息事务         
