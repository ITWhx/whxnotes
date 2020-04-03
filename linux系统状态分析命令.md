# linux系统状态分析命令

### TOP命令

| 指标        | 解释                                                         | 说明 |
| :---------- | :----------------------------------------------------------- | :--- |
| load averge | cpu负载                                                      |      |
| us          | 用户态计算                                                   |      |
| sy          | 系统态，偏高可能系统态加锁，网络调用                         |      |
| wa          | Io wait系统磁盘等待时间，一般在0.几，如果偏高，说明系统磁盘访问速度慢，等待磁盘io花了较多时间 |      |
| si          | 网络软中断                                                   |      |
| st          | 宿主机上的虚拟机占用的cpu资源                                |      |
|             |                                                              |      |

<img src="/Users/wuhaoxuan/Library/Application Support/typora-user-images/image-20200328103051438.png" alt="image-20200328103051438" style="zoom:50%;" />

### iotop

| Total disk read  | 磁盘总的读流量 |      |
| ---------------- | -------------- | ---- |
| total disk write | 磁盘总的写流量 |      |

![image-20200328103952993](/Users/wuhaoxuan/Library/Application Support/typora-user-images/image-20200328103952993.png)