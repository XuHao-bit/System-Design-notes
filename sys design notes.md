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



#### Course 5. Message queue

- Notifier with: Load Balancing, Heart-Beat and DB Persistence --> **Message/Task Queue**
- ![message_queue_intro](./imgs/message_queue_intro.png)
  - 理解：server要处理的request被放入task queue里面，然后所有的task被均匀的分配给了所有的server，当某些server宕机之后，notifier通过心跳包检测到server的id，并在持久化的db中查到sid对应的未完成的task（request），然后负载均衡地将未完成的request再分配给其他的server。最终实现异步RPC。
- 举例：JMS(Java Message Service), ActiveMQ, RabbitMQ
- 使用场景：业务解耦/最终一致性/广播/错峰流控；如果需要强一致性，关注业务逻辑的处理结果，那么只需要RPC就好了。



#### Course 6. What is a micro-service architecture and it's advantages?

- monolithic vs. microservice

  - 单体应用程序：服务由多个模块所组成，将所有的模块打包成为一个包，作为一个进程运行。用户界面、数据访问层、数据部署层是紧密耦合的。【例：系统的各个模块可能访问同一个db，因此他们是协作的】

    - 【Advantages】Good for small team: 如果团队比较小，个人员都清楚系统的业务流程，那么没必要做成微服务，个人仅负责各个模块的开发即可。
    - Less Complex：考虑的东西少，比如不需要考虑怎么将系统划分为好几个部分，并且不需要考虑各个部分之间怎么通信，保证一致性等等。【个人理解】
    - Less Duplication：对于配置文件等等其他类型的文件，不需要复制到各个服务中，仅仅存在一份即可。
    - Faster，Procedure Calls：模块之间协作仅仅作为进程内的协作，不需要RPC等方式就可以通信。
    - 【Disadvantages】More context required：当新的组员加入时，他需要了解的东西太多了，需要将整个系统了解才可以知道他所需要做哪些工作（不然他不会理解）
    - Complicated Deployments：部署会比较复杂，当你检查到一个模块出现问题时，需要你重新打包整个项目并且再次部署。
    - Single Point of Failure！：单点出现问题之后，整个服务都需要重启。

  - 微服务：一组松散耦合的组件，可以一起工作并执行任务。【各个模块有自己的db】

    - 【Advantages】Scalability
    - Easier for new team members
    - Working in Parallel
    - Easier to reason about：比如一个聊天系统，可以很方便的知道哪个微服务是重要的，因此为那个服务扩展更多的机器以提高性能
    - 【Disadvantages】Hard to Design，Needs skilled Architects：比如你发现S1总是和S2进行交互，那么把他们合并成一个Service就可以

    

#### Course 7. What is Database Sharding?【分片】

- Horizontal Partitioning vs. Vertical Partitioning
  - ![db_partition](./imgs/db_partition.png)
  - 垂直拆分
    - 按照业务等等对数据库中的表（不一定是不同的表，同一表的不同列也可以）进行分组，相同类型放在新的同一个数据库中。
  - 水平拆分
    - 如果一个表过大，按照某种规则（比如某个值>threshold；hash值等）划分为多个组，将这些组存放在不同的数据库中。
  - ![db_horizontal_vertical](./imgs/db_horizontal_vertical.png)
  - 分库分表（也就是表层级的拆分和数据库层级的拆分）
  - 一般业务比较小采用分表即可，级别大可以采用分库；同时也要注意分库带来的问题，比如常见的分布式问题，分布式事务、高可用、数据一致等
- 水平拆分数据库实例--数据库分片
  - 【优势】
    - 帮助系统的水平扩展（horizontal scaling）：在水平扩展之后，将部分数据迁移到新的节点上
    - 加速查询响应时间
    - 降低宕机的影响：分片之后，宕机仅仅影响到对应片的数据库的系统
  - 【劣势】
    - 分片的数据库架构十分复杂：体现在两方面，i）分片过程可能导致数据丢失或者表损坏，风险很大；ii）正确使用分片也可能对团队工作流程产生重大的影响（虽说水平划分是按照某种规则来的，但是当某种业务不按照该规则进行访问数据时，分片的劣势就体现了）。
    - 分片容易导致不平衡，数据可能会往一个片的数据库倾斜，最终回答到负载最大，面临着再次分片的问题
    - 对数据库分片之后，很难将其恢复到未分片的架构
    - 并不是所有的数据库引擎都支持分片
  - **基于键的分片** Key Based Sharding
    - 【描述】将一个静态列（数据不随时间改变而改变）进行哈希之后，按照值写入不同的数据库中。
    - 【劣势】动态添加/删除服务器的时候事情会变的棘手，产生数据的重新映射以及迁移，可以采用一致hash的方法，尽量减少数据迁移量
    - 【优势】均匀分布数据
  - **基于范围的分片** Range Based Sharding
    - 【desc】比如按照某个商品的价格进行分片
    - 【ad】实现简单，写入读取也简单
    - 【disad】不能预防数据不均匀分布，比如50-100块钱的商品很多，而100,000块钱以上的商品很少
  - **基于目录的分片** Directory Based Sharding
    - 【desc】原始数据的某一列作为分片键，数据在哪个分区通过维护一个查找表来实现。也就是说，在查找数据时，现根据分片键查找到数据所在分区，再在该分区进行查找
    - ![db_direct_based_sharding](./imgs/db_direct_based_sharding.png)
    - 【优势】灵活性，对比范围分片只能指定键的范围，键的分片只能指定一种哈希函数，他们更改起来比较麻烦。
    - 【劣势】每次查找需要进行一次查表，性能下降；有单点故障，查找表损坏，会导致系统无法正常运行
- 本章节图片参考：https://zhuanlan.zhihu.com/p/57185574



#### Course 8 How Netflix onboards new context: Video Processing at scale

- Problem Description
- Video formats and resolutions
  - codec to compress the video, 高质量、中等质量、低质量。。。
  -  different resolutions，1080p、720p等等 
- Chunk processing  
  - 将视频划分为块
  - Scenes>Timestamp，将视频按场景分块，而非按时间：将视频按照4s分为一个shot，将多个shot组合成一个scene
  - Sparse & Dense Movie，对应着不同的请求策略，每次请求得到的视频数量的多少
- Storage
  - Amazon S3，只保存常量数据，不提供修改
- Open Connect for video caching
  - 建立了Open Connect Box来缓存某些视频，避免访问美国的服务器
- Summary

