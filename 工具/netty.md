# netty

## 同步阻塞
调用系统read()
直到返回数据为止停止阻塞

## 同步非阻塞
不停调用系统read()；
直到数据到达系统内核区
进行read
从系统内核区拷贝数据

## 多路复用i/o
select 循环可读，直到找到数据写入到缓冲区
read 进行复制

## Reactor模型
buffer
select
channel

