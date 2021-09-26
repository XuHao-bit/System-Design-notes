# System Design

[Course resources](https://www.youtube.com/playlist?list=PLMCXHnjXnTnvo6alSjVkgxV-VH6EPyvoX)

Course author: Gaurav Sen

Note By: xuhaobit@gmail.com



#### Course 1: System Design Basics: Horizontal vs. Vertical Scaling

- Scalability: 
  - 可扩展性，指的是Server进一步扩展的能力。通俗来讲，为了让Server获得更好的性能，往往有两种扩展的方式：i）买更多的机器，让这些机器作为Server，这样当多个请求过来时，server处理的效率更高了；ii）买一个更大的机器，这样server的处理能力、处理速度就更高了。
  - 通常将machine的数量变多叫做水平扩展性；将machine变大叫做垂直扩展性。

![horizontal_vs_vertical](./imgs/horizontal_vs_vertical.png)

- Horizontal vs. Vertical Scaling

|          | HORIZONTAL                                        | VERTICAL                         |
| -------- | ------------------------------------------------- | -------------------------------- |
| 负载要求 | Load Balancing Required                           | N/A                              |
| 出现错误 | Resilient(韧性的，一个machine出错，系统不会结束)  | Single point a failure           |
| 进程通信 | Network calls (RPC, Remote Process Communication) | IPC, Inter process Communication |
| 一致性   | DATA INCONSISTENCY                                | Consistent                       |
| 可扩展性 | SCALES WELL AS USERS INCREASE                     | Hardware limit                   |



#### Course 2. System Design Primer: How to start with distributed systems?

- think about to have a pizza shop
  - how to scaling?
    - 1【Vertical Scaling，可伸缩性】Optimize process  and increase throughput with the same resource.
    - 2 Preparing before hand at non-peak hours.
    - 3【Backups】keep a backup and avoid single point of failure.
    - 4【Horizontal Scaling】Hire more resources.
    - 5 Micro Service architecture
    - ![micro_service_arch](./imgs/micro_service_arch.png)
    - 6 Distributed System (Build a new system)
    - 7 Load Balance (Build a manager to send request to Server in a balanced way.)
    - 8 Decoupling 【解耦】
    - 9 Logging and Metrics calculation.
    - 10 Extensible 【可扩展性】指的是增加组件、扩充功能的能力



#### Course3.4. Consistent Hashing

Consistent Hashing helps to make Load Balancing

- 为什么要使用一致性哈希？
  - 在对request进行hash的时候，如果同一个用户得到的结果是一致的，那么他的请求就会发到同一个server，因而可以将用户信息存放在server的cache中；
  - 而如果访问到的不同的server，那么用户信息将不停的从cache换下去，访问信息的复杂度就提升了。
- 顺时针hash结构
  - ![hash_arch](./imgs/hash_arch.png)
  - 优点：如果server够多的话，每一个request分配到server的概率都是平均的1/N；
  - 缺点：如果server少的话，会出现负载不均衡的情况（增加、减少server，负载会不均衡）
- 改进结构
  - 每一个server使用多个hash function，这样的话，尽管server少，映射之后还是会变多；在server减少一个之后，在圈形结构中也是均匀的减少，这样尽可能最大的保持部分资源不变。
  - ![hash_arch_v2](./imgs/hash_arch_v2.png)
  - 经典饼图（最小改变）
- 使用场景？
  - 分布式系统的负载均衡问题
  - web缓存（同一用户经过一致哈希之后，映射到同一个服务器，这样可以保证缓存的高效命中）
  - 数据库