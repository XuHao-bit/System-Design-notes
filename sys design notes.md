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

    

