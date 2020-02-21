# concurrenthashmap  

## jdk1.8结构  
1、ziseCtr：在多个方法中出现过这个变量，该变量主要是用来控制数组的初始化和扩容的，默认值为0，可以概括一下4种状态：
a、sizeCtr=0：默认值；  
b、sizeCtr=-1：表示Map正在初始化中；  
c、sizeCtr=-N：表示正在有N-1个线程进行扩容操作；  
d、sizeCtr>0: 未初始化则表示初始化Map的大小，已初始化则表示下次进行扩容操作的阈值；  
2、table：用于存储链表或红黑数的数组，初始值为null，在第一次进行put操作的时候进行初始化，默认值为16；  
3、nextTable：在扩容时新生成的数组，其大小为当前table的2倍，用于存放table转移过来的值；
4、Node：该类存储数据的核心，以key-value形式来存储；  
5、ForwardingNode：这是一个特殊Node节点，仅在进行扩容时用作占位符，表示当前位置已被移动或者为null，该node节点的hash值为-1；    
 
## jdk1.8操作    
### put  
1、初始化inittable，没有初始化   
2、hash没冲突，直接cas   
3、是否在扩容，在的话等扩容  
4、hash冲突的话，直接加synchronized   
5、添加完以后，看链是否需要扩容   

### get   
计算hash值，定位到该table索引位置，如果是首节点符合就返回   
如果遇到扩容的时候，会调用标志正在扩容节点ForwardingNode的find方法，查找该节点，匹配就返回    
以上都不符合的话，就往下遍历节点，匹配就返回，否则最后就返回null    

### 扩容  
维护一个索引，通过cas多线程来实现扩容。从inittable迁移到nexttable.
