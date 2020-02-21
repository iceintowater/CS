# 锁  
## synchronized  
### 数据结构

#### markword
对象头中：  
markword: gc 标记 、 分代年龄 、 hashcode 、 线程id，锁标志

#### 线程获取锁的过程
偏向锁->轻量级锁->自旋锁->重锁

- 获取偏向锁的过程
1、线程看markword中锁标志是否是偏向状态，是的话进入下面步骤，再判断是否是自己线程id，是的话直接执行5，否则2
2、是偏向状态，但不是自己id,进行cas，若成功，则5，否则3  
3、说明有竞争，则持有锁的线程到达全局安全点（没有字节码执行）被挂起，将锁升级为轻量锁，撤销掉偏向锁。持有锁的线程继续执行
4、同步代码  

- 获取轻量级锁的过程
判断是否是无锁状态  
是的话在线程栈中创建lock record,复制markword到lock record  
cas 将markword中指针指向lock record，将lock record中的owner指针指向对象头   
成功的话，把锁变成轻量级锁，执行代码     
失败的话，看markword指针是否指向lockrecord,成功的话直接执行同步代码，同样也要升级下锁。还是不行的话，升级为重🔒，并开始尝试自旋了   

- 轻量级锁的释放
cas lock record 到 mark word
成功即可。若不成功，则说明有竞争，锁变成了重级别锁。

- 自旋  
循环看锁是否已经被消除，等待获得锁

- 重🔒
竞争列表、锁候选者列表、wait列表、Owner
获取monitor  
线程首先通过CAS尝试将monitor的owner设置为自己。  
若执行成功，则判断该线程是不是重入。若是重入，则执行recursions + 1,否则执行recursions = 1。  
若失败，则将自己封装为ObjectWaiter，并通过CAS加入到cxq中。  
释放monitor   
判断是否为重量级锁，是则继续流程。   
recursions - 1   
根据不同的策略设置一个OnDeckThread   


#reentranlock
## 获取锁  
获取状态的state的值，如果state=0即代表锁没有被其它线程占用(但是并不代表同步队列没有线程在等待)，执行步骤2。如果state!=0则代表锁
正在被其它线程占用，执行步骤3  
判断同步队列是否存在线程(节点)，如果不存在则直接将锁的所有者设置成当前线程，且更新状态state，然后返回true      
判断锁的所有者是不是当前线程，如果是则更新状态state的值，然后返回true，如果不是，那么返回false，即线程会被加入到同步队列中  

## 放弃锁   
判断当前线程是不是锁的所有者，如果是则进行步骤2，如果不是则抛出异常。   
判断此次释放锁后state的值是否为0，如果是则代表锁有没有重入，然后将锁的所有者设置成null且返回true，然后执行步骤3，如果不是则代表锁发生了重入执行步骤4。     
现在锁已经释放完，即state=0，唤醒同步队列中的后继节点进行锁的获取。    
锁还没有释放完，即state!=0，不唤醒同步队列。    




##ReentrantLock的等待/通知机制   
Synchronized + Object的wait和notify、notifyAll方法能实现等待/通知机制，那么ReentrantLock是否也能实现这样的等待/通知机制，答案是：可以。
ReentrantLock通过Condition对象，也就是条件队列实现了和wait、notify、notifyAll相同的语义。
线程执行condition.await()方法，将节点1从同步队列转移到条件队列中。





