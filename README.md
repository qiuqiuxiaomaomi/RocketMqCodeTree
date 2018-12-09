# RocketMqCodeTree
RocketMq核心源码研读


![](https://i.imgur.com/gYwOgOM.png)

Server端设计

![](https://i.imgur.com/8Auv4IT.png)

Client端设计

![](https://i.imgur.com/u6L67l3.png)

<pre>

</pre>

![](https://i.imgur.com/A9g57OT.png)

<pre>
Kafka与RocketMq 文件系统结构
      Kafka：
            1）在Kafka文件系统中，同一个Topic下有多个不同partion，每个partion创建一个
            目录。即topic下有分区的字母。
            2）每个partion相当于一个巨型文件，被平均分配到多个segment段文件中。
            3）每个partion只需要支持顺序读写就行了，segment文件生命周期由服务端配置参数
            决定。
            5）index为稀疏索引结构，并不存储每条记录的元数据信息。

            Kafka的高效文件特点：
                 1）一个大文件分成多个小文件段。
                 2）多个小文件段，容易定时清除或删除已经消费完文件，减少磁盘占用。
                 3）index, log全部映射到memory直接操作，使用零拷贝加页缓存技术，避免
                 segment file 被交换到磁盘增加I/O操作。
                 5）索引文件元数据存储用的是相对前一个segment file的offset存储，节省空间。
</pre>

![](https://i.imgur.com/f4Nu6CK.png)

<pre>
Kafka具体过程

           1）当建立连接请求时，首先客户端向kafka broker发送连接请求，broker中由Accept
           Thread线程接收并建立连接后，把client的socket以轮询的方式转交给相应的processor thread;
           2) 当Client向broker发送数据请求，由processor thread处理并接收client数据放
           到request缓冲区中，以待IO Thread进行逻辑处理和计算并把返回的result放到response 缓冲区中。
           3）接着唤醒processor thread, processor thread查询response队列，循环发送所有的response数据给client.
</pre>

![](https://i.imgur.com/G1UmVOr.png)

<pre>
如何根据offset查找数据
</pre>