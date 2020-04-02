##### raft协议保证

* 提交成功请求，数据一定不会丢
* 各个节点数据保持最终一致性

##### etcd特性

* 底层存储key是有序的
* 因为key有序，天然支持按目录结构高效遍历
* 支持复杂事务，提供类似if...then..else..的事务能力
* 给予租约机制实现key的TTL过期

![image-20200329121310871](/Users/wuhaoxuan/Library/Application Support/typora-user-images/image-20200329121310871.png)

##### 支持mvcc多版本控制协议

![image-20200329121457759](/Users/wuhaoxuan/Library/Application Support/typora-user-images/image-20200329121457759.png)





### 监听kv变化

![image-20200329121625084](/Users/wuhaoxuan/Library/Application Support/typora-user-images/image-20200329121625084.png)





### Watch机制工作原理

![image-20200329121828739](/Users/wuhaoxuan/Library/Application Support/typora-user-images/image-20200329121828739.png)





##### lease租约机制	

![image-20200329122009548](/Users/wuhaoxuan/Library/Application Support/typora-user-images/image-20200329122009548.png)