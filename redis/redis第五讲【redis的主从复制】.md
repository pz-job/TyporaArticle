##redis第五讲【redis的主从复制】

####什么是主从复制



####解决了什么问题

我们知道redis有两种持久化方式，但是他们都持久化在了同一台服务器上，所以当这台服务器损坏了之后，我们的数据应该怎么办?

你可能会想到，将备份数据定时转移到另一台物理主机就好了 

所以redis为我们提供了复制模式

其中定义了两个角色 一个是主机master，一个是从机slave ，他们之间能摩擦出那些火花呢

slave 无时无刻都想掏空master

- 当他们正常交配，emmm......不对（emmm.jpg），正常连接的时候
  - master会发送一连串的命令，来保持对slave 的更新，将自身的数据同步到slave

- 断开重连之后
  - slave会重新同步中间丢失的命令流

- 无法进行部分重新同步
  - 当无法重新同步的时候，会进行全量同步，master将所有的数据的快照发送给slave之后，还在数据更改时持续发送命令同步到slave



所以我们可以放心的把备份工作交给slave

####主从模式的使用 （主仆.jpg）

配置方法很简单

配置文件配置

命令配置

只需要在从机的配置文件配置

```shell
# 主机ip + 端口号
slaveof 192.168.1.1 6379
```



缺少代码



#### 主从模式的几种方式（新的姿势增加了.jpg）

- 一个主机多个从机（渣男！） 我们使用一台master，多台slave进行数据的复制备份
- 也可以多个slave之间相连接（备胎备中备！）



缺少宕机后行为



#### 复制的好处

- redis在master复制的时候是非阻塞的，可以一边同步一边处理客户端请求

- slave在复制的时候大部分也是非阻塞的，



#### 故障转移

当我们的主机master宕机之后，我们的slave会等待我们的master上线，再次期间不会进行任何操作，

这个时候我们可以手动的进行主机切换，将我们其中的一台从机slaver装换成我们的主机master，

当原来的master恢复重新上线的时候，会自动沦为从机slave

可是我们无法知道redis什么时候宕机，所以我们需要他们自动的进行主从切换，

这个时候需要我们的哨兵！从而实现系统高可用。



####哨兵模式

在哨兵模式下，我们需要奇数个哨兵，因为当主机宕机之后哨兵会去判断master是否宕机，在哨兵集群下进行投票，当投票数超过设定的值的时候会进行从机slave选举成为master，所以我们最少需要三台哨兵去监控我们的主机

