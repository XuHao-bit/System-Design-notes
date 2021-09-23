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



